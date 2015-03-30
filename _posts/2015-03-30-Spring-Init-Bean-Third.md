---
layout: post
title: Spring bean 通过实现 InitializingBean ,DisposableBean 初始化
category: Spring
date: 2015-03-30
---


**一、简介**

关于在spring  容器初始化 bean 和销毁前所做的操作定义方式有三种：

* 第一种：通过@PostConstruct 和 @PreDestroy 方法 实现初始化和销毁bean之前进行的操作
* 第二种是：通过 在xml中定义init-method 和  destory-method方法
* 第三种是： 通过bean实现InitializingBean和 DisposableBean接口

下面演示通过  init-method 和 destory-method

<!-- more --> 

**二、举例**

* 1：定义相应类实现InitializingBean ,DisposableBean 接口

{% highlight java %}
package com.myapp.core.annotation.init;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;

public class PersonService  implements InitializingBean,DisposableBean{
  
	private String  message;

	public String getMessage() {
		return message;
	}

	public void setMessage(String message) {
		this.message = message;
	}

	@Override
	public void destroy() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("I'm  init  method  using implements InitializingBean interface...."+message);
		
	}

	@Override
	public void afterPropertiesSet() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("I'm  init  method  using implements DisposableBean interface...."+message);
		
	}
	

}

{% endhighlight%}

* 定义相应的配置文件:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:context="http://www.springframework.org/schema/context"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context-3.1.xsd">

<!-- <context:component-scan  base-package="com.myapp.core.jsr330"/> -->

<!-- <context:annotation-config /> -->


<!-- <bean class="org.springframework.context.annotation.CommonAnnotationBeanPostProcessor" />
<bean id="personService" class="com.myapp.core.annotation.init.PersonService">
		  <property name="message" value="123"></property>
</bean>
 -->

<bean id="personService" class="com.myapp.core.annotation.init.PersonService">
		  <property name="message" value="123"></property>
</bean>

</beans>
{% endhighlight%}

* 3：测试类：
{% highlight java %}
package com.myapp.core.annotation.init;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.AbstractApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainTest {
	
	public static void main(String[] args) {
		
		AbstractApplicationContext  context = new ClassPathXmlApplicationContext("resource/annotation.xml");
		
		PersonService   personService  =  (PersonService)context.getBean("personService");
		
	         context.registerShutdownHook();
	}

}

{% endhighlight%}


**[本文转自http://blog.csdn.net/topwqp/article/details/8681573](http://blog.csdn.net/topwqp/article/details/8681573)**