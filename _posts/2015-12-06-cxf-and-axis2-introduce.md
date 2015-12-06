---
layout: post
title: 主流Web Service框架介绍：CXF和Axis2
category: WebService
date: 2015-12-06
---

　CXF和Axis2是目前java平台上最主流的两个框架，虽然两个项目都隶属ASF，但却是基于不同思想和风格实现的，因此也各有所长。
<!-- more -->

**CXF：**[http://cxf.apache.org/](http://cxf.apache.org/)

是由过去的Celtix和XFire两个框架合并而来，CXF在java社区有广泛的接受度是得益于它能很好的集成Spring。我认为CXF最突出的两个优势是：

1.对JAX-WS规范的完整实现。 作为java平台上的WebService标准，过去既有的WebService产品必然会向这一标准靠拢，而JAX-WS标准本身大大地降低了开发WebService的工作量，对于开发人员来说，是非常受欢迎的。

2.对Spring的友好支持。 CXF从Xfire继承而来，对Spring有着非常友好的支持。鉴于Spring的广泛应用，对很多团队来说这是非常有吸引力

**Axis2:** [http://axis.apache.org/axis2/java/core/](http://axis.apache.org/axis2/java/core/)

与CXF这类嵌入式的框架相比，Axis2更像是一种是WS容器，它要求应用程序以aar包的形式部署到自己里面，这对于既有系统，特别是那些基于servlet容器的web应用来说，改造的代价可能会很大。Axis2的优势在于一方面它对WS-*协议族的支持比较全面，另一方面是它还支持C平台，这是一个值得我们关注的优势

**CXF VS AXIS2**


1.CXF支持 WS-Addressing，WS-Policy， WS-RM， WS-Security和WS-I Basic Profile。Axis2不支持WS-Policy，但是承诺在下面的版本支持。 

2. CXF可以很好支持Spring。Axis2不能 

3. AXIS2支持更广泛的数据并对，如XMLBeans，JiBX，JaxMe和JaxBRI和它自定义的数据绑定ADB。注意JaxME和JaxBRI都还是试验性的。CXF只支持JAXB和Aegis。在CXF2.1 

4. Axis2支持多语言-除了Java,他还支持C/C++版本。 

比较这两个框架的Web Service开发方法与比较它们的特性同样重要。 从开发者的角度，两个框架的特性相当的不同。 Axis2的开发方式类似一个小型的应用服务器，Axis2的开发包要以WAR的形式部署到Servlet容器中，比如Tomcat，通过这些容器可以对工作中的Web Service进行很好的监控和管理。Axis2 的Web administrion模块可以让我们动态的配置Axis2.一个新的服务可以上载，激活，使之失效，修改web服务的参数。管理UI也可以管理一个或者多个处于运行状态的服务。这种界面化管理方式的一个弊端是所有在运行时修改的参数没有办法保存，因为在重启动之后，你所做的修改就会全部失效。 

Axis2允许自己作为独立的应用来发布Web Service，并提供了大量的功能和一个很好的模型，这个模型可以通过它本身的架构（modular architecture）不断添加新的功能。有些开发人员认为这种方式对于他们的需求太过于繁琐。这些开发人员会更喜欢CXF。 

CXF更注重开发人员的工效（ergonomics）和嵌入能力（embeddability）。大多数配置都可以API来完成，替代了比较繁琐的XML配置文件， Spring的集成性经常的被提及，CXF支持Spring2.0和CXF's API和Spring的配置文件可以非常好的对应。CXF强调代码优先的设计方式（code-first design)，使用了简单的API使得从现有的应用开发服务变得方便。 
