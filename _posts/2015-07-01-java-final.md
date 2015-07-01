---
layout: post
title: java中static关键字的理解
category: JAVA
date: 2015-07-01
duoshuo: true
tags : [面试]
---

一、静态方法
当在一个类中定义一个static方法 的时候，那就是说，无需创建该类对象即可调用该方法。声明为static的方法有以下几条限制：
1、static方法内只能调用其他static方法
2、static只能访问static数据
3、static方法内不能以任何方式使用this   super关键字

{% highlight java %}
class Simple { 
static void go(){ 
System.out.println("Welcome"); 
} 
} 
 
public class Cal{ 
public static void main(String[] args){ 
Simple.go(); 
} 
}

{% endhighlight %}

二、静态变量
我们常用的成员变量和方法真正并不属于类，而是属于实例化的某个类对象，
而静态变量 静态方法并不属于特定的实例化对象，他是在类被加载的时候就存在了，跟实例化没有任何关系，
类被装载到内存中的时候 静态变量 被放置在一块专门存在静态变量 的内存区域，可以叫做静态域。
程序运行期间，静态域内的变量随时等候调用。
 
 {% highlight java %}
class Value { 
static int c = 0; 
 
static void inc(){ 
c++; 
} 
} 
 
public class Count2{ 
public static void prt(String s){ 
System.out.print(s); 
} 
 
public static void main(String[] args){ 
Value v1, v2; 
v1 = new Value(); 
v2 = new Value(); 
prt("v1.c=" + v1.c + " v2.c=" + v2.c); 
v1.inc(); 
prt(" v1.c=" + v1.c + " v2.c=" + v2.c); 
} 
} 
{% endhighlight %}

结果为：v1.c=0 v2.c=0 v1.c=1 v2.c=1 
由此可以证明它们共享一块存储区。static变量有点类似于C中的全局变量的概念。
三、静态类
通常一个普通类是不允许声明成静态的，只有一个内部类才可以。这时这个声明为静态的内部类可以直接作为一个普通类使用，而不需实例一个外部类。
 
 {% highlight java %}
public class StaticCls { 
public static void main(String[] args) ...{ 
OuterCls.InnerCls oi = new OuterCls.InnerCls(); 
} 
} 
 
class OuterCls{ 
public static class InnerCls{ 
InnerCls(){ 
System.out.println("InnerCls"); 
} 
} 
} 

{% endhighlight %}
结果为：InnerCls
 
四、static和final一起用表示什么？
static 和 final一起修饰成员变量和成员方法可以简单理解为“全局常量”
对于变量表示一旦给值就不可修改，并且可以通过类名直接访问
对于方法，表示不可覆盖，并且可以通过类名直接访问
 
五、补充
static表示‘全局’或者‘静态’的意思，但是java语言中没有全局变量的概念
被static修饰的成员变量和成员方法独立于该类的任何对象，也就是说，他不依赖类的特定实例，被类的所有实例共享。只要这个类被加载，Java虚拟机就能根据类名在运行时找到他们，因此，static对象可以在他的任何对象创建之前访问，无需引用任何对象。
static前面可以有private修饰符修饰，表示这个变量可以在类的静态代码块中，或者类的静态方法中，当然也可以在非静态方法中使用。。需要注意的是他不可以在其他类中通过类名直接调用，这一点很重要，实际上我们要搞明白，private是访问权限限定，static表示不要实例化就可以直接使用，这样就容易理解的多了，static前面也可以加其他权限修饰符限定