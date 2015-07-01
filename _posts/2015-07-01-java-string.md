---
layout: post
title: 与String相关的面试题汇总
category: JAVA
date: 2015-07-01
duoshuo: true
tags : [面试]
---

1、先来个最古老的，String是否是基本数据类型
答：不是，基础数据类型包括byte、char、short、int、long、float、double、boolean ，这八种类型都是Java中内置的类型，也叫原生类型，string不是，它是一个类。
2、String s1="abc";String s2="abc"; System.out.println(a==b);  输出结果是什么？
答：结果是true，由于String是final类，字符串是常量（内存中创建后不能被修改），而且字符串经常在程序中使用，所以Java为其提供了缓冲区，缓冲区内
的字符串会被共享，使用双引号形式定义一个字符串的时候就会被存储在缓冲区的，使用"abc"的时候会先在缓冲区中寻找是否存在此字符串，没有就创建一个，
有则直接使用，第一次使用“abc”的时候会在缓冲区中创建，第二次的时候就会直接引用。
所以，如果问题目中的代码创建了几个对象，那么也就是创建了一个。
比较一下：

{% highlight java %}
public class StringDemo1 {  
    public static void main(String[] args) {  
        test1();  
        test2();  
    }  
    private static void test1() {  
        String s1="abc";  
        String s2="abc";  
        System.out.println(s1==s2);//true  
    }  
  
    private static void test2() {  
        String s3=new String("bcd");  
        String s4="bcd";  
        System.out.println(s3==s4);//false    
    }  
}  

{% endhighlight %}
   

所以，再用构造函数创建一个字符串对象的时候，是每次都在缓冲区外创建一个新的对象，用"=="比较的时候永远不会相等。
3、String s1="abc";String s2="ab"+"c"; System.out.println(a==b);  输出结果是什么？
答：结果还是true；其实这里的s2并没有进行字符串相加，两个字符串常量相加在编译的时候会自动转换成一个字符串。
4、下面这条语句一共创建了多少个对象：String s="a"+"b"+"c"+"d";
答：由前面的两道题，其实我们已经知道这道题的答案了，明白了它的原理也就不那么难了，答案就是0个或者1个
因为如果对象"a"+"b"+"c"+"d"在编译的时候会自动将其编译成"abcd"，所以这道题就变成了String s="abcd";创建了几个对象？  
如果在执行该句代码之前缓冲区中存在"abcd"这个对象，那么就不会重新创建对象，那么就是0个，如果缓冲区中没有"abcd"这个对象，那么就是1个。
5、String s=new String("xyz");创建了几个对象？有什么不同？
答：创建了一个或者两个 ，首先new String("xyz")肯定会在内存中创建一个对象，它是参照常量"xyz"进行创建对象，所以首先会看缓冲区中是否存在常
量"xyz"，如果不存在，要先在缓冲区中创建一个常量"xyz"，否则不用创建，所以答案为1个或者2个。
6、==和equals的区别
（1）、基本数据类型，也称为原始数据类型（byte、char、short、int、long、float、double、boolean）它们之间的比较用双等号（==），比较的是它们的值。
（2）、对象的比较，当对象用==进行比较的时候比较的是它们的内存中的存放地址，所以，new出的两个相同对象用==比较是不相等的，因为它们在内存中存放的地址不同，所以对象的比较要用equals进行比较，值得注意的是，其实Object类中定义的equals方法也是基于==的,源代码如下：
{% highlight java %}
public boolean equals(Object obj) {  
    return (this == obj);  
}  
{% endhighlight %}

在Object类中equals比较的也是内存地址，但是在String、Integer、Date等类中覆写了Object类中的equals方法，我们拿String类中的equals方法看一下：

{% highlight java %}
public boolean equals(Object anObject) {  
    if (this == anObject) {  
        return true;  
    }  
    if (anObject instanceof String) {  
        String anotherString = (String) anObject;  
        int n = value.length;//本身字符串长度  
        if (n == anotherString.value.length) {//如果本身字符串长度与传递进来的字符串长度相等就继续向下比较  
            char v1[] = value;//取每一个位置的字符  
            char v2[] = anotherString.value;  
            int i = 0;  
            while (n-- != 0) {  
                if (v1[i] != v2[i])//对每一个位置的字符进行逐一比较  
                        return false;  
                i++;  
            }  
            return true;  
        }  
    }  
    return false;  
}  
{% endhighlight %}

看明白了吧，String中覆写的equals方法是用来比较两个字符串的内容是否相同的，而不是内存中的存放地址。
7、String s="abcdefg"; s.substring(0,3);System.out.println(s);
答：这题就比较简单了，答案就是abcdefg，因为String类是final类，值不可改变，final类不可被继承。
8、String类和StringBuffer类的区别
Java平台提供了两个类，String和StringBuffer类，String类提供了数值不可改变的字符串，StringBuffer类提供的字符串可以进行修改。
所以我们可以使用StringBuffer来构造动态数据。举个例子：
我们要把1到100的所有字符串拼起来，组成一个新的字符串，当我们用String类和StringBuffer类分别执行的时候：

{% highlight java %}
import java.util.Date;  
public class StringDemo {  
    public static void main(String[] args) {  
        StringTest();  
        StringBufferTest();  
    }  
  
    private static void StringTest() {  
        long d1=new Date().getTime();  
        String s = "";  
        for(int i=1;i<=10000;i++){  
            s=s+i;  
        }  
        long d2=new Date().getTime();  
        System.out.println("应用String类用时"+(d2-d1));//190  
    }  
    private static void StringBufferTest() {  
        long d3=new Date().getTime();  
        StringBuffer s1 =new StringBuffer();  
        for(int i=1;i<=10000;i++){  
            s1=s1.append(i);  
        }  
        long d4=new Date().getTime();  
        System.out.println("应用StringBuffer类用时"+(d4-d3));//1  
    }  
      
}  
{% endhighlight %}

通过打印看出StringBuffer类的效率要比String高的多。因为用String的时候要循环创建100个对象，而用StringBuffer的时候只是一个对象的值在不断改变。
另外，两者还有个重要的区别，就是String类覆写了Object类的equals方法，所以new String（“abc”）.equals(new String("abc"))的结果是true，而
StringBuffer类并没有覆写Object的equals方法。所以new StringBuffer("abc").equals(new StringBuffer("abc"))的结果为false。
注意：StringBuffer 对象用append()来修改其对象，而不是用“+”； StringBuffer对象不能用“=”赋值，只能用构造函数（new StringBuffer("")）来赋
值。
8、StringBuffer和StringBuilder的区别
区别就是StringBuffer是支持多线程操作，是线程安全的，而StringBuilder只能支持单线程操作，是线程不安全的。由于StringBuffer支持了多线程的操作导
致比StringBuilder性能慢。
      
