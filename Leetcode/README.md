# 刷题TIPS

## 数组：
初始化：
```java
// 初始化一个大小为10，默认值为0的数组
int[] nums = new int[10];

// 初始化一个二位boolean数组
boolean[][] visited = new boolean[5][10];
```
常用方法：
```java
// 函数开头一般要做一个非空检查，然后用索引下标访问元素
if (nums.length == 0) {
    return;
}

for (int i = 0; i < nums.length; i++) {
    // 访问num[i]
}
```
## 字符串
初始化：
```java
String s = "hello world!";
```
访问字符串：
```java
char str = s.charAt(index);
```
修改字符串：
```java
// String不支持直接修改字符串，要转化为char[]类型才能修改
char[] chars = s1.toCharArray();
chars[1] = 'a';
String s2 = new String(chars);
```
判断字符串是否相同：
```java
// 一定要用equals方法进行判断，不能直接用==
if (s1.equals(s2)) {
    // 相等
} else {
    // 不相等
}
```
通过 STRINGBUILDER 进行频繁的字符串拼接以提高效率
```java
StringBuilder sb = new StringBuilder();

for (char c = 'a'; c <= 'f'; c++) {
    // append方法支持拼接字符、字符串、数字等类型
    sb.append(c);
    String result = sb.toString();
}
```
## 动态数组ArrayList:
```java
List<Interger> list = new ArrayList<Interger>();
ArrayList<Interger> list = new ArrayList<Interger>();
```
常用方法：
```java
// 判断是否为空
boolean isEmpty()

// 返回元素个数
int size()

// 访问索引元素
E get(int index)

// 在尾部添加元素
boolean add(E e)
```
## 双链表 LinkedList
初始化：
```java
LinkedList<String> strings = new LinkedList<>();
```
常用方法：
```java
// 判断是否为空
boolean isEmpty()

// 返回元素个数
int size()

// 在尾部添加元素
boolean add(E e)

// 删除尾部最后一个元素
E removeLast()

// 在头部添加元素
void addFirst(E e)

// 删除头部第一个元素
E removeFirst()
```

## 哈希 HashMap:
```java
Map<Interger,String> map = new HashMap<int,String>();
// 初始化一个字符串映射到数组的哈希表
HashMap<String, int[]> map = new HashMap<>();
```
常用方法：
```java
// 判断是否存在Key
boolean containsKey(Object key)
boolean containsValue(Object Value)

// 获取Key的对应Value，如果不存在则返回null
V get(Object key)

// 将Key和Value存入哈希表
V put(K key, V value)

// 获取Key的对应Value，如果不存在则返回null
V getOrDefault(Object key, V defaultValue)
        

// 将Key和Value存入哈希表，如果存在，则什么都不做
V putIfAbsent(K key, V value)

// 删除键值对并返回值
V remove(Object key)

// 获取哈希表中所有Key
Set<K> keySet()
```
## HashSet
```java
Set<String> set = new HashSet<String>();
```

## Stack
```java
LinkedStack<String> stack = new LinkedStack<>();
Stack<Interger> stack = new Stack<Interger>();
```
常用方法：
```java
// 判断是否为空
boolean isEmpty()

// 返回元素个数
int size()

// 将元素压入栈顶
E push(E e)

// 返回栈顶元素
E peek()

// 删除并的返回栈顶元素
E pop()
```

Queue
```java
Queue<String> queue = new LinkedList<String>();
```
常用方法：
```java
  //如果插入失败，会直接抛出异常
    boolean add(E e);

    //同样是添加操作，但是插入失败不会抛出异常
    boolean offer(E e);

    //移除队首元素，但是如果队列已经为空，那么会抛出异常
    E remove();

       //同样是移除队首元素，但是如果队列为空，会返回null
    E poll();

    //仅获取队首元素，不进行出队操作，但是如果队列已经为空，那么会抛出异常
    E element();

    //同样是仅获取队首元素，但是如果队列为空，会返回null
    E peek();
```
Deque 双端队列
```java
Deque<String> deque = new LinkedList<>();
Deque<String> deque = new ArrayDeque<>();   //数组实现的栈和队列
```
常用方法：
```java
    void addFirst(E e);
    void addLast(E e);
        
    boolean offerFirst(E e);
    boolean offerLast(E e);

    E removeFirst();
    E removeLast();

    E pollFirst();
    E pollLast();

    E getFirst();
    E getLast();

    E peekFirst();
    E peekLast();

```
优先队列：
```java
Queue<Integer> queue = new PriorityQueue<>();
```

常用方法：
```java
// 判断是否为空
boolean isEmpty()

// 返回元素个数
int size()

// 返回队头元素
E peek()

// 删除并返回队头元素
E poll()

// 在队尾插入元素
boolean offer(E e)
```

树节点：
```java
class TreeNode {
    int value;
    TreeNode left;
    TreeNode right;
    public TreeNode () {};
    public TreeNode(int value){
        this.value = value;
        this.left = null;
        this.right = null;
    }
}
```

链表节点：
```java
class Node {
    int value;
    Node next;
    public Node() {};
    public Node(int value){
        this.value = value;
        this.next = null;
    }
}
```

