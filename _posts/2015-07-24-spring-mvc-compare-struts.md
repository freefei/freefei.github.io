---
layout: post
title: spring mvc compare struts
category: Spring MVC
date: 2015-07-24
---

（1）、 机制 
spring mvc的入口是servlet，而struts2是filter，这样就导致了二者的机制不同。

（2）、Spring的整个MVC配置是基于IOC容器的

　　与struts相比， Spring MVC的配置文件，最先看到的不是action或者form，而是一些有着特定名字的bean，Bean下面的配置是一些简单或有点复杂的属性。我们看到的是机器更容易的数据结构，而不是人更容易理解的元素。
　　但是这恰恰是Spring的MVC强大的根源!因为它的配置就是Spring的核心IOC容器的配置，这意味着所有IOC容器的威力都可以在这里展现，我们可以为所欲为地对Spring MVC进行扩展和增强，我们可以完成在其它MVC framwork中很多难以想象的任务。想扩展新的URL映射方式吗?要换一个themeResolver或LocalReolver的实现吗?想在页面中显示新类型的View甚至想直接在Controller里定义AOP吗?这些对Spring的MVC来说都是可实现的。 
　　Spring采用IOC配置的另一个原因是使Spring的MVC与Spring的IOC容器的整合变得非常的容易。Spring提供了与struts的整合，但是这样整合都需要在进行间接的包装，感觉总不是很自然 
采用IOC容器来实现的另一个原因是这会减少好多开发工作量。假如我们对Spring的IOC容器非常熟悉的话，会发现它非常的亲切，也非常的简单。  

<!-- more -->

　　（3）、Spring提供了明确的Model, View概念和相应的数据结构

　　在Spring里有一个有趣的数据类型叫做ModelAndView，它只是简单地把要显示的数据和显示的结果封装在一个类里。但是它却提供了明确的MVC概念，尤其是model概念的强化，使程序的逻辑变得更清晰了。
　　在Struts里写程序里的时候，为了显示数据经常自己把东西放到HttpSession或HttpServletRequest里(或set到form里，虽然不太有用)，这造成了model概念的模糊，而且也导致了struts与JSP页面的紧耦合。 

（4）、 性能 

spring会稍微比struts快。
struts2框架是类级别的拦截。每次来了请求就创建一个Action，然后调用setter getter方法把request中的数据注入,他实际上是通过setter getter方法与request打交道的,struts2中，一个Action对象对应一个request上下文 
spring3 mvc不同，spring3mvc是方法级别的拦截。拦截到方法后根据参数上的注解，把request数据注入进去，在spring3mvc中，一个方法对应一个request上下文。 
另外，spring3 mvc的方法之间基本上独立的，独享request response数据，请求数据通过参数获取，处理结果通过ModelMap交回给框架，方法之间不共享变量，而struts2就比较乱，虽然方法之间也是独立的，但其所有Action变量是共享的，这不会影响程序运行，却给我们编码，读程序时带来麻烦。

（5）、设计思想上

struts更加符合oop的编程思想， spring就比较谨慎，在servlet上扩展。　　

（6）、Spring的Controller是Singleton的，或者是线程不安全的
和Struts1一样，Spring的Controller是Singleton的，这意味着每个request过来，系统都会用原有的instance去处理，这样导致了两个结果:我们不用每次创建Controller，减少了对象创建和垃圾收集的时间;由于只有一个Controller的instance，当多个线程调用它的时候，它里面的instance变量不是线程安全的。
而Struts2是线程安全的。   

　　（7）、Spring没有像Struts2那样去隐藏Servlet相关的元素如HttpServletRequest或HttpServletResponse
　　在Struts2里我们可以没有HttpServletRequest或者HttpServletResponse，只有getter, setter或ActionContext里数据，这样的结果导致一个干净的Action，一个与Web完全无关的Action，一个可以在任何环境下独立运行的bean。这样的action的好处在上篇文章里也说过，大概为:

　　 1、它使我们的Action可以非常容易地被测试。

　　 2、用户可以在Action里添加业务逻辑，并被其它类重用。　
　
然而跟Spring比较一下，我们就会发现这两点功能所带来的好处其实并不象我们想象的那么显著。Spring的Controller类也可以非常轻松被测试，因为他的spring-mock下面的包里提供的MockHttpServletRequest, MockHttpServletResponse还有其它一些类让测试Controller变得异常轻松。再看一下Action里的业务逻辑吧，多数人都会业务逻辑delegate（委派)给另一个Service类或Manager类。因为我们很清楚，往Action里加业务逻辑会使整个体系的分层架构变得不清晰，不管怎样，Web层就是Web层，业务层就是业务层，两者的逻辑混在一起总会带来问题的。而且往Action里加业务逻辑会使用这个Action类变得庞大，所以业务问题在spring mvc是也不是问题。　

（8）、 intercepter的实现机制

struts有以自己的interceptor机制，spring mvc用的是独立的AOP方式。这样导致struts的配置文件量还是比spring mvc大，虽然struts的配置能继承，所以我觉得论使用上来讲，spring mvc使用更加简洁，开发效率Spring MVC确实比struts2高。
　　
（9）、Spring提供了更多的选择方式

　　 Spring里提供的Controller，提供了好多不同的Controller类。要生成Wizard吗?要专门用于提交form的Controller吗?要执多个方法的类吗?Spring提供了丰富的子类来扩展这些选择。当然我们还可以很轻松地自己扩展这些功能。
再看看Spring的ViewResolver吧， 它提供了无数不同类型的ViewResolver。更重要的是我们自定义我们的页面映射方式。而strtus1会存在页面与forward name的一层间接转换，我们必须在配置文件里配置好某个字符串(典型的是success)对应的是那个页面。但是Spring里我们有了更大的自由度，如将JSP文件名去掉扩展名的映射方法。 
　　
（10）、Spring的tag 

　　尽管Spring的tag数量很少，但它却是精心设计的。它的目标很简单:让美工可以轻松地编辑页面。因为在Spring的页面里Text仍然是Text，checkbox仍然是CheckBox，而不象在struts中的Tag。它只是用Springbind对输入内容进行了一下包装。

另外，spring3 mvc的验证也是一个亮点，支持JSR303，处理ajax的请求更是方便，只需一个注解@ResponseBody ，然后直接返回响应文本即可。


转自 http://blog.csdn.net/wanghuan203/article/details/8623048
