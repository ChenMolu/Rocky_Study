# Maven依赖版本冲突问题

解决方式：

1. 使用exclusions注解排除掉冲突的依赖

```xml
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-api</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```



2. 在pom文件中修改依赖的前后顺序

