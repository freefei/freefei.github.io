---
layout: post
title: java collection 之queue
category: JAVA
date: 2015-07-23
---

Queue是一种常见的数据结构，其主要特征在于FIFO（先进先出），Java中的Queue是这样定义的：

{% highlight java %}

public interface Queue<E> extends Collection<E> {
E element();
boolean offer(E o);
E peek();
E poll();
E remove();
}
{% endhighlight %}


虽然Queue都具有FIFO的特点，但具体输出哪一个元素，Queue的各种实现是不同的，尤其是在排序的情况下，新输入的元素并非放入队列尾部，而是放在适当的位置。Queue的每一种实现都必须指定排序属性（ordering properties）。

Queue可能对存放的元素数目有所限制。这样的Queue成为“有界的”（bounded），在Java.util.concurrent中的某些Queue实现是有界的，而java.util中的Queue实现不是有界的。

Queue的每个操作都有两种方法：

Queue Interface Structure	
	
|   |  Throws exception | Returns special value | 
|---------| ------| -------| 
|Insert | add(e)| offer(e) | 
|Remove | remove()| poll() |
|Examine | element()| peek() | 



add方法继承自Collection，当Queue的元素已经到达限制数目时，add会抛出一个IllegalStateException异常；offer方法仅仅定位于应用在有界Queue的情况下，当Queue已经装满时，offer会返回false。

remove和poll方法都删除并返回Queue中的头元素（注意，并不是插入的第一个元素，因为有的Queue实现是排序的）。当Queue为空时，remove抛出NoSuchElementException异常，而poll返回null。

element和peek返回但不删除Queue中的头元素，它们的区别类似remove与poll。

Queue的实现一般并不容许插入null，只有LinkedList是一个意外，它容许插入null，但使用者必须注意，不要与poll和peek方法返回的null值混淆。

Queue的实现一般并不定义基于元素的equals和hashCode方法，而是调用继承自Object的对应方法。

Queue接口并没有定义并行程序中常用的阻塞方法，也就是说，元素进入Queue之前不必检查Queue中是否有足够的空间，不过作为Queue扩展的java.util.concurrent.BlockingQueue接口定义了这些方法。

普通的LinkedList实现并没有定义特殊的排序算法，所以输出元素时会按照插入的顺序：

{% highlight java %}
import java.util.*;
public class Countdown {
public static void main(String[] args)
throws InterruptedException {
int time = 10;
Queue<Integer> queue = new LinkedList<Integer>();
for (int i = time; i >= 0; i--)
queue.add(i);
while(!queue.isEmpty()) {
System.out.println(queue.remove());
Thread.sleep(1000);
}
}
}
{% endhighlight %}


程序的输出结果为：
10
9
8
…..

如果作一点小更改，采用PriorityQueue

{% highlight java %}

import java.util.*;
public class Countdown {
public static void main(String[] args)
throws InterruptedException {
int time = 10;
Queue<Integer> queue = new LinkedList<Integer>();
Queue<Integer> pQueue = new PriorityQueue<Integer>();
for (int i = time; i >= 0; i--)
queue.add(i);
while(!queue.isEmpty()) {
pQueue.add(queue.remove()); 
}
while(!pQueue.isEmpty()) {
System.out.println(pQueue.remove()); 
}
}
}

{% endhighlight %}

则输出结果为：
0
1
2
……

查阅文档可知，PriorityQueue的内部是一个min heap，实际上，观察PriorityQueue的输出也可以发现这一点：

{% highlight java %}
int time = 10;
Queue<Integer> queue = new LinkedList<Integer>();
Queue<Integer> pQueue = new PriorityQueue<Integer>();
for (int i = time; i >= 0; i--)
queue.add(i);
while(!queue.isEmpty()) {
pQueue.add(queue.remove()); 
}

System.out.println(pQueue);
pQueue.remove();
System.out.println(pQueue);

{% endhighlight %}

输出结果为
[0,1,5,4,2,9,6,10,7,8,3]
[1,2,5,4,3,9,6,10,7,8]

实际上就是两个min-heap按照从上至下，从左至右的输出。