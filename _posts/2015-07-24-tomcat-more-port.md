---
layout: post
title: tomcat more port
category: 
date: 2015-07-24
---

### 一、Tomcat 安装后本身提供了一个server，端口配置默认是8080，对应目录为：..\Tomcat 6.0\webapps
### 二、Tomcat 6.0 配置多个端口，其实也就是给Tomcat增加几个server，并设置对应目录。下面以增加两个端口号为例
第一步、修改server.xml[..\Tomcat 6.0\conf]
*（1）Tomcat提供的如下：

{% highlight xml %}
<Service name="Catalina">  
     
    <Connector port="8090" maxHttpHeaderSize="8192"  
               maxThreads="150" minSpareThreads="25" maxSpareThreads="75"  
               enableLookups="false" redirectPort="8443" acceptCount="100"  
               connectionTimeout="20000" disableUploadTimeout="true" />  
      
    <Connector port="8009"   
               enableLookups="false" redirectPort="8443" protocol="AJP/1.3" />  
  
  
    <Engine name="Catalina" defaultHost="localhost">  
  
  
      <Realm className="org.apache.catalina.realm.UserDatabaseRealm"  
             resourceName="UserDatabase"/>  
        
      <Host name="localhost" appBase="webapps"  
       unpackWARs="true" autoDeploy="true"  
       xmlValidation="false" xmlNamespaceAware="false">                
      </Host>  
  
  
    </Engine>  
  
  
</Service>  
{% endhighlight %}

*（2）新增两个端口号，注意Service name、Engine name、appBase，
当然了，端口号别忘了修改，以免重复。

{% highlight xml %}
<Service name="Catalina1">  
     
    <Connector port="8091" maxHttpHeaderSize="8192"  
               maxThreads="150" minSpareThreads="25" maxSpareThreads="75"  
               enableLookups="false" redirectPort="8443" acceptCount="100"  
               connectionTimeout="20000" disableUploadTimeout="true" />  
      
    <Connector port="8009"   
               enableLookups="false" redirectPort="8443" protocol="AJP/1.3" />  
  
  
    <Engine name="Catalina1" defaultHost="localhost">  
  
  
      <Realm className="org.apache.catalina.realm.UserDatabaseRealm"  
             resourceName="UserDatabase"/>  
        
      <Host name="localhost" appBase="webapps1"  
       unpackWARs="true" autoDeploy="true"  
       xmlValidation="false" xmlNamespaceAware="false">                
      </Host>  
  
  
    </Engine>  
  
  
</Service>  


<Service name="Catalina2">  
     
    <Connector port="8092" maxHttpHeaderSize="8192"  
               maxThreads="150" minSpareThreads="25" maxSpareThreads="75"  
               enableLookups="false" redirectPort="8443" acceptCount="100"  
               connectionTimeout="20000" disableUploadTimeout="true" />  
      
    <Connector port="8009"   
               enableLookups="false" redirectPort="8443" protocol="AJP/1.3" />  
  
  
    <Engine name="Catalina2" defaultHost="localhost">  
  
  
      <Realm className="org.apache.catalina.realm.UserDatabaseRealm"  
             resourceName="UserDatabase"/>  
        
      <Host name="localhost" appBase="webapps2"<!-- 这个地方很重要，我今天没修改这个，导致webapps下的几个应用都可以用多个端口访问，结果只好将默认的webapps下的应用拷到其他地方，在${tomcat_home}/conf/下创建Catalina2/localhost/，并创建不同的xml文件将docBase指向应用所在的路径-->  
       unpackWARs="true" autoDeploy="true"  
       xmlValidation="false" xmlNamespaceAware="false">                
      </Host>  
  
  
    </Engine>  
  
  
</Service>  
{% endhighlight %}


第二步、创建相应的部署目录

..\Tomcat 6.0\webapps1

..\Tomcat 6.0\webapps2

同时可以将Tomcat 6.0\webapps下的有用的复制到新建的部署目录

第三步、创建配置文件目录和配置文件

..\Tomcat 6.0\conf\Catalina1\localhost

..\Tomcat 6.0\conf\Catalina2\localhost

host-manager.xml

manager.xml

配置文件的配置可参照..\Tomcat 6.0\conf\Catalina\localhost下的，只需要修改一下对应目录即可

大概的配置步骤就是这些，现在可以把不同的war包放在不同的位置来访问了
以上配置方法同样适合Tomcat 5.5


转自 http://blog.csdn.net/wanghuan203/article/details/45310799

