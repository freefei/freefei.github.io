---
layout: post
title: Java中的equals和==
category: JAVA
date: 2015-07-02
duoshuo: true
tags : [面试]
---

在初学Java时，可能会经常碰到下面的代码：

> String str1 = new String("hello");
> String str2 = new String("hello");
>         
> System.out.println(str1==str2);
> System.out.println(str1.equals(str2));

为什么第4行和第5行的输出结果不一样？==和equals方法之间的区别是什么？如果在初学Java的时候这个问题不弄清楚，就会导致自己在以后编写代码时出现一些低级的错误。今天就来一起了解一下==和equals方法的区别之处。


<!-- more -->


一.关系操作符“==”到底比较的是什么？

　　下面这个句话是摘自《Java编程思想》一书中的原话：

　　“关系操作符生成的是一个boolean结果，它们计算的是操作数的值之间的关系”。

　　这句话看似简单，理解起来还是需要细细体会的。说的简单点，==就是用来比较值是否相等。下面先看几个例子：

{% highlight java%}

public class Main {

    /**
     * @param args
     */
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        
        int n=3;
        int m=3;
        
        System.out.println(n==m);
        
        String str = new String("hello");
        String str1 = new String("hello");
        String str2 = new String("hello");
        
        System.out.println(str1==str2);
        
        str1 = str;
        str2 = str;
        System.out.println(str1==str2);
    }

}

{% endhighlight%}

输出结果为 true false true

　　n==m结果为true，这个很容易理解，变量n和变量m存储的值都为3，肯定是相等的。而为什么str1和str2两次比较的结果不同？要理解这个其实只需要理解基本数据类型变量和非基本数据类型变量的区别。

　　在Java中游8种基本数据类型：

　　浮点型：float(4 byte), double(8 byte)

　　整型：byte(1 byte), short(2 byte), int(4 byte) , long(8 byte)

　　字符型: char(2 byte)

　　布尔型: boolean(JVM规范没有明确规定其所占的空间大小，仅规定其只能够取字面值"true"和"false")

　　对于这8种基本数据类型的变量，变量直接存储的是“值”，因此在用关系操作符==来进行比较时，比较的就是 “值” 本身。要注意浮点型和整型都是有符号类型的，而char是无符号类型的（char类型取值范围为0~2^16-1).

　　也就是说比如：

　　int n=3;

　　int m=3;　

　　变量n和变量m都是直接存储的"3"这个数值，所以用==比较的时候结果是true。

　　而对于非基本数据类型的变量，在一些书籍中称作为 引用类型的变量。比如上面的str1就是引用类型的变量，引用类型的变量存储的并不是 “值”本身，而是于其关联的对象在内存中的地址。比如下面这行代码：

　　String str1;

　　这句话声明了一个引用类型的变量，此时它并没有和任何对象关联。

　　而 通过new String("hello")来产生一个对象（也称作为类String的一个实例），并将这个对象和str1进行绑定：

　　str1= new String("hello");

　　那么str1指向了一个对象（很多地方也把str1称作为对象的引用），此时变量str1中存储的是它指向的对象在内存中的存储地址，并不是“值”本身，也就是说并不是直接存储的字符串"hello"。这里面的引用和C/C++中的指针很类似。

　　因此在用==对str1和str2进行第一次比较时，得到的结果是false。因此它们分别指向的是不同的对象，也就是说它们实际存储的内存地址不同。

　　而在第二次比较时，都让str1和str2指向了str指向的对象，那么得到的结果毫无疑问是true。

二.equals比较的又是什么？

　　equals方法是基类Object中的方法，因此对于所有的继承于Object的类都会有该方法。为了更直观地理解equals方法的作用，直接看Object类中equals方法的实现。

　　该类的源码路径为：C:\Program Files\Java\jdk1.6.0_14的src.zip 的java.lang路径下的Object.java（视个人jdk安装路径而定）。

　　下面是Object类中equals方法的实现：

![class](http://7u2myi.com1.z0.glb.clouddn.com/object.jpg)

很显然，在Object类中，equals方法是用来比较两个对象的引用是否相等，即是否指向同一个对象。

　　但是有些朋友又会有疑问了，为什么下面一段代码的输出结果是true？

{% highlight java%}

public class Main {

    /**
     * @param args
     */
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        
        String str1 = new String("hello");
        String str2 = new String("hello");
        
        System.out.println(str1.equals(str2));
    }
}

{% endhighlight%}

要知道究竟，可以看一下String类的equals方法的具体实现，同样在该路径下，String.java为String类的实现。

　　下面是String类中equals方法的具体实现：

![string](http://7u2myi.com1.z0.glb.clouddn.com/string.jpg)
可以看出，String类对equals方法进行了重写，用来比较指向的字符串对象所存储的字符串是否相等。

　　其他的一些类诸如Double，Date，Integer等，都对equals方法进行了重写用来比较指向的对象所存储的内容是否相等。

　　总结来说：

　　1）对于==，如果作用于基本数据类型的变量，则直接比较其存储的 “值”是否相等；

　　　　如果作用于引用类型的变量，则比较的是所指向的对象的地址

　　2）对于equals方法，注意：equals方法不能作用于基本数据类型的变量

　　　　如果没有对equals方法进行重写，则比较的是引用类型的变量所指向的对象的地址；

　　　　诸如String、Date等类对equals方法进行了重写的话，比较的是所指向的对象的内容。




java中的数据类型，可分为两类：

1.基本数据类型，也称原始数据类型。byte,short,char,int,long,float,double,boolean 
  他们之间的比较，应用双等号（==）,比较的是他们的值。 
2.复合数据类型(类) 
  当他们用（==）进行比较的时候，比较的是他们在内存中的存放地址，所以，除非是同一个new出来的对象，他们的比较后的结果为true，否则比较后结果为false。 JAVA当中所有的类都是继承于Object这个基类的，在Object中的基类中定义了一个equals的方法，这个方法的初始行为是比较对象的内存地 址，但在一些类库当中这个方法被覆盖掉了，如String,Integer,Date在这些类当中equals有其自身的实现，而不再是比较类在堆内存中的存放地址了。
  对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是基于他们在内存中的存放位置的地址值的，因为Object的equals方法也是用双等号（==）进行比较的，所以比较后的结果跟双等号（==）的结果相同。

  {% highlight java%}
public class TestString {
 public static void main(String[] args) {
String s1 = "Monday";
String s2 = "Monday";
if (s1 == s2)
{
System.out.println("s1 == s2");}
else{
System.out.println("s1 != s2");}
}
}

  {% endhighlight%}

  编译并运行程序，输出：s1 == s2说明：s1 与 s2 引用同一个 String 对象 -- "Monday"!
2.再稍微改动一下程序，会有更奇怪的发现：

{% highlight java%}
public class TestString {
public static void main(String[] args) {
String s1 = "Monday";
String s2 = new String("Monday");
if (s1 == s2)
{System.out.println("s1 == s2");}
else
{System.out.println("s1 != s2");}
if (s1.equals(s2)) {System.out.println("s1 equals s2");}
else{
System.out.println("s1 not equals s2");}
}
}
  {% endhighlight%}

我们将s2用new操作符创建
程序输出：
s1 != s2
s1 equals s2
说明：s1 s2分别引用了两个"Monday"String对象

3. 字符串缓冲池
原来，程序在运行的时候会创建一个字符串缓冲池当使用 s2 = "Monday" 这样的表达是创建字符串的时候，程序首先会在这个String缓冲池中寻找相同值的对象，在第一个程序中，s1先被放到了池中，所以在s2被创建的时候，程序找到了具有相同值的 s1
将s2引用s1所引用的对象"Monday"
第二段程序中，使用了 new 操作符，他明白的告诉程序："我要一个新的！不要旧的！"于是一个新的"Monday"Sting对象被创建在内存中。他们的值相同，但是位置不同，一个在池中游泳一个在岸边休息。哎呀，真是资源浪费，明明是一样的非要分开做什么呢？

4.再次更改程序：

{% highlight java%}
public class TestString {
public static void main(String[] args) {
String s1 = "Monday";
String s2 = new String("Monday");
s2 = s2.intern();
if (s1 == s2)
{System.out.println("s1 == s2");}
else
{System.out.println("s1 != s2");}
if (s1.equals(s2)) {System.out.println("s1 equals s2");}
else{
System.out.println("s1 not equals s2");}
}
}
  {% endhighlight%}

  这次加入：s2 = s2.intern();
程序输出：
s1 == s2
s1 equals s2
原 来，（java.lang.String的intern()方法"abc".intern()方法的返回值还是字符串"abc"，表面上看起来好像这个方 法没什么用处。但实际上，它做了个小动作：检查字符串池里是否存在"abc"这么一个字符串，如果存在，就返回池里的字符串；如果不存在，该方法会 把"abc"添加到字符串池中，然后再返回它的引用。
）