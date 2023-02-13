# Java 创建对象的四种方式

首先创建一个类，方便演示之后集中创建不同对象的方式。

```java
public class Student implements Serializable,Cloneable{
    
    public Student(){
        super();
    }
    
    @Override
    protected Student clone() throws CloneNotSupportedException {
        return (Student) super.clone();
    }
}

```

## 1. new 关键字创建对象

```java
Student student = new Student();
```

## 2. 利用反射机制创建对象

默认构造器构造方法

```java
Student student = Student.class.newInstance();
```

构造器创建

```java
//默认空参构造器构造
Student student = Student.class.getConstructor().newInstance();
//指定构造器构造
Student student1=Student.class.getConstructor(int.class).newInstance(1);
```

## 3. 通过已有对象克隆创建

注意是使用`深拷贝`方式对已有对象进行克隆，从而创建新的对象

```java
Student student = new Student();
Student clone = student.clone();
```

## 4. 通过序列化和反序列化进行创建

实际上就是将创建好的对象通过序列化写在流里持久化在本地，然根据需要将流读取出来重新构造一个相同的新的对象。

```java
Student student = (Student) new ObjectInputStream(new
                             FileInputStream("file.txt")).readObject();
```

注意：所有要实现序列化的类都必须实现Serializable接口