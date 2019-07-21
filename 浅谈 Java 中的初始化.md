# Java 内存分布
![Java 内存分布](https://img-blog.csdnimg.cn/20190630100143600.png)
需要注意的是，**方法中的参数属于局部变量** ，类似于 `String str="字符串"` 这样定义的字符串是存放在堆内存中的「字符串常量池」（常量池中不会添加已有成员）中。

而 `String str1 = new String()` 内存是直接位于堆中，每一次对象的实例化都会在堆中开辟新的内存空间。
# 成员变量与局部变量的区别
![成员变量与局部](https://img-blog.csdnimg.cn/2019063010122170.png)
结合代码分析

```java
class Person {
    private String name;
    private int age;

    public Person(){ }
    public Person(String name, int age) { // 两个参数属于局部变量，调用完成后被销毁，且只能在该方法内部访问
        this.name = name;
        this.age = age;
    }

    public void showInfo(){ // 类内部访问成员变量
        System.out.println("name =" + name);
        System.out.println("age = "+age);
    }
}
public class TestDemo {
    public static void main(String[] args) {
        Person p1 = new Person();
        p1.showInfo();
        Person p2 = new Person("hack-hu",18);
        p2.showInfo();
    }
}
```
输出结果：

```java
// p1 输出缺省值，两个成员变量都被自动初始化
name =null
age = 0
// p2 输出赋值后结果，重载的构造方法在调用完成后销毁
name =hack-hu
age = 18
```
提示：虽然成员变量会自动完成初始化，但是在定义时或使用前先初始化是个好习惯。
# 类中数据的初始化顺序
原则：

 - 1、类构造函数调用时，先初始化类中的成员变量，**所有成员变量初始化完成后**，再执行构造函数代码。
   
  -  2、父类构造函数完成再执行子类构造函数。
   
  - 3、静态（static）成员初始化后再初始化非静态成员
   
  - 4、同一优先级，按照代码的先后顺序执行。

代码实践

定义父类 Person 
```java
public class Person {
    private String name;
    private int age;

    static {
        System.out.println("父类静态块代码执行");
    }

    public Person(){
        System.out.println("父类构造函数执行");
    }
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void showInfo(){
        System.out.println("name =" + name);
        System.out.println("age = "+age);
    }
}
```
定义其旁类 Nationality （国籍）

```java
public class Nationality {
    static {
        System.out.println("旁类静态块执行");
    }
    public Nationality(){
        System.out.println("旁类构造方法执行");
    }
}
```
定义 Person 子类 Student

```java
public class Student extends Person {
    static Nationality nation= new Nationality();
    static {
        System.out.println("子类静态块执行");
    }
    public Student(){
        System.out.println("子类构造函数执行");
    }
}
```
输出结果：

```java
父类静态块代码执行
旁类静态块执行
旁类构造方法执行
子类静态块执行
父类构造函数执行
子类构造函数执行
```
注意，子类中的静态对象 nation，要比父类的构造函数先执行，说明所有的静态成员都要先初始化。当 nation 不为静态时，则先完成父类构造，再初始化子类非静态成员。

总结：实践出真知，对于含糊不清的概念，可以实践一些案例来参照理解。若文章中有错误欢迎指正，欢迎你给出评论，或是默默点点赞给个好看，这都是对我的一种认可。
