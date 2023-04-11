# final 关键字

final 可以用来修饰：类、方法、变量

1. final用来修饰一个类：修饰类之后不可以继承

String类、System类、StringBuffer类等都是被final修饰的类

2. final用来修饰方法：表明方法不可以再进行重写

3. final修饰一个变量：变量就变成常量

* final修饰属性：

  * 可以考虑赋值的位置有：

    * 显式初始化，

    ```java
    final int num  = 10;
    ```

    * 代码块中初始化

    ```java
    final int num;
    
    {
      num = 10;
    }
    ```

    * 构造器中初始化

    ```java
    final int num;
    
    public Test() {
      num = 10;
    }
    ```

* Final 修饰局部变量

  ```java
  public void show(){
    final int NUM = 10; //常量，不可以再修改
  }
  ```

  ```java
  public void show(final int num){
    //在函数内不可以再进行操作修改
  }
  ```

**static final 全局常量**







