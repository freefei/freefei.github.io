---
layout: post
title: Java异常处理面试题归纳
category: JAVA
date: 2015-07-01
duoshuo: true
tags : [面试]
---

1、error和exception有什么区别

error表示系统级的错误，是java运行环境内部错误或者硬件问题，不能指望程序来处理这样的问题，除了退出运行外别无选择，它是Java虚拟机抛出的。
exception 表示程序需要捕捉、需要处理的异常，是由与程序设计的不完善而出现的问题，程序必须处理的问题

2、运行时异常和一般异常有何不同

Java提供了两类主要的异常：runtimeException和checkedException 
一般异常（checkedException）主要是指IO异常、SQL异常等。对于这种异常，JVM要求我们必须对其进行cathc处理，所以，面对这种异常，不管我们是否愿
意，都是要写一大堆的catch块去处理可能出现的异常。
运行时异常（runtimeException）我们一般不处理，当出现这类异常的时候程序会由虚拟机接管。比如，我们从来没有去处理过NullPointerException，而且
这个异常还是最常见的异常之一。
出现运行时异常的时候，程序会将异常一直向上抛，一直抛到遇到处理代码，如果没有catch块进行处理，到了最上层，如果是多线程就有Thread.run()抛出，如
果不是多线程那么就由main.run()抛出。抛出之后，如果是线程，那么该线程也就终止了，如果是主程序，那么该程序也就终止了。
其实运行时异常的也是继承自Exception，也可以用catch块对其处理，只是我们一般不处理罢了，也就是说，如果不对运行时异常进行catch处理，那么结果不
是线程退出就是主程序终止。
如果不想终止，那么我们就必须捕获所有可能出现的运行时异常。如果程序中出现了异常数据，但是它不影响下面的程序执行，那么我们就该在catch块里面将异
常数据舍弃，然后记录日志。如果，它影响到了下面的程序运行，那么还是程序退出比较好些。

<!-- more -->

3、Java中异常处理机制的原理

Java通过面向对象的方式对异常进行处理，Java把异常按照不同的类型进行分类，并提供了良好的接口。在Java中，每个异常都是一个对象，它都是Throwable
或其子类的实例。当一个方法出现异常后就会抛出一个异常对象，该对象中包含有异常信息，调用这个对象的方法可以捕获到这个异常并对异常进行处理。Java的
异常处理是通过5个关键词来实现的：try catch  throw throws finally。
一般情况下是用try来执行一段程序，如果出现异常，系统会抛出（throws），我们可以通过它的类型来捕捉它，或最后由缺省处理器来处理它（finally）。
try：用来指定一块预防所有异常的程序
catch：紧跟在try后面，用来捕获异常
throw：用来明确的抛出一个异常
throws：用来标明一个成员函数可能抛出的各种异常
finally：确保一段代码无论发生什么异常都会被执行的一段代码。

4、你平时在项目中是怎样对异常进行处理的。
（1）尽量避免出现runtimeException 。例如对于可能出现空指针的代码，带使用对象之前一定要判断一下该对象是否为空，必要的时候对runtimeException
也进行try catch处理。
（2）进行try catch处理的时候要在catch代码块中对异常信息进行记录，通过调用异常类的相关方法获取到异常的相关信息，返回到web端，不仅要给用户良好
的用户体验，也要能帮助程序员良好的定位异常出现的位置及原因。例如，以前做的一个项目，程序遇到异常页面会显示一个图片告诉用户哪些操作导致程序出现
了什么异常，同时图片上有一个按钮用来点击展示异常的详细信息给程序员看的。

5、final、finally、finalize的区别

（1）、final用于声明变量、方法和类的，分别表示变量值不可变，方法不可覆盖，类不可以继承
（2）、finally是异常处理中的一个关键字，表示finally{}里面的代码一定要执行
（3）、finalize是Object类的一个方法，在垃圾回收的时候会调用被回收对象的此方法。

6、try()里面有一个return语句，那么后面的finally{}里面的code会不会被执行，什么时候执行，是在return前还是return后？
自己写了个代码测试了一下：

{% highlight java %}
public class finallyDemo   
public static void main(String[] args) {  
    int i=getInt();  
    System.out.println(i);  
}  
  
private static int getInt() {  
    // TODO Auto-generated method stub  
    try {  
        return 0;  
    } catch (Exception e) {  
        // TODO Auto-generated catch block  
        e.printStackTrace();  
    }finally{  
        return 1;  
    }  
}  
      
{% endhighlight %}