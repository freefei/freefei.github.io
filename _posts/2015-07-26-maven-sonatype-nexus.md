---
layout: post
title: maven sonatype nexus
category: Maven
date: 2015-07-26
---

1、 为什么使用Nexus
如果没有私服，我们所需的所有构件都需要通过maven的中央仓库和第三方的Maven仓库下载到本地，而一个团队中的所有人都重复的从maven仓库下载构件无疑加大了仓库的负载和浪费了外网带宽，如果网速慢的话，还会影响项目的进程。很多情况下项目的开发都是在内网进行的，连接不到maven仓库怎么办呢？开发的公共构件怎么让其它项目使用？这个时候我们不得不为自己的团队搭建属于自己的maven私服，这样既节省了网络带宽也会加速项目搭建的进程，当然前提条件就是你的私服中拥有项目所需的所有构件。

<!-- more -->

2.这些都准备好之后，去下载最新版本的nexus 

下载地址：http://www.sonatype.org/nexus/go 
我本地安装的是 nexus-2.2-01-bundle，最新的版本是nexus-2.4.0-09-bundle

3.打开目录nexus-2.4.0-09-bundle\nexus-2.4.0-09\bin\jsw 这个目录下面你会发现有很多系统版本的nexus环境
我的电脑是win7 (64)为的系统，所以我选择的是windows-x86-64这个版本，当然可以根据个人的电脑系统选择对应的版本
![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven1.png)

打开一个版本你会看到如下:
![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven2.png)

我一般都是将nexus安装成windows服务，所以点击install-nexus.bat这个，访问http://localhost:8081/nexus/ 启动后如下页面，在右上角有个Log in 的超链接，点击登录
默认的用户名是 admin 密码是 admin123
![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven3.png)

4.接下来，我们配置一下maven的代理服务器(前提是你的电脑不能连接外网，如果可以上外网，这里也没有意思，只是介绍一下)
   在左侧菜单找到如图
![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven4.png)

点击查看右边有很多选项，找到这里
![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven5.png)

添加你的代理服务器就可以了。
 5：接下来，好像这里都不需要怎么配置的，反正我没有用到很多的功能，可能是技术学得不好，不会用，呵呵....
     对了，这里还有一个可能需要注意一下的，就是3rd party、Snapshots、Releases这三个，分别用来保存第三方jar（典型的oracle数据库的j驱动包），项目组内部的快照、项目组内部的发布版.
     我目前只是用3rd party这个第三方的功能，将maven仓库中没有构件的jar包上传到服务器。如何将第三方的jar上传到nexus上面呢?如下：举例上传oracle的驱动包
![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven6.png)

![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven7.png)

![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven8.png)

![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven9.png)

![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven10.png)

那么现在nexus已经安装好，怎么使用上传的jar包吧，很简单的，前提是你已经建立了一个maven的项目。含有pom.xml这个文件,在这个文件中添加如下：
先去服务器看看jar的maven构件
![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven11.png)

然后在pom.xml中添加如下的本地仓库地址：
{% highlight xml %}
<repositories>
 2         <repository>
 3             <id>nexus</id>
 4             <name>Team Nexus Repository</name>
 5             <url>http://localhost:8081/nexus/content/groups/public</url>
 6         </repository>
 7     </repositories>
 8     <pluginRepositories>
 9         <pluginRepository>
10             <id>nexus</id>
11             <name>Team Nexus Repository</name>
12             <url>http://localhost:8081/nexus/content/groups/public</url>
13         </pluginRepository>
14     </pluginRepositories>
15     <dependencies>
16         <dependency>
17             <groupId>com.oracle</groupId>
18             <artifactId>ojdbc</artifactId>
19             <version>10.1.0.2.0</version>
20         </dependency>
21     </dependencies>

{% endhighlight %}

![描述](http://7u2myi.com1.z0.glb.clouddn.com/maven12.png)

到此，完成了nexus私服的搭建，项目组开发人员开发时，只要在项目的pom.xml文件中，添加如下pom.xml信息即可获取私服的jar.
如果添加其他的构件时，会先在nexus私服下载好，以后才会下载到本地。以后，如果发现私服已经存在某一jar包，则会直接从私服下载，如果没有再去网络上下载。这就是搭建私服的好处。哈哈........
所以很有必要搭建maven私服。





