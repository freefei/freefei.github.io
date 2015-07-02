---
layout: post
title: Java多线程面试题归纳
category: JAVA
date: 2015-07-01
duoshuo: true
tags : [面试]
---

1、多线程有哪几种实现方法？举个例子说明下线程的同步。

（1）Java多线程有两种实现方式：继承Thread类和实现Runnable接口，Thread就是实现了Runnable接口。
两个最简单的线程例子：

{% highlight java %}
package chc.runnable;  
  
public class ThreadTest2 {  
    public static void main(String[] args) throws InterruptedException {  
        Thread1 t = new Thread1();  
        //t.run(); //这里也不能直接调用方法  
        t.start();  
        for (int i = 0; i < 100; i++) {  
            System.out.println("main:"+i);  
        }  
    }  
}  
  
class Thread1 extends Thread{  
    public void run() {  
        for (int i = 0; i < 100; i++) {  
            System.out.println("Thread-----:"+i);  
        }  
    }  
}  

package chc.runnable;  
  
public class ThreadTest1 {  
    public static void main(String[] args) {  
        Runnable1 r =new Runnable1();  
        Thread t1=new Thread(r);  
        t1.start();  
    }  
}  
  
class Runnable1  implements Runnable {  
  
    public void run() {  
        // TODO Auto-generated method stub  
        for (int i = 1; i <= 5; i++) {  
            System.out.println("实现Runnable接口的线程----->"+i);  
        }  
    }  
  
}  

{% endhighlight %}

通过上两个例子发现，当启动线程的时候并不影响主程序的继续执行。

<!-- more -->

（2）线程同步问题

使用synchronnized关键字
银行账户存取钱的问题：

{% highlight java %}
public class ThreadTest {  
    public static void main(String[] args){  
        ThreadA  t1=new ThreadA();  
        t1.start();  
        ThreadB  t2=new ThreadB();  
        t2.start();  
    }  
}  
  
class ThreadA extends Thread{  
    @Override  
    public void run(){  
        for(int i=0;i<100;i++){  
            account.add();  
            try {  
                sleep(10);//模拟银行系统处理时间  
            } catch (InterruptedException e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
        }  
    }  
}  
  
class ThreadB extends Thread{  
    @Override  
    public void run() {  
        for(int i=0;i<100;i++){  
            account.remove();  
            try {  
                sleep(10);<span style="font-family: SimSun;">//模拟银行系统处理时间</span>  
            } catch (InterruptedException e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
        }  
    }  
}  
  
class account {  
    public static   int count=1000;  
      
    //减去100元  
    public static synchronized void remove(){  
        count=count-100;  
        System.out.println("减去100元,卡内余额"+count);  
    }  
      
    //增加100元  
    public static synchronized void add(){  
        count=count+100;  
        System.out.println("加上100元,卡内余额"+count);  
    }  
}  

{% endhighlight %}

注意：上例中将account类的add()和remove()方法都加上了static关键字，因为这样在线程中可以直接通过类名调用到这两个方法，而不需要实例化对象。
因为synchronized同步只对同一个对象中的方法有效，也就是说一个线程正在执行account的add()方法，另一个线程是可以调用到另一个对象的add方法的。
2、启动一个线程是用run()还是start()，调用的时候有什么区别？
当然是start()了，当调用线程的start()方法的时候，线程就会进入到就绪状态
run()方法是线程的执行入口，当线程从就绪状态进入到执行状态时首先要从run()方法开始执行。
当然，我们也是可以直接通过线程对象调用该对象的run()方法的，只是这只是一次普通的调用，并没有启动任何一个线程。
当我们调用start()方法时，是另外启动了一个线程去执行线程类的代码，并不影响主程序的执行，但是调用run()方法的时候要等待run()方法内的代码执
行完主程序才可以向下执行，举个例子:

{% highlight java %}
public class ThreadDemo2 {  
    public static void main(String[] args) {  
        Thread5 t1=new Thread5();  
        Thread6 t2=new Thread6();  
        t1.run();  
        t2.start();  
        for(int i=0;i<100;i++){  
            System.out.println("主进程执行："+i);  
        }  
    }  
}  
  
class Thread5 extends Thread{  
    @Override  
    public void run() {  
        for(int i=0;i<100;i++){  
            System.out.println("Thread5执行："+i);  
        }  
    }  
}  
  
class Thread6 extends Thread{  
    @Override  
    public void run() {  
        for(int i=0;i<100;i++){  
            System.out.println("Thread6执行："+i);  
        }  
    }  
}  
{% endhighlight %}

输出结果的顺序是：Thread5全部打印完后 Thread6和主程序交替打印。验证了上面的说法

3、当一个线程进入到一个对象的synchronized方法，那么其他线程是否可以进入该对象的其它方法？

不一定，看情况
如果其它方法加了static关键字，那么该方法属于类，不属于对象，不能与对象的方法保持同步（即使有synchronized关键字），是能进入的。
如果其它方法不带有static关键字且带有synchronized关键字，那么不能进入，如果不带，则能。
再其次就看方法内部有没有wait（）方法释放锁了

4、子线程循环2次，接着主线程循环3次，接着子线程循环3次，接着主线程循环3次，如此循环5次，请写出程序代码。

{% highlight java %}
public class ThreadDemo5 {  
    static boolean thread_flag=false;//指示子线程循环是否结束  
    static boolean main_flag=true;   //调用子线程的start方法后变为true,循环等待thread_flag为true（也就是子线程循环完）的时刻，主线程循环完又变为false;  
    public static void main(String[] args) {  
        for(int k=0;k<5;k++){  
            Thread7 t=new Thread7();  
            t.start();  
            main_flag=true;  
            while (main_flag) {//循环等待thread_f  
                if(thread_flag){  
                    for(int i=0;i<3;i++){  
                        System.out.println("主线程第一次循环"+(i+1)+"次");  
                    }  
                    thread_flag=false;  
                    main_flag=false;  
                }     
            }  
        }  
    }  
}  
  
class Thread7 extends Thread {  
    static boolean flag=true;//标志子线程第几次循环，当值为true的时候子线程循环2次，否则循环3次  
    public void run() {  
        if(flag){  
            for(int i=0;i<2;i++){  
                System.out.println("子线程第一次循环"+(i+1)+"次");  
            }  
            flag=false;  
        }else{  
            for(int i=0;i<3;i++){  
                System.out.println("子线程第二次循环"+(i+1)+"次");  
            }  
            flag=true;  
        }  
        ThreadDemo5.thread_flag=true;  
          
          
    }  
}  
{% endhighlight %}

这个题就是要注意调用子线程的start方法的时候并不能阻止主程序继续向下执行，所以我们要用变量来标记。

5、sleep()和wait()有何异同？

（1）首先一个最明显的区别是  wait是Object类的方法,而sleep()是Thread类的静态方法,谁调用了该方法谁去休眠,即使在a线程里调用了b线程的sleep方法,实际上还是a线程去休眠.
（2）比较重要的一点是sleep没有释放出锁，而wait释放了锁，是其他线程可以使用同步块资源。
     sleep不出让系统资源；wait是进入线程等待池等待，出让系统资源，其他线程可以占用CPU。一般wait不会加时间限制，因为如果wait线程的运行资源不够，再出来也没用，要
     等待其他线程调用notify/notifyAll唤醒等待池中的所有线程，才会进入就绪队列等待OS分配系统资源。sleep(milliseconds)可以用时间指定使它自动唤醒过来，如果时间不到
     只能调用interrupt()强行打断。
（3）使用范围：
     wait，notify和notifyAll只能在同步控制方法或者同步控制块里面使用，而sleep可以在任何地方使用 
     synchronized(x){ 
       x.notify() 
       //或者wait() 
      }
（4）sleep需要捕获异常,而wait不需要。
6、现在有T1 T2 T3三个线程，怎样保证T2在T1执行完之后执行 T3在T2执行完之后执行
这题主要是考察对join()方法的使用。
当线程A当中执行了线程B.join()，那么A线程要等待B线程执行完才可以执行。

{% highlight java %}
public class JoinDemo {  
    public static void main(String[] args) {  
        T1 t1=new T1("T1");  
        T2 t2=new T2("T2");  
        T3 t3=new T3("T3");  
        t1.start();  
        try {  
            t1.join();  
        } catch (InterruptedException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
        t2.start();  
        try {  
            t2.join();  
        } catch (InterruptedException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
        t3.start();  
    }  
}  
  
class T1 extends  Thread{  
    private String name;  
    public T1(String name) {  
        this.name=name;  
    }  
  
    @Override  
    public void run() {  
        for(int i=0;i<5;i++){  
            try {  
                sleep(5);  
            } catch (InterruptedException e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
            System.out.println(this.name+"循环"+i);  
        }  
    }  
}  
class T2 extends  Thread{  
    private String name;  
    public T2(String name) {  
        this.name=name;  
    }  
    @Override  
    public void run() {  
        for(int i=0;i<5;i++){  
            try {  
                sleep(5);  
            } catch (InterruptedException e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
            System.out.println(this.name+"循环"+i);  
        }  
    }  
}  
class T3 extends  Thread{  
    private String name;  
    public T3(String name) {  
        this.name=name;  
    }  
    @Override  
    public void run() {  
        for(int i=0;i<5;i++){  
            System.out.println(this.name+"循环"+i);  
        }  
    }  
}  
{% endhighlight %}