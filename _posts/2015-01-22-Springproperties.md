---
layout: post
title: Spring注入properties中的配置信息
category: Spring
duoshuo: true
date: 2015-01-22
tags : [spring自动注入]
---

项目中可以使用spring读取properties配置文件的方式方便的注入配置信息

<!-- more -->

**一个简单的例子**

1、spring配置文件 root-context.xml

{% highlight xml %}

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/util
       http://www.springframework.org/schema/util/spring-util.xsd">


    <util:properties id="app" location="classpath:app.properties"/>
</beans>

{% endhighlight %}

2、properties文件 app.properties

{% highlight text %}

searchHost=127.0.0.1
searchPort=9200

{% endhighlight %}

3、测试代码 TestProperties.java

{% highlight java %}

package com.my.profile;
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
/**
 * 测试spring读取properties配置信息
 * Created with IntelliJ IDEA
 * Author: songrenfei
 * Date: 15/1/22
 * Time: 下午9:46
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring/root-context.xml"})
public class TestProperties {

    @Value("#{app.searchHost}")
    private String searchHost;

    @Value("#{app.searchPort}")
    private String searchPort;

    @Test
    public void test(){

        Assert.assertNotNull(searchHost);
        Assert.assertNotNull(searchPort);
    }
}

{% endhighlight %}

4、xml中也可以直接使用properties中得值

{% highlight xml %}
 <util:properties id="app" location="classpath:groupon.properties"/>
    <bean id="setting" class="io.terminus.pampas.engine.Setting">
        <property name="mode" value="IMPLANT"/>
        <property name="implantApp">
            <bean class="io.terminus.pampas.engine.model.App">
                <property name="key" value="#{app.appName}"/>
                <property name="domain" value="wtf"/>
                <property name="assetsHome" value="#{app.assetsHome}"/>
                <property name="configPath" value="#{app.configPath}"/>
            </bean>
        </property>
        <property name="devMode" value="#{app.mode == 'dev'}"/>
        <property name="registryId" value="pampas"/>
    </bean>

{% endhighlight %}

**如果同时读取多份properties文件 方法如下**

方法一：

{% highlight xml %}
<util:properties/> 标签只能加载一个文件，当多个属性文件需要被加载的时候，可以使用多个该标签
{% endhighlight %}

方法二：

{% highlight xml %}
<!-- <util:properties/> 标签的实现类是PropertiesFactoryBean，  
 直接使用该类的bean配置，设置其locations属性可以达到一个和上面一样加载多个配置文件的目的 -->  
 <bean id="settings"   
   class="org.springframework.beans.factory.config.PropertiesFactoryBean">  
   <property name="locations">  
  <list>  
    <value>file:/opt/rms/config/rms-mq.properties</value>  
    <value>file:/opt/rms/config/rms-env.properties</value>  
  </list>  
   </property>  
 </bean>  
{% endhighlight %}

方法三：

{% highlight xml %}
    <!-- 将多个配置文件读取到容器中，交给Spring管理 -->  
    <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">  
        <property name="locations">  
           <list>  
              <!-- 这里支持多种寻址方式：classpath和file -->  
              <value>classpath:/opt/demo/config/demo-db.properties</value>  
              <!-- 推荐使用file的方式引入，这样可以将配置和代码分离 -->  
              <value>file:/opt/demo/config/demo-mq.properties</value>  
              <value>file:/opt/demo/config/demo-remote.properties</value>  
            </list>  
        </property>  
    </bean>  

 {% endhighlight %}

 方法四：

{% highlight xml %}
 <!-- 将多个配置文件位置放到列表中 -->  
    <bean id="propertyResources" class="java.util.ArrayList">  
        <constructor-arg>  
            <list>  
              <!-- 这里支持多种寻址方式：classpath和file -->  
              <value>classpath:/opt/demo/config/demo-db.properties</value>  
              <!-- 推荐使用file的方式引入，这样可以将配置和代码分离 -->  
              <value>file:/opt/demo/config/demo-mq.properties</value>  
              <value>file:/opt/demo/config/demo-remote.properties</value>  
            </list>  
        </constructor-arg>  
    </bean>  
      
    <!-- 将配置文件读取到容器中，交给Spring管理 -->  
    <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">  
        <property name="locations" ref="propertyResources" />  
    </bean> 

{% endhighlight %}

 