# RocketMQ延迟消费

## CommitLog

在**org.apache.rocketmq.store.CommitLog**中，针对延迟消息做了一些处理：

```java
// 延迟级别大于0，就是延时消息
if (msg.getDelayTimeLevel() > 0) {
    // 判断当前延迟级别，如果大于最大延迟级别，
    // 就设置当前延迟级别为最大延迟级别。
    if (msg.getDelayTimeLevel() > this.defaultMessageStore
            .getScheduleMessageService().getMaxDelayLevel()) {
        msg.setDelayTimeLevel(this.defaultMessageStore
                .getScheduleMessageService().getMaxDelayLevel());
    }

    // 获取延迟消息的主题，
    // 其中RMQ_SYS_SCHEDULE_TOPIC的值为SCHEDULE_TOPIC_XXXX
    topic = TopicValidator.RMQ_SYS_SCHEDULE_TOPIC;
    // 根据延迟级别获取延迟消息的队列Id，
    // 队列Id其实就是延迟级别减1
    queueId = ScheduleMessageService.delayLevel2QueueId(msg.getDelayTimeLevel());

    // 备份真正的主题和队列Id
    MessageAccessor.putProperty(msg
            , MessageConst.PROPERTY_REAL_TOPIC, msg.getTopic());
    MessageAccessor.putProperty(msg
            , MessageConst.PROPERTY_REAL_QUEUE_ID, String.valueOf(msg.getQueueId()));
    msg.setPropertiesString(MessageDecoder.messageProperties2String(msg.getProperties()));

    // 设置延时消息的主题和队列Id
    msg.setTopic(topic);
    msg.setQueueId(queueId);
}
```

可以看到，每一个延迟消息的主题都被暂时更改为**SCHEDULE_TOPIC_XXXX**，并且根据延迟级别延迟消息变更了新的队列Id。接下来，处理延迟消息的就是**org.apache.rocketmq.store.schedule.ScheduleMessageService**。

#### ScheduleMessageService

ScheduleMessageService是由**org.apache.rocketmq.store.DefaultMessageStore**进行初始化的，初始化包括构造对象和调用`load`方法。最后，再执行ScheduleMessageService的`start`方法：

```java
public void start() {
    // 使用AtomicBoolean确保start方法仅有效执行一次
    if (started.compareAndSet(false, true)) {
        this.timer = new Timer("ScheduleMessageTimerThread", true);
        // 遍历所有延迟级别
        for (Map.Entry<Integer, Long> entry : this.delayLevelTable.entrySet()) {
            // key为延迟级别
            Integer level = entry.getKey();
            // value为延迟级别对应的毫秒数
            Long timeDelay = entry.getValue();
            // 根据延迟级别获得对应队列的偏移量
            Long offset = this.offsetTable.get(level);
            // 如果偏移量为null，则设置为0
            if (null == offset) {
                offset = 0L;
            }

            if (timeDelay != null) {
                // 为每个延迟级别创建定时任务，
                // 第一次启动任务延迟为FIRST_DELAY_TIME，也就是1秒
                this.timer.schedule(
                        new DeliverDelayedMessageTimerTask(level, offset), FIRST_DELAY_TIME);
            }
        }

        // 延迟10秒后每隔flushDelayOffsetInterval执行一次任务，
        // 其中，flushDelayOffsetInterval默认配置也为10秒
        this.timer.scheduleAtFixedRate(new TimerTask() {

            @Override
            public void run() {
                try {
                    // 持久化每个队列消费的偏移量
                    if (started.get()) ScheduleMessageService.this.persist();
                } catch (Throwable e) {
                    log.error("scheduleAtFixedRate flush exception", e);
                }
            }
        }, 10000, this.defaultMessageStore
            .getMessageStoreConfig().getFlushDelayOffsetInterval());
    }
}
```

遍历所有延迟级别，根据延迟级别获得对应队列的偏移量，如果偏移量不存在，则设置为0。然后为每个延迟级别创建定时任务，第一次启动任务延迟为1秒，第二次及以后的启动任务延迟才是延迟级别相应的延迟时间。

然后，又创建了一个定时任务，用于持久化每个队列消费的偏移量。持久化的频率由**flushDelayOffsetInterval**属性进行配置，默认为10秒。

#### 定时任务

ScheduleMessageService的`start`方法执行之后，每个延迟级别都创建自己的定时任务，这里的定时任务的具体实现就在**DeliverDelayedMessageTimerTask**类之中，它核心代码是**executeOnTimeup**方法之中，我们来看一下主要部分：

```java
// 根据主题和队列Id获取消息队列
ConsumeQueue cq =
        ScheduleMessageService.this.defaultMessageStore.findConsumeQueue(
                TopicValidator.RMQ_SYS_SCHEDULE_TOPIC
                , delayLevel2QueueId(delayLevel));
```

如果没有获取到对应的消息队列，则在**DELAY_FOR_A_WHILE**（默认为100）毫秒后再执行任务。如果获取到了，就继续执行下面操作：

```java
// 根据消费偏移量从消息队列中获取所有有效消息
SelectMappedBufferResult bufferCQ = cq.getIndexBuffer(this.offset);
```

如果没有获取到有效消息，则在**DELAY_FOR_A_WHILE**（默认为100）毫秒后再执行任务。如果获取到了，就继续执行下面操作：

```java
// 遍历所有消息
for (; i < bufferCQ.getSize(); i += ConsumeQueue.CQ_STORE_UNIT_SIZE) {
    // 获取消息的物理偏移量
    long offsetPy = bufferCQ.getByteBuffer().getLong();
    // 获取消息的物理长度
    int sizePy = bufferCQ.getByteBuffer().getInt();
    long tagsCode = bufferCQ.getByteBuffer().getLong();
    
    // 省略部分代码...

    long now = System.currentTimeMillis();
    // 计算消息应该被消费的时间
    long deliverTimestamp = this.correctDeliverTimestamp(now, tagsCode);
    // 计算下一条消息的偏移量
    nextOffset = offset + (i / ConsumeQueue.CQ_STORE_UNIT_SIZE)

    long countdown = deliverTimestamp - now;
    // 省略部分代码...
}
```

如果当前消息不到消费的时间，则在`countdown`毫秒后再执行任务。如果到消费的时间，就继续执行下面操作：

```java
// 根据消息的物理偏移量和大小获取消息
MessageExt msgExt =
    ScheduleMessageService.this.defaultMessageStore.lookMessageByOffset(
            offsetPy, sizePy);
```

如果获取到消息，则继续执行下面操作：

```java
// 重新构建新的消息，包括：
// 1.清除消息的延迟级别
// 2.恢复真正的消息主题和队列Id
MessageExtBrokerInner msgInner = this.messageTimeup(msgExt);
if (TopicValidator.RMQ_SYS_TRANS_HALF_TOPIC.equals(msgInner.getTopic())) {
    log.error("[BUG] the real topic of schedule msg is {},"
                    + " discard the msg. msg={}",
            msgInner.getTopic(), msgInner);
    continue;
}
// 重新把消息发送到真正的消息队列上
PutMessageResult putMessageResult =
        ScheduleMessageService.this.writeMessageStore
                .putMessage(msgInner);
```

清除了消息的延迟级别，并且恢复了真正的消息主题和队列Id，重新把消息发送到真正的消息队列上以后，消费者就可以立即消费了。

#### 总结

经过以上对源码的分析，可以总结出延迟消息的实现步骤：

1. 如果消息的延迟级别大于0，则表示该消息为延迟消息，修改该消息的主题为**SCHEDULE_TOPIC_XXXX**，队列Id为延迟级别减1。
2. 消息进入**SCHEDULE_TOPIC_XXXX**的队列中。
3. 定时任务根据上次拉取的偏移量不断从队列中取出所有消息。
4. 根据消息的物理偏移量和大小再次获取消息。
5. 根据消息属性重新创建消息，清除延迟级别，恢复原主题和队列Id。
6. 重新发送消息到原主题的队列中，供消费者进行消费。
