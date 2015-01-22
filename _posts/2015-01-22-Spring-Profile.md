---
layout: post
title: Spring Profile
category: Spring
date: 2015-01-22
tags : [项目架构]
---
###需求描述：

在实际开发中，我们常常需要应对多类环境，针对不同的环境来更改相应的配置，比如常见开发环境、测试环境以及客户的实际部署环境。
<!-- more -->

###设置如下:

1、假设项目中有两种环境，开发和测试，定义两个beans，分别对应两个环境下的数据源配置：在Spring的配置文件applicationContext.xml中定义两个profile的beans。


	<beans profile="dev">
	</beans>
		
	<beans profile="test">
	</beans>
		
2、更改web.xml

	<context-param>
    <param-name>spring.profiles.active</param-name>
   	<param-value>dev</param-value>
	</context-param>
	或
	<context-param>
    <param-name>spring.profiles.active</param-name>
    <param-value>test</param-value>
	</context-param>
	
3、简单测试例子（不基于web）

* XML配置 spring-context.xml
{% highlight xml%}
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-		instance"
    	  xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/	spring-beans.xsd">			
			
    	<beans profile="dev">
			<bean id="user" class="com.my.user.model.User">
        		<property name="id" value="1"/>
        		<property name="name" value="songrenfei"/>
        		<property name="age" value="12"/>
    		</bean>
    	</beans>

		<beans profile="test">
          <bean id="user" class="com.my.user.model.User">
            <property name="id" value="1"/>
            <property name="name" value="songrenfei-test"/>
            <property name="age" value="34"/>
          </bean>
         </beans>
	 </beans>
	    
{% endhighlight %}

* java代码 User.java
{% highlight java linenos %}
package com.my.user.model;

import lombok.Data;

import java.io.Serializable;

/**
 * Created by songrenfei on 14-11-5.
 */
@Data
public class User implements Serializable{


    private static final long serialVersionUID = 2113807297177018033L;

    private Long id;

    private String name;

    private Integer age;
}

{% endhighlight%}

* java代码 TestSpringProfile.java

 {% highlight java linenos %}
 package com.my.profile;
import com.my.user.model.User;
import org.hamcrest.core.Is;
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
/**
 * Created with IntelliJ IDEA
 * Author: songrenfei
 * Date: 15/1/22
 * Time: 下午4:34
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring/spring-context.xml"})
@ActiveProfiles("test") //指定环境
public class TestSpringProfile {
    @Autowired
    private User user;

    @Test
    public void test(){

        Assert.assertThat(user.getName(), Is.is("songrenfei"));

    }
}

 
 {% endhighlight %}



