---
layout: post
title: Java集合类相关面试题
category: JAVA
date: 2015-07-01
duoshuo: true
tags : [面试]
---

1、Collection和Collections的区别
java.util.Collection 是一个集合接口，Collection接口在Java类库中有很多具体的实现，例如List、Set
java.util.Collections 是针对集合类的一个帮助类，它提供了一系列的静态方法实现对各种集合的搜索、排序、线程安全化等操作。
2、ArrayList与Vector的区别
这两个类都实现了List接口（List接口继承自Collection接口）。它们都是有序集合，它们内部的元素都是可以重复的，都可以根据序号取出其中的某一元素。

<!-- more -->

它们两个的区别在于：
（1）、线程安全的问题：Vector是早期Java就有的，是允许多线程操作的，是线程安全的；而ArrayList是在Java2中才出现，它是线程不安全的，只能使用单线程
操作。 由于Vector支持多线程操作，所以在性能上就比不上ArrayList了。同样的HashTable相比于HashMap也是支持多线程的操作而导致性能不如HashMap。
（2）、数据增长的问题
ArrayList和Vector都有一个初始的容量大小，当存储进去它们里面的元素个数超出容量的时候，就需要增加ArrayList和Vector的存储空间，每次增加存储空间
的时候不是只增加一个存储单元，是增加多个存储单元。Vector默认增加原来的一倍，ArrayList默认增加原来的0.5倍。
Vector可以由我们自己来设置增长的大小，ArrayList没有提供相关的方法。
3、LinkedList与ArrayList有什么区别
两者都实现的是List接口，不同之处在于：
（1）、ArrayList是基于动态数组实现的，LinkedList是基于链表的数据结构。
（2）、get访问List内部任意元素时，ArrayList的性能要比LinkedList性能好。LinkedList中的get方法是要按照顺序从列表的一端开始检查，直到另一端
（3）、对于新增和删除操作LinkedList要强于ArrayList，因为ArrayList要移动数据
4、去掉Vector中的一个重复元素

{% highlight java %}
import java.util.HashSet;  
import java.util.Iterator;  
import java.util.Vector;  
public class VectorDemo {  
    public static void main(String[] args) {  
        Vector veList=new Vector();  
        veList.add("aa");  
        veList.add("bb");  
        veList.add("aa");  
        veList.add("bb");  
        veList.add("cc");  
          
        //去掉Vector中的重复元素方法一:  
        veList=getNewVector(veList);  
        //迭代结果  
        System.out.println("*************************第一种方式************************");  
        for(int i=0;i<veList.size();i++){  
            System.out.println(veList.get(i));  
        }  
          
        //去掉Vector中的重复元素方法二：  
        Vector veList1=getNewVector1(veList);  
        System.out.println("*************************第二种方式************************");  
        for(int i=0;i<veList1.size();i++){  
            System.out.println(veList1.get(i));  
        }         
    }  
  
    private static Vector getNewVector(Vector veList) {  
        Vector newVector=new Vector();  
        for(int i=0;i<veList.size();i++){  
            String str=(String) veList.get(i);  
            if(!newVector.contains(str)){  
                newVector.add(str);  
            }  
        }  
        return newVector;  
    }  
      
    private static Vector getNewVector1(Vector veList) {  
        Vector newVector=new Vector();  
        HashSet set=new HashSet(veList);  
        Iterator it =set.iterator();  
        while (it.hasNext()) {  
            String str=(String) it.next();  
            newVector.add(str);  
        }  
        return newVector;  
    }  
}  
{% endhighlight %}

5、HashMap与HashTable的区别
两者都实现了Map接口，主要区别在于：
（1）、HashTable是早期Java就有的，支持多线程操作，是线程安全的。HashMap是Java2才出现的，是HashTable的轻量级实现，仅支持单线程操作，线程不安
全的。
（2）、HashMap允许空的key和value  HashTable不允许
6、List与Map的区别
List是存储单列数据的集合，Map是存储key和value这样双列数据的集合，List中存储的数据是有顺序的，并且允许重复。Map当中存储的数据是没有顺序的，它
存储的key是不能重复的，value是可以重复的。
List继承Collection接口，Map不是，Map没有父类
7、List、Map、Set三个接口，存取元素时各有什么特点
首先List和Set都是单列元素的集合，它们有一个共同的父接口Collection。
List内的元素讲究有序性，内部元素可重复；但是Set恰恰相反，它讲究的是无序性，元素不可重复；Set的add方法有一个boolean的返回值，每当add一个新元
素的时候都会调用equals方法进行逐一比较，当新元素与所有的已存在元素的都不重复的时候add成功返回true，否则返回false。
Map与List和Set不同，它是双列存储的（键和值一一对应），它在存储元素调用的是put方法，每次存储时，要存储一份key和value，不能存储重复的key，这个
重复的规则也是利用equals进行比较。取数据的时候则可以根据key获取value。另外还是以获得所有key的集合和所有value的集合，还可以获得key和value组成
的Map.Entry对象的集合。
8、介绍一下TreeSet
（1）TreeSet的原理
Tree在存储对象的时候需要排序，但是需要指定排序的算法。
Integer和String可以自动排序（有默认算法）

{% highlight java %}
import java.util.*;  
public class TreeSetDemo1 {  
    public static void main(String[] args) {  
        Set ts = new TreeSet();  
        ts.add(new Integer(5));  
        ts.add(new Integer(10));  
        ts.add(new Integer(1));  
        ts.add(new Integer(6));  
        ts.add(new Integer(2));  
        Iterator it = ts.iterator();  
        /** 
         * 结果打印的顺序是1 2 5 6 10是按照规律的顺序排列的，这是因为Integer类实现了Comparable接口 
         * 重写了它的compareTo()方法 
         */  
        while (it.hasNext()) {  
            System.out.println(it.next());  
        }  
    }  
}  
{% endhighlight %}

注：Integer类中compareTo()方法的实现方式：

{% highlight java %}
/** 
 * Compares two {@code Integer} objects numerically. 
 * 
 * @param   anotherInteger   the {@code Integer} to be compared. 
 * @return  the value {@code 0} if this {@code Integer} is 
 *          equal to the argument {@code Integer}; a value less than 
 *          {@code 0} if this {@code Integer} is numerically less 
 *          than the argument {@code Integer}; and a value greater 
 *          than {@code 0} if this {@code Integer} is numerically 
 *           greater than the argument {@code Integer} (signed 
 *           comparison). 
 * @since   1.2 
 */  
public int compareTo(Integer anotherInteger) {  
    return compare(this.value, anotherInteger.value);  
}  
  
/** 
 * Compares two {@code int} values numerically. 
 * The value returned is identical to what would be returned by: 
 * <pre> 
 *    Integer.valueOf(x).compareTo(Integer.valueOf(y)) 
 * </pre> 
 * 
 * @param  x the first {@code int} to compare 
 * @param  y the second {@code int} to compare 
 * @return the value {@code 0} if {@code x == y}; 
 *         a value less than {@code 0} if {@code x < y}; and 
 *         a value greater than {@code 0} if {@code x > y} 
 * @since 1.7 
 */  
public static int compare(int x, int y) {  
    return (x < y) ? -1 : ((x == y) ? 0 : 1);  
}  
{% endhighlight %}

自定义的类存储的时候需要指定排序的算法，否则会出现异常。如果想把自定义的类存储到TreeSet对象中，那
么必须实现Comparable接口，重写它的compareTo()方法，在方法内定义比较大小的方法，根据大小关系，返回正数、负数或者0.
在使用TreeSet的add方法进行存储对象的时候就会自动调用compareTo()方法进行比较，根据比较结果按照二叉树的方式进行存储。

{% highlight java %}
import java.util.Iterator;  
import java.util.Set;  
import java.util.TreeSet;  
public class TreeSetDemo2 {  
    public static void main(String[] args) {  
            Set ts = new TreeSet();  
            ts.add(new Teacher("zhangsan", 1));  
            ts.add(new Teacher("lisi", 2));  
            ts.add(new Teacher("wangmazi", 3));  
            ts.add(new Teacher("wangwu",4));  
            ts.add(new Teacher("mazi", 3));  
            Iterator it = ts.iterator();  
            while (it.hasNext()) {  
                System.out.println(it.next());  
            }  
        }  
}  
class Teacher implements Comparable {  
    int num;  
    String name;  
  
    Teacher(String name, int num) {  
        this.num = num;  
        this.name = name;  
    }  
  
    public String toString() {  
        return "学号：" + num + "\t\t姓名：" + name;  
    }  
  
    //o中存放时的红黑二叉树中的节点，从根节点开始比较  
    public int compareTo(Object o) {  
        Teacher ss = (Teacher) o;  
        int result = num < ss.num ? 1 : (num == ss.num ? 0 : -1);//降序  
        //int result = num > ss.num ? 1 : (num == ss.num ? 0 : -1);//升序  
        if (result == 0) {  
            result = name.compareTo(ss.name);  
        }  
        return result;  
    }  
}  
{% endhighlight %}