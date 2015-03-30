---
layout: post
title: 通过Spring @PostConstruct 和 @PreDestroy 方法初始化bean
category: Spring
date: 2015-03-30
---

**一、简介**

关于在spring  容器初始化 bean 和销毁前所做的操作定义方式有三种：

* 第一种：通过@PostConstruct 和 @PreDestroy 方法 实现初始化和销毁bean之前进行的操作
* 第二种是：通过 在xml中定义init-method 和  destory-method方法
* 第三种是： 通过bean实现InitializingBean和 DisposableBean接口

下面演示通过  @PostConstruct 和 @PreDestory

<!-- more --> 


**二、举例**

* 1:定义相关的实现类：
{% highlight java %}
package com.myapp.core.annotation.init;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

public class PersonService {
  
	private String  message;

	public String getMessage() {
		return message;
	}

	public void setMessage(String message) {
		this.message = message;
	}
	
	@PostConstruct
	public void  init(){
		System.out.println("I'm  init  method  using  @PostConstrut...."+message);
	}
	
	@PreDestroy
	public void  dostory(){
		System.out.println("I'm  destory method  using  @PreDestroy....."+message);
	}
	
}

 {% endhighlight %}

 * 2：定义相关的配置文件：

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

	<context:annotation-config />

	<bean id="personService" class="com.myapp.core.annotation.init.PersonService">
	  <property name="message" value="123"></property>
	</bean>

	</beans>
  {% endhighlight %}

  测试类:

{% highlight java %}
  package com.myapp.core.annotation.init;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainTest {
	
	public static void main(String[] args) {
		
		ApplicationContext  context = new ClassPathXmlApplicationContext("resource/annotation.xml");
		
		PersonService   personService  =  (PersonService)context.getBean("personService");
		
		personService.dostory();
	}

}

{% endhighlight %}

测试结果：

I'm  init  method  using  @PostConstrut....123

I'm  destory method  using  @PreDestroy.....123

**[转载自http://blog.csdn.net/topwqp/article/details/8681497](http://blog.csdn.net/topwqp/article/details/8681497)**

