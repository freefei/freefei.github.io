---
layout: post
title: js预解析理解
category: 前端
date: 2015-02-01
duoshuo: true
tags : [js]
---

函数预解析：

* 1、javascript在执行前会进行类似“预解析”的操作：首先会创建一个在当前执行环境下的活动对象，并将那些用var 声明的变量、定义的函数设置为活动对象的属性 ，但是此时这些变量的赋值都是undefined。
 
* 2、在javascript解释执行阶段，遇到变量需要解析时，会首先从当前执行环境的活动对象中查找，如果没有找到‍ 而且该执行环境的拥有者有prototype属性时则会从prototype链中查找，否则将会按照作用域链查找。 遇到var a = …这样的语句时会给相应的变量进行赋值（注意：变量的赋值是在解释执行阶段完成的，如果在这之前使用变量，它的值会是undefined）。

<!-- more -->

例子1：
Java代码  收藏代码
{% highlight js %}

function handle(){        
     alert(arg1);         
   } ;    
       
   handle();         
   var arg1 = 20;  

 {% endhighlight%}
 结果是：undefined。因为在解释到 var arg1 = 20; 这句之前就打印了arg1的值，此时尚未给arg1赋值。
 
例子2：
Java代码  收藏代码
{% highlight js %}
alert(handle);      
var handle = function (){   
       alert(20);       
};   
 结果：undefined
 
  {% endhighlight%}
Java代码  收藏代码
{% highlight js %}
handle();  
  
var handle=function (){  
      alert("输出");  
}  
  {% endhighlight%}
 结果：handle is not a function. 因为在执行handle()这句时，并没有给handle赋值–函数定义。如果改为：
Java代码  收藏代码
{% highlight js %}
handle();      
var handle = function handle (){   
       alert(20);       
};   
  {% endhighlight%}
在IE下会弹出对话框，因为它将var handle…这句同时解释为函数定义，而函数定义在预编译时就应经有值，所以可以执行。但在FF中，依然只解释为一个变量申明，知道执行到这一句时才会赋值。
正因为如此应避免在变量被初始化之前使用变量。
 
 
JavaScript是解释型语言是毋庸置疑的，但它是不是仅在运行时自上往下一句一句地解析的呢？事实上或某种现象证明并不是这样的，通过 《JavaScript权威指南》及网上相关资料了解到，JavaScript有“预解析”行为。理解这一特性是很重要的，不然在实际开发中你可能会遇到 很多无从解析的问题，甚至导致程序bug的存在。为了解析这一现象，也作为自己的一次学习总结，本文逐步引导你来认识JavaScript“预解析”，如 果我的见解有误，还望指正。

*（1）如果JavaScript仅是运行时自上往下逐句解析的，下面的代码能正确运行是可以理解的，因为我们先定义函数，然后才调用它。
Java代码  收藏代码
{% highlight js %}
function showMsg()  
{  
    alert('This is message');  
}  
showMsg(); // This is message  
   {% endhighlight%}
*（2）我们也知道函数可以定义在调用代码之后，如下代码也是能正常工作的。看起来调用showMsg()的时候showMsg()还是没有定义的，但能正常工作，则表明JavaScript是“预解析”的。
Java代码  收藏代码
{% highlight js %}
showMsg(); // This is message  
function showMsg()  
{  
    alert('This is message');  
}  
   {% endhighlight%}
*（3）上面是函数的例子，下面再来一个普通变量的例子。以下例子运行将会弹出undefined，表明第一句的msg已经是定义了，只是没有初始 化，它与var msg; alert(msg);是一样的。如果你把下面第二句注释掉，则会报“msg未定义”错误。这亦表明JavaScript是“预解析”的。
Java代码  收藏代码
{% highlight js %}
alert(msg); //undefined  
var msg='This is message';  
    {% endhighlight%}
*（4）再来看一个例子，加深对JavaScript“预解析”印象。以下代码你将看到两次弹出的对话框都是显示This is message 2，为什么会这样呢？其实下面一前一后定义了两个同名函数，后面的showMsg()覆盖了前面定义的（在JavaScript中，同名变量一样会存在覆 盖问题 ），等于第一个showMsg()报废了。为什么第二次调用的showMsg()不是调用它上面定义的那个message 1函数呢？这再次证明JavaScript有“预解析”行为。
Java代码  收藏代码
{% highlight js %}
showMsg(); // This is message 2  
function showMsg()  
{  
    alert('This is message 1');  
}  
showMsg(); // This is message 2  
function showMsg()  
{  
    alert('This is message 2');  
}  
     {% endhighlight%}
*（5）JavaScript“预解析”是把变量或函数预解析到它们能调用的环境（变量运行时环境）中。如下代码看起来alert(msg)之前有看 到msg的定义，但是程序运行还是报“msg未定义”错误，这是因为 函数里定义的变量是函数的私有变量，外面不能直接调用 ，这表明JavaScript “预解析”并不是把所有定义的变量统一解析到一个全局对象中 ，比如window。
Java代码  收藏代码
{% highlight js %}
function showMsg()  
{  
    var msg='This is message';  
}  
alert(msg); // msg未定义  
 {% endhighlight%}
*（6）JavaScript“预解析”是分段进行的，准确说是分
{% highlight html %} <script> {% endhighlight %}
块进行的。以下代码出现在同一个页面的两个脚本块中， 同时定义了三个同名函数。程序运行结果表明第二个
{% highlight js %} <script> {% endhighlight %}
脚本块的showMsg()没有覆盖前面两个showMsg()，而第一个脚本块的第二个 showMsg()则覆盖了第一个showMsg()。

Java代码  收藏代码
{% highlight html %}
<body>  
<script type="text/javascript">  
showMsg(); //This is message 2  
function showMsg()  
{  
    alert('This is message 1');  
}  
function showMsg()  
{  
    alert('This is message 2');  
}  
</script>  
  
<script type="text/javascript">  
function showMsg()  
{  
    alert('This is message 3');  
}  
</script>  
</body>  
 {% endhighlight%}
 来源：[http://archive.cnblogs.com/a/1984625/](http://archive.cnblogs.com/a/1984625/)