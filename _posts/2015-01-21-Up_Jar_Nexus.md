---
layout: post
title: 上传jar包到私服
category: MAVEN
duoshuo: true
date: 2015-01-21
tags : [maven-nexus]
---

把本地jar包或第三方jar包上传到nexus私服方法：

<!-- more -->

1、 命令如下：

 {% highlight text %}
mvn deploy:deploy-file -Durl=file://C:\m2-repo \
                       -DrepositoryId=some.id \
                       -Dfile=your-artifact-1.0.jar \
                       [-DpomFile=your-pom.xml] \
                       [-DgroupId=org.some.group] \
                       [-DartifactId=your-artifact] \
                       [-Dversion=1.0] \
                       [-Dpackaging=jar] \
                       [-Dclassifier=test] \
                       [-DgeneratePom=true] \
                       [-DgeneratePom.description="My Project Description"] \
                       [-DrepositoryLayout=legacy] \
                       [-DuniqueVersion=false]
{% endhighlight %}

2、 示例：


mvn deploy:deploy-file -DgroupId=org.sphx -DartifactId=sphinxapi -Dversion=1.0 -Dpackaging=jar 
-Dfile=D:\sphinxapi.jar -Durl=http://10.10.10.10:8800/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty



3、 命令参数解释：

* DgroupId和DartifactId构成了该jar包在pom.xml的坐标，项目就是依靠这两个属性定位。自己起名字也行。

* Dfile表示需要上传的jar包的绝对路径。

* Durl私服上仓库的位置，打开nexus——>repositories菜单，可以看到该路径。

* DrepositoryId服务器的表示id，在nexus的configuration可以看到。

* 上传成功后，在nexus界面点击3rd party仓库可以看到这包。

4、 配置权限

 ~/m2/settles.xml文件

 {% highlight xml %}
  <servers>
    <server>
      <id>ID</id>
      <username>name</username>
      <password>password</password>
    </server>
  </servers>
 {%  endhighlight %}

id：这是Server的ID(不是登录进来的user)，与Maven想要连接上的repository/mirror中的id元素相匹配。

username，password：这两个元素成对出现，表示连接这个server需要验证username和password。

