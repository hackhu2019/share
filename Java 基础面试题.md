这短时间在刷「牛客网」的 Java 基础面试题，挑一些里面比较有代表性的题目，和大家分享下我的理解。

# 类型一：初始化
**what**：类内部初始化顺序，静态成员、非静态成员、构造器方法是以怎样的顺序完成初始化的？

**how？** 以下程序会有怎样的输出结果
```java
public class Animal {
    static {
        System.out.println("静态代码块执行");
    }
    Other other  = new Other("非静态");
    static Other staticOther = new Other("静态");
    {
        System.out.println("非静态代码块执行");
    }
    public void sayHi() {
        System.out.println("你好，我是 Animal 类");
    }
    public Animal() {
        System.out.println("兽类被初始化");
    }
	public static void main(String[] args) {
        Animal animal = new Animal();
    }
}
```
输出：
```java
静态代码块执行
静态Other 类初始化
非静态Other 类初始化
非静态代码块执行
兽类被初始化
```

在同一类中，先初始静态成员（包括静态代码块）——> 非静态成员 ——> 执行构造器内部操作。同一级别中，按照代码先后顺序执行。

**why？**
1. **静态成员不属于任意实例，而是属于类**。在我们声明类对象时，静态成员就会执行初始化。Animal  类在加载至内存时，就会自动初始化静态成员，不需要任何实例（new 构造器）。
2. 成员优先于构造器内部操作，先初始化（基本类型有对应的默认值，引用类型初始化为 null）。因为构造器（构造函数），可以访问成员变量，在构造器可能会对成员变量进行某些操作，而未赋值的变量编译器是不允许使用的（即编译不通过）。但是编译器的隐式初始化未必是我们想要的值，所以建议我们在定义时初始化或在使用前初始化。
3. 同一级别中，按照代码先后顺序执行。Java 程序经过编译器编译成字节码，而字节码经过虚拟机解释器转化为机器码，最终变成一条条指令，而计算机执行指令通常是逐行执行的（特殊情况：跳转指令）。

**what**：继承关系，子类的初始化和父类的初始化有关吗？

**how？** 以下程序会有怎样的输出结果
```java
public class Animal {
    public Animal() {
        System.out.println("兽类被初始化");
    }
    public static void main(String[] args) {
        Golden golden = new Golden();
    }
}
class Dog extends Animal {
    public Dog() {
        System.out.println("Dog 类被初始化");
    }
}

class Golden extends Dog{
    public Golden(){
        System.out.println("Golden 类被初始化");
    }
}
```
执行结果：
```java
兽类被初始化
Dog 类被初始化
Golden 类被初始化
```
在调用子类构造器创建子类实例时，父类无参构造器会先执行。

**why?**

子类继承了父类所有成员和方法（包括 private 成员），在子类构造器中可以通过 super 显式调用父类的成员和方法。为确保子类在使用时，父类成员可用，要先调用父类构造器，完成父类初始化。

**扩展**

在子类构造器中会隐式调用父类无参构造器，这是我们未意识到父类构造器的原因（编译器简化我们的代码）。所以，**当父类不存在无参构造器时，我们需要显式调用父类构造器，且必须为子类构造器第一条执行语句。**

```java
class Dog extends Animal {
    String name;
    public Dog(String name) {
        this.name = name;
        System.out.println("Dog 类被初始化");
    }
}

class Golden extends Dog{
    public Golden(){
        super("金毛");
        System.out.println("Golden 类被初始化");
    }
}
```
# 类型二：封装与多态的结合
**what**: 实例化父类对象却调用子类的构造函数，那执行方法时，调用的是父类方法，还是子类方法，那方法中所调用的成员呢？

**how**：
```java
class Test1 {
    private int id = 12;

    public void printId() {
        System.out.println(idAdd());
    }

    public int idAdd(){
        return id+1;
    }
}

public class Test2 extends Test1 {
    private int id = 1;

    public static void main(String[] args) {
        Test1 test = new Test2();
        test.printId();
    }
}
```
输出：13

**why？**

Java 中，**调用谁的构造方法，就访问谁的作用域**。但是，**父类的方法只能访问父类成员**。当** 子类重写父类方法时，子类作用域调用的就是子类的方法**。

修改 Test2 代码

```java
public class Test2 extends Test1 {
    private int id = 1;

    public int idAdd(){
        return id+1;
    }
    public static void main(String[] args) {
        Test1 test = new Test2();
        test.printId();
    }
}
```
此时的输出的答案就是 2。

此时父类 `public void printId()`  调用的是子类重写的 idAdd()，而子类的 idAdd() 访问的 id 是其本身的成员变量。由于子类方法的重写，二者的答案截然不同，这就是「多态」的一种体现。

# 类型三：值传递还是引用传递？
**what**：参数传递，会改变原有变量的值或引用吗？

**how？** 以下程序会有怎样的输出结果
```java
public void update(String str,char[] chars){
        str = "新字符串";
        chars[0] = '0';
    }
    public static void main(String[] args) {
        Test test = new Test();
        String str = "字符串";
        char[] chars = {'1','2','3'};
        test.update(str, chars);
        System.out.println("str:" + str);
        System.out.println("chars:" + Arrays.toString(chars));
    }
```
输出结果：

```java
str:字符串
chars:[0, 2, 3]
```
String 的引用未改变，但是 chars[0] 的值被改变

**why？**

Java 中所有的参数都是「值传递」，简单说就是「拷贝」（复制）。对于基本类型，就是复制其值，引用类型则复制其引用。

方法中的 `str = "新字符串";` 实际上是将传入的引用地址改变，但是原有的 str 引用是没有发生改变的，而 chars 虽然也是复制引用，但是通过下标访问到到的是 chars[0] 实际内存地址，实际修改了它的值。

为了方便理解我将代码改造，并做了标注，方便理解。

![引用传递](https://img-blog.csdnimg.cn/2019080418533279.png)
类似于成语「刻舟求剑」所讲述的故事，虽然在舟上做了丢剑处的标记，但是舟下的那片江却不是丢剑的江了。

三道典型的 Java 基础面试讲完了，没准你有什么不一样的看法或者技巧，欢迎你给出评论，或是默默点点赞给个好看，这都是对我的一种认可。
