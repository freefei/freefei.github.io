---
layout: post
title: 取消代码警告SuppresWarnings
category: JAVA
duoshuo: true
date: 2015-01-20
tags : [java注解]
---
##@SuppressWarnings(unchecked)作用解释
屏蔽某些编译时的警告信息 
在强制类型转换的时候编译器会给出警告 
@SuppressWarnings("unchecked")

关键字
* deprecation
使用了不赞成使用的类或方法时的警告
* unchecked
执行了未检查的转换时的警告，例如当使用集合时没有用泛型 (Generics) 来指定集合保存的类型。
* fallthrough
当 Switch 程序块直接通往下一种情况而没有 Break 时的警告。
* path
在类路径、源文件路径等中有不存在的路径时的警告。
* serial
当在可序列化的类上缺少 serialVersionUID 定义时的警告。
* finally
任何 finally 子句不能正常完成时的警告。
* all
关于以上所有情况的警告。

示例：
*   @SuppressWarnings("unchecked")
告诉编译器忽略 unchecked 警告信息，如使用List，ArrayList等未进行参数化产生的警告信息。
*  @SuppressWarnings("serial")
如果编译器出现这样的警告信息：The serializable class WmailCalendar does not declare a static final serialVersionUID field of type long
       使用这个注释将警告信息去掉。
*  @SuppressWarnings("deprecation")
如果使用了使用@Deprecated注释的方法，编译器将出现警告信息。
       使用这个注释将警告信息去掉。
*   @SuppressWarnings("unchecked", "deprecation")
告诉编译器同时忽略unchecked和deprecation的警告信息。
*   @SuppressWarnings(value={"unchecked", "deprecation"})
等同于@SuppressWarnings("unchecked", "deprecation")