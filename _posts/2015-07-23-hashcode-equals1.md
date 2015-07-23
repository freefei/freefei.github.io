---
layout: post
title: Java 中正确使用hashCode和equals方法 一
category: JAVA
date: 2015-07-23
---
转自：http://www.oschina.net/question/82993_75533

在这篇文章中，我将告诉大家我对hashCode和equals方法的理解。我将讨论他们的默认实现，以及如何正确的重写他们。我也将使用Apache Commons提供的工具包做一个实现。

目录：

hashCode()和equals()的用法
重写默认实现
使用Apache Commons Lang包重写hashCode()和equals()
需要注意记住的事情
当使用ORM的时候特别要注意的
hashCode()和equals()定义在Object类中，这个类是所有java类的基类，所以所有的java类都继承这两个方法。

<!-- more -->


使用hashCode()和equals()

hashCode()方法被用来获取给定对象的唯一整数。这个整数被用来确定对象被存储在HashTable类似的结构中的位置。默认的，Object类的hashCode()方法返回这个对象存储的内存地址的编号。

重写默认的实现

如果你不重写这两个方法，将几乎不遇到任何问题，但是有的时候程序要求我们必须改变一些对象的默认实现。

来看看这个例子，让我们创建一个简单的类Employee

{% highlight java%}

01	public class Employee
02	{
03	    private Integer id;
04	    private String firstname;
05	    private String lastName;
06	    private String department;
07	 
08	    public Integer getId() {
09	        return id;
10	    }
11	    public void setId(Integer id) {
12	        this.id = id;
13	    }
14	    public String getFirstname() {
15	        return firstname;
16	    }
17	    public void setFirstname(String firstname) {
18	        this.firstname = firstname;
19	    }
20	    public String getLastName() {
21	        return lastName;
22	    }
23	    public void setLastName(String lastName) {
24	        this.lastName = lastName;
25	    }
26	    public String getDepartment() {
27	        return department;
28	    }
29	    public void setDepartment(String department) {
30	        this.department = department;
31	    }
32	}
上面的Employee类只是有一些非常基础的属性和getter、setter.现在来考虑一个你需要比较两个employee的情形。

01	public class EqualsTest {
02	    public static void main(String[] args) {
03	        Employee e1 = new Employee();
04	        Employee e2 = new Employee();
05	 
06	        e1.setId(100);
07	        e2.setId(100);
08	        //Prints false in console
09	        System.out.println(e1.equals(e2));
10	    }
11	}
毫无疑问，上面的程序将输出false，但是，事实上上面两个对象代表的是通过一个employee。真正的商业逻辑希望我们返回true。 
为了达到这个目的，我们需要重写equals方法。

01	public boolean equals(Object o) {
02	        if(o == null)
03	        {
04	            return false;
05	        }
06	        if (o == this)
07	        {
08	           return true;
09	        }
10	        if (getClass() != o.getClass())
11	        {
12	            return false;
13	        }
14	        Employee e = (Employee) o;
15	        return (this.getId() == e.getId());
16	}
在上面的类中添加这个方法，EauqlsTest将会输出true。 
So are we done?没有，让我们换一种测试方法来看看。

01	import java.util.HashSet;
02	import java.util.Set;
03	 
04	public class EqualsTest
05	{
06	    public static void main(String[] args)
07	    {
08	        Employee e1 = new Employee();
09	        Employee e2 = new Employee();
10	 
11	        e1.setId(100);
12	        e2.setId(100);
13	 
14	        //Prints 'true'
15	        System.out.println(e1.equals(e2));
16	 
17	        Set<Employee> employees = new HashSet<Employee>();
18	        employees.add(e1);
19	        employees.add(e2);
20	        //Prints two objects
21	        System.out.println(employees);
22	    }
上面的程序输出的结果是两个。如果两个employee对象equals返回true，Set中应该只存储一个对象才对，问题在哪里呢？ 
我们忘掉了第二个重要的方法hashCode()。就像JDK的Javadoc中所说的一样，如果重写equals()方法必须要重写hashCode()方法。我们加上下面这个方法，程序将执行正确。

1	@Override
2	 public int hashCode()
3	 {
4	    final int PRIME = 31;
5	    int result = 1;
6	    result = PRIME * result + getId();
7	    return result;
8	 }
使用Apache Commons Lang包重写hashCode() 和equals()方法 
Apache Commons 包提供了两个非常优秀的类来生成hashCode()和equals()方法。看下面的程序。


01	import org.apache.commons.lang3.builder.EqualsBuilder;
02	import org.apache.commons.lang3.builder.HashCodeBuilder;
03	public class Employee
04	{
05	 private Integer id;
06	 private String firstname;
07	 private String lastName;
08	 private String department;
09	public Integer getId() {
10	    return id;
11	 }
12	 public void setId(Integer id) {
13	    this.id = id;
14	 }
15	 public String getFirstname() {
16	    return firstname;
17	 }
18	 public void setFirstname(String firstname) {
19	    this.firstname = firstname;
20	 }
21	 public String getLastName() {
22	    return lastName;
23	 }
24	 public void setLastName(String lastName) {
25	    this.lastName = lastName;
26	 }
27	 public String getDepartment() {
28	    return department;
29	 }
30	 public void setDepartment(String department) {
31	    this.department = department;
32	 }
33	@Override
34	 public int hashCode()
35	 {
36	    final int PRIME = 31;
37	    return new HashCodeBuilder(getId()%2==0?getId()+1:getId(), PRIME).
38	           toHashCode();
39	 }
40	@Override
41	 public boolean equals(Object o) {
42	    if (o == null)
43	       return false;
44	    if (o == this)
45	       return true;
46	    if (o.getClass() != getClass())
47	       return false;
48	    Employee e = (Employee) o;
49	       return new EqualsBuilder().
50	              append(getId(), e.getId()).
51	              isEquals();
52	    }
53	 }

{% endhighlight%}


### 需要注意记住的事情

尽量保证使用对象的同一个属性来生成hashCode()和equals()两个方法。在我们的案例中,我们使用员工id。
eqauls方法必须保证一致（如果对象没有被修改，equals应该返回相同的值）
任何时候只要a.equals(b),那么a.hashCode()必须和b.hashCode()相等。
两者必须同时重写。
当使用ORM的时候特别要注意的

如果你使用ORM处理一些对象的话，你要确保在hashCode()和equals()对象中使用getter和setter而不是直接引用成员变量。因为在ORM中有的时候成员变量会被延时加载，这些变量只有当getter方法被调用的时候才真正可用。
例如在我们的例子中，如果我们使用e1.id == e2.id则可能会出现这个问题，但是我们使用e1.getId() == e2.getId()就不会出现这个问题。



