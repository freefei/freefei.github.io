---
layout: post
title: spring @Qualifier
category: Spring
date: 2015-08-05
---

@Autowired是根据类型进行自动装配的。如果当Spring上下文中存在不止一个UserDao类型的bean时，就会抛出BeanCreationException异常;如果Spring上下文中不存在UserDao类型的bean，也会抛出BeanCreationException异常。我们可以使用@Qualifier配合@Autowired来解决这些问题。如下：

①可能存在多个UserDao实例
{%highlight java%}
@Autowired 
@Qualifier("userServiceImpl") 
public IUserService userService; 

或

@Autowired   
public void setUserDao(@Qualifier("userDao") UserDao userDao) {   
    this.userDao = userDao;   
} 
{% endhighlight%}

这样Spring会找到id为userServiceImpl和userDao的bean进行装配。

②可能不存在UserDao实例

{% highlight java%}
@Autowired(required = false)   
public IUserService userService
{% endhighlight%}