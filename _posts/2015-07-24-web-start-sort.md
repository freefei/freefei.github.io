---
layout: post
title: web.xml中 listener、filter、servlet等 加载顺序
category: Web
date: 2015-07-24
---

Web项目的很多信息都配置在web.xml文件中，而且项目启动时，容器(下文以Tomcat为例)会先读配置文件，那web.xml文件中的那么多内容是按照什么顺序加载的呢？
这个读取顺序跟文件内容的上下文顺序基本没有关系，也就是说这些配置信息的读取顺序是一定的，跟他写在哪个位置没关系，写到最后的可能第一个执行。

<!-- more -->


分析：

*（1）因为无论是Listener，Filter，Servlet都可能在初始化的时候用到ServletContext中的内容，而这个内容来自<context-param></context-param>配置。
所以首先在启动WEB项目的时候会先读<context-param></context-param>和<listener></listener>
<context-param></context-param>的位置可以随便写，但确是最早被加载。
在得到这个context-param的值之后,就可以做一些其他操作了，这个时候的WEB项目还没有完全启动完成，读取context-param是最早的操作，会比所有的Servlet等都要早。

*（2）然后,Tomcat会创建一个ServletContext对象,WEB项目整个Application都将共享这个ServletContext，Tomcat随后将<context-param></context-param>转化为键值对,并交给ServletContext。

*（3）Tomcat创建<listener></listener>中的类实例,即创建监听.

*（4）在监听中会有contextInitialized(ServletContextEvent args)初始化方法,在这个方法中可以获得ServletContext中的值：
ServletContext = ServletContextEvent.getServletContext()       
context-param的值 = ServletContext.getInitParameter("context-param的键")  

*（5）然后就是Filter和Servlet

所以这个大致的顺序就是：context-param -> listener -> filter -> servlet

对于某类配置节而言，与它们出现的顺序是有关的。

以 filter 为例，web.xml 中当然可以定义多个 filter，与 filter 相关的一个配置节是 filter-mapping，这里一定要注意，对于拥有相同 filter-name 的 filter 和 filter-mapping 配置节而言，filter-mapping 必须出现在 filter 之后，否则当解析到 filter-mapping 时，它所对应的 filter-name 还未定义。web Tomcat启动时初始化每个 filter 时，是按照 filter 配置节出现的顺序来初始化的，当请求资源匹配多个 filter-mapping 时，filter 拦截资源是按照 filter-mapping 配置节出现的顺序来依次调用 doFilter() 方法的。
Servlet的mapping 同 filter 类似。

关于Servlet的load-on-startup：

load-on-startup 元素在web应用启动的时候指定了servlet被加载的顺序，它的值必须是一个整数。如果它的值是一个负整数或是这个元素不存在，那么Tomcat会在该servlet被调用的时候，加载这个servlet 。如果值是正整数或零，Tomcat在加载配置的时候就加载并初始化这个servlet，Tomcat必须保证值小的先被加载。如果值相等，Tomcat可以自动选择先加载谁。

在servlet的配置当中，<load-on-startup>0</load-on-startup>的含义是：

（1）标记Tomcat是否在启动的时候就加载这个servlet。

（2）当值为0或者大于0时，表示Tomcat在应用启动时就加载这个servlet；

（3）当是一个负数时或者没有指定时，则指示Tomcat在该servlet被选择时才加载。

（4）正数的值越小，启动该servlet的优先级越高。

最终结论：

web.xml 的加载顺序是：[context-param -> listener -> filter -> servlet -> spring] ，而同类型节点之间的实际程序调用的时候的顺序是根据对应的 mapping 的顺序进行调用的。


参考文件：http://hangzhou492.blog.51cto.com/67690/750671
