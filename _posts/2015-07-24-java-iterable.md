---
layout: post
title: Java迭代器
category: JAVA
date: 2015-07-24
---

迭代器是一种模式，它可以使得对于序列类型的数据结构的遍历行为与被遍历的对象分离，即我们无需关心该序列的底层结构是什么样子的。只要拿到这个对象,使用迭代器就可以遍历这个对象的内部.

<!-- more -->

* 1.Iterator
Java提供一个专门的迭代器<<interface>>Iterator，我们可以对某个序列实现该interface，来提供标准的Java迭代器。Iterator接口实现后的功能是“使用”一个迭代器.
文档定义:
{% highlight java %}
Package  java.util; 

public interface Iterator<E> {

    boolean hasNext();//判断是否存在下一个对象元素

     E next();

    void remove();
}


{% endhighlight %}

* 2.Iterable
Java中还提供了一个Iterable接口，Iterable接口实现后的功能是“返回”一个迭代器,我们常用的实现了该接口的子接口有: Collection<E>, Deque<E>, List<E>, Queue<E>, Set<E> 等.该接口的iterator()方法返回一个标准的Iterator实现。实现这个接口允许对象成为 Foreach 语句的目标。就可以通过Foreach语法遍历你的底层序列。
 Iterable接口包含一个能够产生Iterator的iterator()方法，并且Iterable接口被foreach用来在序列中移动。因此如果创建了任何实现Iterable接口的类，都可以将它用于foreach语句中。

 {% highlight java %}
 文档定义:

Package  java.lang; 

import  java.util.Iterator; 
public interface Iterable<T> { 
     Iterator<T> iterator(); 
}

使用Iterator的简单例子

import java.util.*;

public class TestIterator {


   public static void main(String[] args) {

        

      List list=new ArrayList();

      Map map=new HashMap();

      for(int i=0;i<10;i++){

          list.add(new String("list"+i) );

          map.put(i, new String("map"+i));

      }

      Iterator iterList= list.iterator();//List接口实现了Iterable接口

        while(iterList.hasNext()){

          String strList=(String)iterList.next();

          System.out.println(strList.toString());

      }

      Iterator iterMap=map.entrySet().iterator();

      while(iterMap.hasNext()){

          Map.Entry  strMap=(Map.Entry)iterMap.next();

          System.out.println(strMap.getValue());

 

      }

   }

}

 <span style="font-size:18px;color:#000099;">&nbsp; </span><span style="font-size:18px;color:#000099;"></span>&nbsp;
 {% endhighlight%}


接口Iterator在不同的子接口中会根据情况进行功能的扩展,例如针对List的迭代器ListIterator,该迭代器只能用于各种List类的访问。ListIterator可以双向移动。添加了previous()等方法.
 
* 3   Iterator与泛型搭配
Iterator对集合类中的任何一个实现类，都可以返回这样一个Iterator对象。可以适用于任何一个类。
因为集合类(List和Set等)可以装入的对象的类型是不确定的,从集合中取出时都是Object类型,用时都需要进行强制转化,这样会很麻烦,用上泛型,就是提前告诉集合确定要装入集合的类型,这样就可以直接使用而不用显示类型转换.非常方便.
 
* 4.foreach和Iterator的关系
        for each是jdk5.0新增加的一个循环结构，可以用来处理集合中的每个元素而不用考虑集合定下标。

        格式如下 

        for(variable:collection){ statement; }

定义一个变量用于暂存集合中的每一个元素，并执行相应的语句(块)。collection必须是一个数组或者是一个实现了lterable接口的类对象。 
 
 {% highlight java %}
上面的例子使用泛型和forEach的写法:

import java.util.*;
public class TestIterator {



   public static void main(String[] args) {

        

      List<String>  list=new ArrayList<String> ();

      for(int i=0;i<10;i++){

          list.add(new String("list"+i) );

      }

      for(String str:list){

        System.out.println(str);

      }  

}


 {% endhighlight %}

可以看出,使用for each循环语句的优势在于更加简洁，更不容易出错，不必关心下标的起始值和终止值。
forEach不是关键字,关键字还是for,语句是由iterator实现的，他们最大的不同之处就在于remove()方法上。

 一般调用删除和添加方法都是具体集合的方法，例如：
 List list = new ArrayList(); list.add(...); list.remove(...);
 但是，如果在循环的过程中调用集合的remove()方法，就会导致循环出错，因为循环过程中list.size()的大小变化了，就导致了错误。 所以，如果想在循环语句中删除集合中的某个元素，就要用迭代器iterator的remove()方法，因为它的remove()方法不仅会删除元素，还会维护一个标志，用来记录目前是不是可删除状态，例如，你不能连续两次调用它的remove()方法，调用之前至少有一次next()方法的调用。  
 forEach就是为了让用iterator循环访问的形式简单，写起来更方便。当然功能不太全,所以但如有删除操作，还是要用它原来的形式。
 
 * 5   使用for循环与使用迭代器iterator的对比
 
效率上的各有有事
采用ArrayList对随机访问比较快，而for循环中的get()方法，采用的即是随机访问的方法，因此在ArrayList里，for循环较快
采用LinkedList则是顺序访问比较快，iterator中的next()方法，采用的即是顺序访问的方法，因此在LinkedList里，使用iterator较快
从数据结构角度分析,for循环适合访问顺序结构,可以根据下标快速获取指定元素.而Iterator 适合访问链式结构,因为迭代器是通过next()和Pre()来定位的.可以访问没有顺序的集合. 
而使用 Iterator 的好处在于可以使用相同方式去遍历集合中元素，而不用考虑集合类的内部实现（只要它实现了 java.lang.Iterable 接口），如果使用 Iterator 来遍历集合中元素，一旦不再使用 List 转而使用 Set 来组织数据，那遍历元素的代码不用做任何修改，如果使用 for 来遍历，那所有遍历此集合的算法都得做相应调整,因为List有序,Set无序,结构不同,他们的访问算法也不一样.


转自 http://blog.csdn.net/wanghuan203/article/details/7279742

