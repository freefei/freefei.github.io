---
layout: post
title: Java中的constructor
category: JAVA
date: 2015-07-01
duoshuo: true
tags : [面试]
---

构造方法和实例方法的区别：
一、主要的区别在于三个方面：修饰符、返回值、命名
1、和实例方法一样，构造器可以有任何访问的修饰符，public、private、protected或者没有修饰符   ，都可以对构造方法进行修饰。不同于实例方法的是构造方法不能有任何非访问性质的修饰符修饰，例如static、final、synchronized、abstract等都不能修饰构造方法。
解释：构造方法用于初始化一个实例对象，所以static修饰是没有任何意义的;多个线程不会同时创建内存地址相同的同一个对象，所以synchronized修饰没有意义;
构造方法不能被子类继承，所以final和abstract修饰没有意义。
2、返回类型是非常重要的，实例方法可以返回任何类型的值或者是无返回值（void）,而构造方法是没有返回类型的，void也不行。
3、至于命名就是构造方法与类名相同，当然了实例方法也可以与类名相同,但是习惯上我们为实例方法命名的时候通常是小写的,另一方面也是与构造方法区分开。
而构造方法与类名相同,所以首字母一般大写。
下面看几个例子熟悉一下：

{% highlight java %}
public class Sample {  
      
    private int x;  
  
    public Sample() { // 不带参数的构造方法  
        this(1);  
    }  
      
    public Sample(int x) { //带参数的构造方法  
        this.x=x;  
    }  
      
    public int Sample(int x) { //不是构造方法  
        return x++;  
    }  
  
}  
{% endhighlight %}

上面的例子即使不通过注释我们也很容易能区分开的，再看下面一个例子

{% highlight java %}
public class Mystery {  
    private String s;  
      
    public void Mystery() { //不是构造方法  
    s = "constructor";  
    }  
      
    void go() {  
    System.out.println(s);  
    }  
      
    public static void main(String[] args) {  
    Mystery m = new Mystery();  
    m.go();  
    }  
}  
{% endhighlight %}

程序执行的结果为null，虽然说Mystery m = new Mystery();调用了Mystery 类的构造方法，但是public void Mystery()并不是构造方法，他只是一个普通的实例方法而已,那该类的构造方法哪去了呢？

二、说到这就得说一下java的默认构造方法
我们知道,java语言中规定每个类至少要有一个构造方法,为了保证这一点，当用户没有给java类定义明确的构造方法的时候,java为我们提供了一个默认的构造方法,这个构造方法没有参数,修饰符是public并且方法体为空。
其实默认的构造方法还分为两种,一种就是刚刚说过的隐藏的构造方法,另一种就是显示定义的默认构造方法.
如果一个类中定义了一个或者多个构造方法,并且每一个构造方法都是带有参数形式的,那么这个类就没有默认的构造方法,看下面的例子。

{% highlight java %}
public class Sample1{}  
  
public class Sample2{  
    public Sample2(int a){System.out.println("My Constructor");}  
}  
  
public class Sample3{  
    public Sample3(){System.out.println("My Default Constructor");}  
} 
{% endhighlight %}

上面的三个类中Sample1有一个隐式的默认构造方法,下列语句Sample1 s1=new Sample()合法;
Sample2没有默认的构造方法,下列语句Sample2 s2=new Sample2()不合法,执行会编译错误
Sample3有一个显示的默认构造方法,所以以下语句Sample3  s3=new Sample3();合法。

三、实例方法和构造方法中this、super的使用.
"this"的用法
实例方法中可以使用this关键字,它指向正在执行方法的类的实例对象,当然static方法中是不可以使用this对象的,因为静态方法不属于类的实例对象;而构造方法中同样可以使用this关键字,构造器中的this是指向同一个对象中不同参数的另一个构造器。让我们来看下面的一段代码：

{% highlight java %}
public class Platypus {  
    String name;  
  
    Platypus(String input) {  
        name = input;  
    }  
  
    Platypus() {  
        this("John/Mary Doe");  
    }  
  
    public static void main(String args[]) {  
        Platypus p1 = new Platypus("digger");  
        Platypus p2 = new Platypus();  
        System.out.println(p1.name + "----" + p2.name);  
    }  
}  
{% endhighlight %}

上面的代码中 类有两个构造器,第一个构造器给类的成员name赋值,第二个构造器调用第一个构造器给类的成员name一个初始值Jonn/Mary Doe
所以程序执行结果：digger----John/Mary Doe
需要注意的两个地方是：
1、构造方法中通过this关键字调用其他构造方法时,那么这句代码必须放在第一行,否则会编译错误。
2、构造方法中只能通过this调用一次其他的构造方法。

"super"的用法：
实例方法和构造方法中的super关键字都用于去指向父类,实例方法中的super关键字是去调用父类当中的某个方法,看下面的代码：

{% highlight java %}
class getBirthInfo {  
    void getBirthInfo() {  
        System.out.println("born alive.");  
    }  
}  
  
class Platypus1 extends getBirthInfo  
{  
    void getBirthInfo() {  
           System.out.println("hatch from eggs");  
           System.out.println("a mammal normally is ");  
           super.getBirthInfo();  
      }  
}  
  
public class test1 {  
    public static void main(String[] args) {  
        Platypus1 p1=new Platypus1();  
        p1.getBirthInfo();  
    }  
}  
{% endhighlight %}

上面的例子使用super.getBirthInfo();调用了它的父类的void getBirthInfo()方法。
构造器中使用super关键字调用父类中的构造器,看下面的代码：

{% highlight java %}
class getBirthInfo {  
    getBirthInfo(){  
        System.out.println("auto");  
    }  
    void aa() {  
        System.out.println("born alive.");  
    }  
}  
  
class Platypus1 extends getBirthInfo  
{  
      Platypus1() {  
        super();  
        System.out.println("hatch from eggs");  
        System.out.println("a mammal normally is ");  
      }  
}  
  
public class test1 {  
    public static void main(String[] args) {  
        Platypus1 p1=new Platypus1();  
    }  
}  
{% endhighlight %}

执行了代码我们就会看到构造器中的super调用了父类的构造方法。

类的继承机制使得子类可以调用父类的功能,下面介绍类在继承关系的初始化顺序问题

请看实例1:

{% highlight java %}
class SuperClass   
{   
    SuperClass()   
    {   
        System.out.println("SuperClass constructor");   
    }   
}   
public class SubClass extends SuperClass {  
    SubClass()   
    {   
        System.out.println("SubClass constructor");   
    }   
    public static void main(String[] args) {  
        SubClass sub = new SubClass();   
    }   
}   
{% endhighlight %}

执行结果：SuperClass constructor
     SubClass constructor
代码中我们只实例化子类一个对象,但从执行结果上看程序一开始并不是运行子类的构造方法,而是先执行父类的默认构造方法,然后再执行子类的构造方法.所以我们在实例化子类对象时,程序会先调用父类的默认构造方法,然后再执行子类的构造方法。

再看实例2：

{% highlight java %}
class SuperClass   
{   
    SuperClass(String str)   
    {   
    System.out.println("Super with a string.");   
    }   
}   
public class SubClass extends SuperClass   
{   
    SubClass(String str)   
    {   
    System.out.println("Sub with a string.");   
    }   
      
    public static void main(String[] args)   
    {   
    SubClass sub = new SubClass("sub");   
    }   
}   
{% endhighlight %}

此程序在JDK下不能编译成功,因为我们在实例化子类对象的时候会先调用其父类默认的构造方法,但是它的父类没有默认的构造方法,所以不能编译成功。
解决办法：
1、在父类中加一个显示的默认构造方法
2、在子类的构造方法中加一句super(str)并且必须在构造器的第一句。
两个办法都可以解决程序编译的问题,但是执行结果是不一样的.
第一种执行结果为:Sub with a string.
第二种执行结果为：Super with a string. 
                       Sub with a string.     第二种方法即使父类中有显示的默认构造方法也不会被调用。

再看实例三：

{% highlight java %}
class One   
{   
    One(String str)   
    {   
    System.out.println(str);   
    }   
}   
class Two   
{   
    One one_1 = new One("one-1");   
    One one_2 = new One("one-2");   
    One one_3 = new One("one-3");   
    Two(String str)   
    {   
    System.out.println(str);   
    }   
}   
public class Test   
{   
    public static void main(String[] args)   
    {   
    System.out.println("Test main() start");   
    Two two = new Two("two");   
    }   
}  
{% endhighlight %}

执行结果：
Test main() start
one-1
one-2
one-3
two
我们在main方法中实例了一个Two的对象,但是程序在实例Two对象时并没有先调用Two的构造方法,而是先初始化Two类的成员变量,Two类中有三个成员变量,他们都是One类的对象,所以要依次执行One类的构造方法,然后再初始化Two类的对象。
既在实例化类的对象时,类中的成员变量会首先进行初始化,如果其中的成员变量有对象，那么它们也会按照顺序执行初始化工作。在所有类成员初始化完成后，才调用对象所在类的构造方法创建对象。构造方法作用就是初始化。 

再看实例四：

{% highlight java %}
class One   
    {   
    One(String str)   
    {   
    System.out.println(str);   
    }   
}   
class Two   
{   
    One one_1 = new One("one-1");   
    One one_2 = new One("one-2");   
    static One one_3 = new One("one-3");   
    Two(String str)   
    {   
    System.out.println(str);   
    }   
}   
public class Test   
{   
    public static void main(String[] args)   
    {   
    System.out.println("Test main() start");   
    Two two_1 = new Two("two-1");   
    System.out.println("------------");   
    Two two_2 = new Two("two-2");   
    }   
}   

{% endhighlight %}
执行结果：
Test main() start
one-3
one-1
one-2
two-1
------------
one-1
one-2
two-2
结论：如果一个类中有静态对象,那么他会在非静态对象初始化前进行初始化,但只初始化一次。而非静态对象每次调用时都要初始化。

再看实例五：

{% highlight java %}
class One   
{   
    One(String str)   
    {   
    System.out.println(str);   
    }   
}   
class Two   
{   
    One one_1 = new One("one-1");   
    One one_2 = new One("one-2");   
    static One one_3 = new One("one-3");   
    Two(String str)   
    {   
    System.out.println(str);   
    }   
}   
public class Test   
{   
    static Two two_3 = new Two("two-3");   
    public static void main(String[] args)   
    {   
    System.out.println("Test main() start");   
    Two two_1 = new Two("two-1");   
    System.out.println("------------");   
    Two two_2 = new Two("two-2");   
    }   
}  

{% endhighlight %}
执行结果：
one-3
one-1
one-2
two-3
Test main() start
one-1
one-2
two-1
------------
one-1
one-2
two-2
结论：程序中主类的静态变量会在main()方法执行前初始化。结果中只输出了一次one-3，这也说明：如果一个类中有静态对象，那么它会在非静态对象前初始化，但只初始化一次。非静态对象每次调用时都要初始化。 

总结初始化顺序：
　　1．主类的静态成员首先初始化。 
　　2．主类的父类的构造方法被调用。 
　　3．主类的非静态对象（变量）初始化。 
　　4．调用主类的构造方法。 
