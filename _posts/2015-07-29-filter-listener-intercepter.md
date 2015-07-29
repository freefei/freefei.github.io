---
layout: post
title: 过滤器、监听器、拦截器的区别
category: Web
date: 2015-07-29
---

###1.过滤器

Servlet中的过滤器Filter是实现了javax.servlet.Filter接口的服务器端程序，主要的用途是过滤字符编码、做一些业务逻辑判断等。其工作原理是，只要你在web.xml文件配置好要拦截的客户端请求，它都会帮你拦截到请求，此时你就可以对请求或响应(Request、Response)统一设置编码，简化操作；同时还可进行逻辑判断，如用户是否已经登陆、有没有权限访问该页面等等工作。它是随你的web应用启动而启动的，只初始化一次，以后就可以拦截相关请求，只有当你的web应用停止或重新部署的时候才销毁，以下通过过滤编码的代码示例来了解它的使用：

<!-- more -->

{% highlight java %}
 MyCharsetFilter.java 编码过滤器 
package ...; 
import ...; 
// 主要目的：过滤字符编码；其次，做一些应用逻辑判断等. 
// Filter跟web应用一起启动 
// 当web应用重新启动或销毁时，Filter也被销毁 
public class MyCharsetFilter implements Filter { 
     private FilterConfig config = null; 
     public void destroy() { 
         System.out.println("MyCharsetFilter准备销毁..."); 
     } 
  
     public void doFilter(ServletRequest arg0, ServletResponse arg1, FilterChain chain) throws IOException, ServletException { 
         // 强制类型转换 
         HttpServletRequest request = (HttpServletRequest)arg0; 
         HttpServletResponse response = (HttpServletResponse)arg1; 
         // 获取web.xm设置的编码集，设置到Request、Response中         request.setCharacterEncoding(config.getInitParameter("charset"));          response.setContentType(config.getInitParameter("contentType"));          response.setCharacterEncoding(config.getInitParameter("charset"));          
        // 将请求转发到目的地 
         chain.doFilter(request, response); 
     } 
  
     public void init(FilterConfig arg0) throws ServletException { 
         this.config = arg0; 
         System.out.println("MyCharsetFilter初始化..."); 
     } 
 } 
{% endhighlight%}

以下是 MyCharsetFilter.java 在web.xml 中配置: 

{% highlight xml %}
 <filter> 
       <filter-name>filter</filter-name> 
       <filter-class>dc.gz.filters.MyCharsetFilter</filter-class> 
       <init-param> 
           <param-name>charset</param-name> 
           <param-value>UTF-8</param-value> 
       </init-param> 
       <init-param> 
           <param-name>contentType</param-name> 
           <param-value>text/html;charset=UTF-8</param-value> 
       </init-param> 
   </filter> 
   <filter-mapping> 
       <filter-name>filter</filter-name> 
       <!-- * 代表截获所有的请求  或指定请求/test.do  /xxx.do --> 
       <url-pattern>/*</url-pattern> 
   </filter-mapping> 
{% endhighlight%}

###2.监听器

现在来说说Servlet的监听器Listener，它是实现了javax.servlet.ServletContextListener 接口的服务器端程序，它也是随web应用的启动而启动，只初始化一次，随web应用的停止而销毁。主要作用是： 做一些初始化的内容添加工作、设置一些基本的内容、比如一些参数或者是一些固定的对象等等。下面利用监听器对数据库连接池DataSource的初始化演示它的使用： 

{% highlight java %}
MyServletContextListener.java 
 package dc.gz.listeners; 
 import javax.servlet.ServletContext; 
 import javax.servlet.ServletContextEvent; 
 import javax.servlet.ServletContextListener; 
 import org.apache.commons.dbcp.BasicDataSource; 
  
  /** 
  * Web应用监听器 
  */ 
 public class MyServletContextListener implements ServletContextListener {   
     // 应用监听器的销毁方法 
     public void contextDestroyed(ServletContextEvent event) { 
         ServletContext sc = event.getServletContext(); 
         // 在整个web应用销毁之前调用，将所有应用空间所设置的内容清空 
         sc.removeAttribute("dataSource"); 
        System.out.println("销毁工作完成..."); 
     } 
  
     // 应用监听器的初始化方法 
     public void contextInitialized(ServletContextEvent event) { 
         // 通过这个事件可以获取整个应用的空间 
         // 在整个web应用下面启动的时候做一些初始化的内容添加工作 
         ServletContext sc = event.getServletContext(); 
         // 设置一些基本的内容；比如一些参数或者是一些固定的对象 
         // 创建DataSource对象，连接池技术 dbcp 
         BasicDataSource bds = new BasicDataSource(); 
         bds.setDriverClassName("com.mysql.jdbc.Driver");                       bds.setUrl("jdbc:mysql://localhost:3306/hibernate"); 
         bds.setUsername("root"); 
         bds.setPassword("root"); 
         bds.setMaxActive(10);//最大连接数 
         bds.setMaxIdle(5);//最大管理数 
         //bds.setMaxWait(maxWait); 最大等待时间 
         // 把 DataSource 放入ServletContext空间中， 
         // 供整个web应用的使用(获取数据库连接) 
         sc.setAttribute("dataSource", bds); 
         System.out.println("应用监听器初始化工作完成..."); 
         System.out.println("已经创建DataSource..."); 
     } 
 } 
{% endhighlight%}

web.xml中配置如下，很简单：

{% highlight xml %}
 <!-- 配置应用监听器  --> 
   <listener> 
       <listener-class>dc.gz.listeners.MyServletContextListener</listener-class> 
   </listener> 
{% endhighlight%}

这样配置好了之后，以后在web应用中就可以通过ServletContext取得BasicDataSource对象，从而获取与数据库的连接，提高性能，方便使用。

###3.拦截器

拦截器是在面向切面编程中应用的，就是在你的service或者一个方法前调用一个方法，或者在方法后调用一个方法。是基于JAVA的反射机制。拦截器不是在web.xml，比如struts在struts.xml中配置，

{% highlight java%}
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable
{
Object result = null;
System.out.println("before invoke method :" + method.getName());
result = method.invoke(this.targetObj, args);
System.out.println("after invoke method : " + method.getName());
return result;
}
{% endhighlight%}

总结：

* 1.过滤器：

所谓过滤器顾名思义是用来过滤的，在java web中，你传入的request,response提前过滤掉一些信息，或者提前设置一些参数，然后再传入servlet或者struts的action进行业务逻辑，比如过滤掉非法url（不是login.do的地址请求，如果用户没有登陆都过滤掉）,或者在传入servlet或者struts的action前统一设置字符集，或者去除掉一些非法字符（聊天室经常用到的，一些骂人的话）。filter 流程是线性的， url传来之后，检查之后，可保持原来的流程继续向下执行，被下一个filter, servlet接收等.

* 2.监听器：

这个东西在c/s模式里面经常用到，他会对特定的事件产生产生一个处理。监听在很多模式下用到。比如说观察者模式，就是一个监听来的。又比如struts可以用监听来启动。Servlet监听器用于监听一些重要事件的发生，监听器对象可以在事情发生前、发生后可以做一些必要的处理。

* 3.java的拦截器

 主要是用在插件上，扩展件上比如 hivernate spring struts2等 有点类似面向切片的技术，在用之前先要在配置文件即xml文件里声明一段的那个东西。