---
layout: post
title: apache 和 tomcat区别
category: Web
date: 2015-07-23
---

经常在用apache和tomcat等这些服务器，可是总感觉还是不清楚他们之间有什么关系，在用tomcat的时候总出现apache，总感到迷惑，到底谁是主谁是次，因此特意在网上查询了一些这方面的资料，总结了一下： 

* 解析一 

apache支持静态页，tomcat支持动态的，比如servlet等， 
一般使用apache+tomcat的话，apache只是作为一个转发，对jsp的处理是由tomcat来处理的。 
apche可以支持phpcgiperl,但是要使用java的话，你需要tomcat在apache后台支撑，将java请求由apache转发给tomcat处理。 
apache是web服务器，tomcat是应用（java）服务器，它只是一个servlet(jsp也翻译成servlet)容器，可以认为是apache的扩展，但是可以独立于apache运行。 

这两个有以下几点可以比较的： 

1、两者都是apache组织开发的 

2、两者都有HTTP服务的功能 

3、两者都是免费的 

不同点： 

Apache是专门用了提供HTTP服务的，以及相关配置的（例如虚拟主机、URL转发等等） 
Tomcat是Apache组织在符合J2EE的JSP、Servlet标准下开发的一个JSP服务器

<!-- more -->


* 解析二： 

APACHE是一个web服务器环境程序 启用他可以作为web服务器使用 不过只支持静态网页 如(asp,php,cgi,jsp)等动态网页的就不行 
如果要在APACHE环境下运行jsp 的话就需要一个解释器来执行jsp网页 而这个jsp解释器就是TOMCAT, 为什么还要JDK呢？因为jsp需要连接数据库的话 就要jdk来提供连接数据库的驱程，所以要运行jsp的web服务器平台就需要APACHE+TOMCAT+JDK 
整合的好处是： 
如果客户端请求的是静态页面，则只需要Apache服务器响应请求 
如果客户端请求动态页面，则是Tomcat服务器响应请求 
因为jsp是服务器端解释代码的，这样整合就可以减少Tomcat的服务开销 
C是一个结构化语言，如谭老爷子所说：它的重点在于算法和数据结构。C程序的设计首要考虑的是如何通过一个过程，对输入（或环境条件）进行运 算处理得到输出（或实现过程（事务）控制），而对于C++，首要考虑的是如何构造一个对象模型，让这个模型能够契合与之对应的问题域，这样就可以通过获取 对象的状态信息得到输出或实现过程（事务）控制。 

* 解析三
： 
apache:侧重于http server 

tomcat:侧重于servlet引擎，如果以standalone方式运行，功能上与apache等效 ， 支持JSP，但对静态网页不太理想； 

apache是web服务器，tomcat是应用（java）服务器，它只是一个servlet(jsp也翻译成servlet)容器，可以认为是apache的扩展，但是可以独立于apache运行。 
换句话说，apache是一辆卡车，上面可以装一些东西如html等。但是不能装水，要装水必须要有容器（桶），而这个桶也可以不放在卡车上。

转自：http://www.cnblogs.com/warioland/archive/2011/12/05/2276729.html
