---
layout: post
title: Servlet面试题归纳
category: JAVA
date: 2015-07-01
duoshuo: true
tags : [面试]
---

1、说一说Servlet生命周期
Servlet生命周期包括三部分：
初始化：Web容器加载servlet，调用init()方法
处理请求：当请求到达时，运行其service()方法。service()自动派遣运行与请求相对应的doXXX（doGet或者doPost）方法。
销毁：服务结束，web容器会调用servlet的distroy()方法销毁servlet。
2、get提交和post提交有何区别
（1）get一般用于从服务器上获取数据，post一般用于向服务器传送数据
（2）请求的时候参数的位置有区别，get的参数是拼接在url后面，用户在浏览器地址栏可以看到。post是放在http包的包体中。
比如说用户注册，你不能把用户提交的注册信息用get的方式吧，那不是说把用户的注册信息都显示在Url上了吗，是不安全的。
（3）能提交的数据有区别，get方式能提交的数据只能是文本，且大小不超过1024个字节，而post不仅可以提交文本还有二进制文件。
所以说想上传文件的话，那我们就需要使用post请求方式
（4）servlet在处理请求的时候分别对应使用doGet和doPost方式进行处理请求
3、JSP与Servlet有什么区别
Servlet是服务器端的程序，动态生成html页面发送到客户端，但是这样程序里会有很多out.println(),java与html语言混在一起
很乱，所以后来sun公司推出了JSP.其实JSP就是Servlet，每次运行的时候JSP都首先被编译成servlet文件，然后再被编译成
.class文件运行。有了jsp，在MVC项目中servlet不再负责动态生成页面，转而去负责控制程序逻辑的作用，控制jsp与javabean
之间的流转。
4、doGet与doPost方法的两个参数是什么
HttpServletRequest：封装了与请求相关的信息
HttpServletResponse：封装了与响应相关的信息
5、request.getAttribute()和request.getParameter
（1）有setAttribute，没有setParameter方法
（2）getParameter获取到的值只能是字符串，不可以是对象，而getAttribute获取到的值是Object类型的。
（3）通过form表单或者url来向另一个页面或者servlet传递参数的时候需要用getParameter获取值；getAttribute只能获取setAttribute的值
（4）setAttribute是应用服务器把这个对象放到该页面所对应的一块内存当中，当你的页面服务器重定向到另一个页面的时候，应用服务器
会把这块内存拷贝到另一个页面对应的内存当中。通过getAttribute可以取得你存下的值，当然这种方法可以用来传对象。
用session也是一样的道理，这是说request和session的生命周期不一样而已。
6、JSP有哪些内置对象，作用是什么？
JSP内置对象

名称  作用
request 包含用户端请求的信息
response    包含服务器传回客户端的响应信息
session 与请求有关的会话期
pageContext 管理网页属性
application 服务器启动时创建，服务器关闭时停止，为多个应用程序保存信息
out 向客户端输出数据
config  servlet的架构部件
page    指网页本身
exception   针对错误页面才可使用
7、四种会话跟踪技术作用域
（1）page：一个页面
（2）request：：一次请求
（3）session：一次会话
（4）application：服务器从启动到停止。
7、JSP中动态INCLUDE和静态INCLUDE有什么区别
include指令用于把另一个页面包含到当前页面中，在什么时候包含的？再转换成servlet的时候包含进去的。
动态INCLUDE用jsp:include动作实现 <jsp:include page="included.jsp" flush="true" />它总是会检查所含文件中的变化,适合用于包含动态页面,
并且可以带参数.
静态INCLUDE用include伪码实现,定不会检查所含文件的变化,适用于包含静态页面<%@ include file="included.htm" %>
8、forward和redirect的区别
转发与重定向
（1）从地址栏显示来说 
forward是服务器请求资源,服务器直接访问目标地址的URL,把那个URL的响应内容读取过来,然后把这些内容再发给浏览器.浏览器根本不知道服务器发送
的内容从哪里来的,所以它的地址栏还是原来的地址.redirect是服务端根据逻辑,发送一个状态码,告诉浏览器重新去请求那个地址.所以地址栏显示的是
新的URL.
（2）从数据共享来说 
forward:转发页面和转发到的页面可以共享request里面的数据.
redirect:不能共享数据.
（3）从运用地方来说 
forward:一般用于用户登陆的时候,根据角色转发到相应的模块.
redirect:一般用于用户注销登陆时返回主页面和跳转到其它的网站等.
（4）从效率来说 
forward:高.
redirect:低.