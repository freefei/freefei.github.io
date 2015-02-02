---
layout: post
title: java注解annotation
category: JAVA
date: 2015-02-02
duoshuo: true
tags : [annotation]
---

**一、什么是注解**

说起注解，得先提一提什么是元数据(metadata)。所谓元数据就是数据的数据。也就是说，元数据是描述数据的。就象数据表中的字段一样，每个字段描述了这个字段下的数据的含义。而J2SE5.0中提供的注解就是java源代码的元数据，也就是说注解是描述java源代码的。在J2SE5.0中可以自定义注解。使用时在@后面跟注解的名字。

<!-- more --> 

 

**二、J2SE5.0中预定义的注解**

在J2SE5.0的java.lang包中预定义了三个注解。它们是Override、Deprecated和SuppressWarnings。下面分别解解它们的含义。

 **Override**

这个注解的作用是标识某一个方法是否覆盖了它的父类的方法。那么为什么要标识呢？让我们来看看如果不用Override标识会发生什么事情。

假设有两个类Class1和ParentClass1，用Class1中的myMethod1方法覆盖ParentClass1中的myMethod1方法。

{% highlight java %}
 class ParentClass1
 {
     public void myMethod1() {}
 }

 class Class1 extends ParentClass1
 {
     public void myMethod2() {}        
 }
 {% endhighlight %}

 


 建立Class1的实例，并且调用myMethod1方法

{% highlight java %}
    ParentClass1 c1 = new Class1();

    c1.myMethod1();
{% endhighlight %}

以上的代码可以正常编译通过和运行。但是在写Class1的代码时，误将myMethod1写成了myMethod2，然而在调用时，myMethod1并未被覆盖。因此，c1.myMethod1()调用的还是ParentClass1的myMethod1方法。更不幸的是，程序员并未意识到这一点。因此，这可能会产生bug。

如果我们使用Override来修饰Class1中的myMethod1方法，当myMethod1被误写成别的方法时，编译器就会报错。因此，就可以避免这类错误。

 
{% highlight java %}
class Class1 extends ParentClass1
{
    @Override   // 编译器产生一个错误
    public void myMethod2() {}        
}
{% endhighlight %}

以上代码编译不能通过，被Override注解的方法必须在父类中存在同样的方法程序才能编译通过。也就是说只有下面的代码才能正确编译。
{% highlight java %}
class Class1 extends ParentClass1
{
    @Override
    public void myMethod1() {}        
}
{% endhighlight %}
 


**Deprecated**


这个注解是一个标记注解。所谓标记注解，就是在源程序中加入这个标记后，并不影响程序的编译，但有时编译器会显示一些警告信息。

那么Deprecated注解是什么意思呢？如果你经常使用eclipse等IDE编写java程序时，可能会经常在属性或方法提示中看到这个词。如果某个类成员的提示中出现了个词，就表示这个并不建议使用这个类成员。因为这个类成员在未来的JDK版本中可能被删除。之所以在现在还保留，是因为给那些已经使用了这些类成员的程序一个缓冲期。如果现在就去了，那么这些程序就无法在新的编译器中编译了。

**SuppressWarnings**


这个世界的事物总是成对出现。即然有使编译器产生警告信息的，那么就有抑制编译器产生警告信息的。

SuppressWarnings注解就是为了这样一个目的而存在的。让我们先看一看如下的代码。

 
{% highlight java %}
public void myMethod()
{
    List wordList = new ArrayList();
    wordList.add("foo");
}
{% endhighlight %}
 


这是一个类中的方法。编译它，将会得到如下的警告。

注意：Testannotation.java 使用了未经检查或不安全的操作。

注意：要了解详细信息，请使用 -Xlint:unchecked 重新编译。

这两行警告信息表示List类必须使用范型才是安全的，才可以进行类型检查。如果想不显示这个警告信息有两种方法。一个是将这个方法进行如下改写：

{% highlight java %}
public void myMethod()
{
    List<String> wordList = new ArrayList<String>();
        wordList.add("foo");
}
{% endhighlight %}

另外一种方法就是使用@SuppressWarnings。

{% highlight java %}
@SuppressWarnings (value={"unchecked"})
public void myMethod()
{
    List wordList = new ArrayList();
    wordList.add("foo");
}
{% endhighlight %}
 
要注意的是SuppressWarnings和前两个注解不一样。这个注解有一个属性。当然，还可以抑制其它警告，如@SuppressWarnings(value={"unchecked", "fallthrough"})

**三、如何自定义注解**

注解的强大之处是它不仅可以使java程序变成自描述的，而且允许程序员自定义注解。注解的定义和接口差不多，只是在interface前面多了一个“@”。

{% highlight java %}
public @interface MyAnnotation

{

}
{% endhighlight %}

上面的代码是一个最简单的注解。这个注解没有属性。也可以理解为是一个标记注解。就象Serializable接口一样是一个标记接口，里面未定义任何方法。

当然，也可以定义而有属性的注解。
{% highlight java %}
public @interface MyAnnotation

{
    String value();
}
{% endhighlight %}
 

可以按如下格式使用MyAnnotation

{% highlight java %}
@MyAnnotation(“abc”)

public void myMethod()

{
}
{% endhighlight %}
 

看了上面的代码，大家可能有一个疑问。怎么没有使用value，而直接就写”abc”了。那么”abc”到底传给谁了。其实这里有一个约定。如果没有写属性名的值，而这个注解又有value属性，就将这个值赋给value属性，如果没有，就出现编译错误。

除了可以省略属性名，还可以省略属性值。这就是默认值。

{% highlight java %}
public @interface MyAnnotation

{
    public String myMethod() default "xyz";
}
{% endhighlight %}
 

可以直接使用MyAnnotation
@MyAnnotation // 使用默认值xyz

{% highlight java %}
public void myMethod()

{

}
{% endhighlight %}
 

也可以这样使用

{% highlight java %}
@MyAnnotation(myMethod=”abc”)

public void myMethod()

{

}
{% endhighlight %}
 

如果要使用多个属性的话。可以参考如下代码。

{% highlight java %}
public @interface MyAnnotation
{
    public enum MyEnum{A, B, C}
    public MyEnum value1();
    public String value2();

}

@MyAnnotation(value1=MyAnnotation.MyEnum.A, value2 = “xyz”)
public void myMethod()
{
}
{% endhighlight %}
 


这一节讨论了如何自定义注解。那么定义注解有什么用呢？有什么方法对注解进行限制呢？我们能从程序中得到注解吗？这些疑问都可以从下面的内容找到答案。


**四、如何对注解进行注解**

这一节的题目读起来虽然有些绕口，但它所蕴涵的知识却对设计更强大的java程序有很大帮助。

在上一节讨论了自定义注解，由此我们可知注解在J2SE5.0中也和类、接口一样。是程序中的一个基本的组成部分。既然可以对类、接口进行注解，那么当然也可以对注解进行注解。

使用普通注解对注解进行注解的方法和对类、接口进行注解的方法一样。所不同的是，J2SE5.0为注解单独提供了4种注解，即元注解。

元注解理解：
注解可以用于注解类（annotate Classes）
可以用于注解接口(annotate Interfaces)
可以用于注解枚举类型(annotate Enums)
因此注解同样也可以用于注解注解（annotate Annotations）
它们是Target、Retention、Documented和Inherited。下面就分别介绍这4种注解。

 

**Target**

这个注解理解起来非常简单。由于target的中文意思是“目标”，因此，我们可能已经猜到这个注解和某一些目标相关。那么这些目标是指什么呢？大家可以先看看下面的代码。

{% highlight java %}
@Target({ElementType.METHOD})
@interface MyAnnotation {}

@MyAnnotation         // 错误的使用
public class Class1
{
    @MyAnnotation        // 正确的使用
    public void myMethod1() {}
}
{% endhighlight %}
 


以上代码定义了一个注解MyAnnotation和一个类Class1，并且使用MyAnnotation分别对Class1和myMethod1进行注解。如果编译这段代码是无法通过的。也许有些人感到惊讶，没错啊！但问题就出在@Target({ElementType.METHOD})上，由于Target使用了一个枚举类型属性，它的值是ElementType.METHOD。这就表明MyAnnotation只能为方法注解。而不能为其它的任何语言元素进行注解。因此，MyAnnotation自然也不能为Class1进行注解了。

 

说到这，大家可能已经基本明白了。原来target所指的目标就是java的语言元素。如类、接口、方法等。当然，Target还可以对其它的语言元素进行限制，如构造函数、字段、参数等。如只允许对方法和构造函数进行注解可以写成：

{% highlight java %}
@Target({ElementType.METHOD,  ElementType.CONSTRUCTOR})

@interface MyAnnotation {}
{% endhighlight %}
 

**Retention**

既然可以自定义注解，当然也可以读取程序中的注解（如何读取注解将在下一节中讨论）。但是注解只有被保存在class文件中才可以被读出来。而Retention就是为设置注解是否保存在class文件中而存在的。下面的代码是Retention的详细用法。

{% highlight java %}
@Retention(RetentionPolicy.SOURCE)

@interface MyAnnotation1 { }

@Retention(RetentionPolicy.CLASS)

@interface MyAnnotation2 {}

@Retention(RetentionPolicy.RUNTIME)

@interface MyAnnotation3 {}
{% endhighlight %}

其中第一段代码的作用是不将注解保存在class文件中，也就是说象“//”一样在编译时被过滤掉了。第二段代码的作用是只将注解保存在class文件中，而使用反射读取注解时忽略这些注解。第三段代码的作用是即将注解保存在class文件中，也可以通过反射读取注解。

 

**Documented**

这个注解和它的名子一样和文档有关。在默认的情况下在使用javadoc自动生成文档时，注解将被忽略掉。如果想在文档中也包含注解，必须使用Documented为文档注解。

{% highlight java %}
@interface MyAnnotation{ }

@MyAnnotation
class Class1
{    
    public void myMethod() { }
}
使用javadoc为这段代码生成文档时并不将@MyAnnotation包含进去。生成的文档对Class1的描述如下：

 class Class1 extends java.lang.Object

 

而如果这样定义MyAnnotation将会出现另一个结果。

@Documented

@interface MyAnnotation {}

生成的文档：

@MyAnnotation // 这行是在加上@Documented后被加上的

class Class1 extends java.lang.Object

{% endhighlight %}

**Inherited**

继承是java主要的特性之一。在类中的protected和public成员都将会被子类继承，但是父类的注解会不会被子类继承呢？很遗憾的告诉大家，在默认的情况下，父类的注解并不会被子类继承。如果要继承，就必须加上Inherited注解。

{% highlight java %}
@Inherited
@interface MyAnnotation { }

@MyAnnotation
public class ParentClass {}

public class ChildClass extends ParentClass { }
{% endhighlight %}

在以上代码中ChildClass和ParentClass一样都已被MyAnnotation注解了。

**五、如何使用反射读取注解**

前面讨论了如何自定义注解。但是自定义了注解又有什么用呢？这个问题才是J2SE5.0提供注解的关键。自定义注解当然是要用的。那么如何用呢？解决这个问题就需要使用java最令人兴奋的功能之一：反射(reflect)。

在以前的JDK版本中，我们可以使用反射得到类的方法、方法的参数以及其它的类成员等信息。那么在J2SE5.0中同样也可以象方法一样得到注解的各种信息。

在使用反射之前必须使用import java.lang.reflect.* 来导入和反射相关的类。

如果要得到某一个类或接口的注解信息，可以使用如下代码：

{% highlight java %}
Annotation annotation = TestAnnotation.class.getAnnotation(MyAnnotation.class);
{% endhighlight %}

如果要得到全部的注解信息可使用如下语句：

{% highlight java %}
Annotation[] annotations = TestAnnotation.class.getAnnotations();

或

Annotation[] annotations = TestAnnotation.class.getDeclaredAnnotations();
{% endhighlight %}

getDeclaredAnnotations与getAnnotations类似，但它们不同的是getDeclaredAnnotations得到的是当前成员所有的注解，不包括继承的。而getAnnotations得到的是包括继承的所有注解。

 

如果要得到其它成员的注解，可先得到这个成员，然后再得到相应的注解。如得到myMethod的注解。

{% highlight java %}
Method method = TestAnnotation.class.getMethod("myMethod", null);

Annotation annotation = method.getAnnotation(MyAnnotation.class);
{% endhighlight %}

注：要想使用反射得到注解信息，这个注解必须使用

@Retention(RetentionPolicy.RUNTIME)进行注解。

 

**六、注解的本质：注解是一种类型**

JDK5.0中的类型：1、类（class）2、接口（interface）3、枚举（enum）4、注解（Annotation）
因此，注解与其他3种类型一样，都可以定义、使用，以及包含有自己的属性、方法
 

**七、注解的分类：**

* （1）标记注释：注解的内部没有属性，称作标记注解
使用方法：@注解名
使用例子：@MarkAnnotation
 
* （2）单值注解：注解的内部只有一个属性，称作单值注解
使用方法：@注解名(属性名=属性值)
使用例子：@SingleAnnotation(value="abc")  //也可以写成@SingleAnnotation("abc")
*(属性名=属性值)可以简化为(属性值)，但是需要满足以下两个条件：
1、该注解必须为单值注解
2、该注解的属性名必须为value
 
* （3）多值注解：注解的内部有多个属性，称作多值注解
使用方法：@注解名(属性名1=属性值1, 属性名2=属性值2……)
使用例子：@MultipliedAnnotation(value1 = "abc", value2 = 30……)

**八、自动测试机的写法：**

自动测试机的原理：
使用Annotation来Annotate元素的实质是：每一个ElementType内部的元素都有两个方法，分别为
（注：为方便理解，以下使用的TestCase为某个特定的自定义注释）
（1）isAnnotationPresent(TestCase.class)  //判断该元素是否被TestCase所注释
（2）getAnnotation(TestCase.class)  //获得TestCase的类对象
 
因此，自动测试机的工作过程是：
（1）首先通过反射，获得被测类o中的每一个方法
（2）对每一个方法通过使用isAnnotationPresent(TestCase.class)判断其是否被TestCase所注释（注意是.class！）
（3）如果某方法method被TestCase所注释，则通过method的getAnnotation(TestCase.class)获得TestCase的类对象tc
（4）通过tc的value()方法，获得该类对象的属性value
（注：此处使用的value()方法，正是在TestCase中定义的value属性，再次理解在注释中定义的value既是属性，也是方法）
（5）调用method方法的invoke(o,value)，用value对method进行测试
 
**九、注解使用实例：**

要求：
* （1）定义一个单值注解TestCase，使其可以注解方法，并且可以被保留到程序运行时
注解的属性类型为String，要求可以使用简写方式为属性赋值
* （2）定义一个类MyClass，要求有三个方法Method1、2、3
方法的参数、返回值类型均为String类型，返回值为传入的参数
使用（1）中的注解来注释Method1、3，并对属性参数赋值
* （3）定义一个测试类TestMyClass，要求使用反射来测试MyClass中所有的被TestCase注解的方法
并将注解的属性值作为参数，调用相应方法来返回测试结果
 
例子：

{% highlight java %}
---------------------------1---------------------------
 
import java.lang.annotation.*;
 
@Target ({ElementType.METHOD})
@Retention (RetentionPolicy.RUNTIME)
 
public @interface TestCase {
  String value();
}
 
---------------------------2---------------------------
 
public class MyClass {
  @TestCase("This is Method 1")
  public String Method1 (String s) {
    return s;
  }
 
  public String Method2 (String s) {
    return s;
  }
 
  @TestCase("This is Method 3")
  public String Method3 (String s) {
    return s;
  }
}
 
---------------------------3---------------------------
 
import java.lang.reflect.*;
 
public class TestMyClass {
  public static void main(String [] args) {
    Class c = Class.forName("MyClass");
    Method [] ms = c.getDeclaredMethods();
    for (Method m : ms) {
      if(m.isAnnotationPresent(TestCase.class) {
        TestCase tc = m.getAnnotation(TestCase.class);
        Object o = c.newInstance();
        String s = tc.value();
        m.invoke(o, s);
        或者以上三句可以直接写成：
        m.invoke(c.newInstace, tc.value());
      }
    }
  }
}
{% endhighlight %}

总结

注解是J2SE5.0提供的一项非常有趣的功能。它不但有趣，而且还非常有用。如即将出台的EJB3.0规范就是借助于注解实现的。这样将使EJB3.0在实现起来更简单，更人性化。还有Hibernate3.0除了使用传统的方法生成hibernate映射外，也可以使用注解来生成hibernate映射。总之，如果能将注解灵活应用到程序中，将会使你的程序更加简洁和强大。

