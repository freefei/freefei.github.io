---
layout: post
title: Spring 的 init-method 和 destory-method 初始化bean
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


在xml中配置 init-method和 destory-method方法
只是定义spring 容器在初始化bean 和容器销毁之前的所做的操作
基于xml的配置只是一种方式：

直接上xml中配置文件:

 {% highlight xml %}

   <bean id="personService" class="com.myapp.core.beanscope.PersonService" scope="singleton"  init-method="init"  destroy-method="cleanUp">
   
   </bean>
 {% endhighlight %}
 定义PersonService类：
  {% highlight java %}
  package com.myapp.core.beanscope;


public class PersonService  {
   private String  message;

	public String getMessage() {
		return message;
	}
	
	public void setMessage(String message) {
		this.message = message;
	}
   

	
	public void init(){
		System.out.println("init");
	}
	//  how  validate the  destory method is  a question
	public void  cleanUp(){
		System.out.println("cleanUp");
	}
}

  {% endhighlight%}

  相应的测试类：
    {% highlight java %}
    package com.myapp.core.beanscope;

import org.springframework.context.support.AbstractApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainTest {
  public static void main(String[] args) {
	  
	  AbstractApplicationContext  context =new  ClassPathXmlApplicationContext("SpringBeans.xml");
	
	PersonService  person = (PersonService)context.getBean("personService");
	
	person.setMessage("hello  spring");
	
	PersonService  person_new = (PersonService)context.getBean("personService");
	
	System.out.println(person.getMessage());
	System.out.println(person_new.getMessage());
	context.registerShutdownHook();

	
}
} 

      {% endhighlight%}

测试结果：

init

hello  spring

hello  spring

cleanUp

可以看出 init 方法和 clean up方法都已经执行了。

context.registerShutdownHook(); 是一个钩子方法，当jvm关闭退出的时候会调用这个钩子方法，在设计模式之 模板模式中 通过在抽象类中定义这样的钩子方法由实现类进行实现，这里的实现类是AbstractApplicationContext，这是spring 容器优雅关闭的方法。

**[本文转自http://blog.csdn.net/topwqp/article/details/8681467](http://blog.csdn.net/topwqp/article/details/8681467)**