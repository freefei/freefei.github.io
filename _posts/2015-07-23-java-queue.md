---
layout: post
title: Java多线程总结之线程安全队列Queue
category: JAVA
date: 2015-07-23
---

在Java多线程应用中，队列的使用率很高，多数生产消费模型的首选数据结构就是队列。Java提供的线程安全的Queue可以分为阻塞队列和非阻塞队列，其中阻塞队列的典型例子是BlockingQueue，非阻塞队列的典型例子是ConcurrentLinkedQueue，在实际应用中要根据实际需要选用阻塞队列或者非阻塞队列。
注：什么叫线程安全？这个首先要明确。线程安全的类 ，指的是类内共享的全局变量的访问必须保证是不受多线程形式影响的。如果由于多线程的访问（比如修改、遍历、查看）而使这些变量结构被破坏或者针对这些变量操作的原子性被破坏，则这个类就不是线程安全的。
今天就聊聊这两种Queue，本文分为以下两个部分，用分割线分开： 
BlockingQueue  阻塞算法
ConcurrentLinkedQueue，非阻塞算法

Queue是什么?一句话：队列是先进先出。相对的，栈是后进先出。

<!-- more -->

[多线程基础总结一--synchronized(1)](http://yanxuxin.iteye.com/blog/547261)

[多线程基础总结二--Thread](http://yanxuxin.iteye.com/blog/547266)

[多线程基础总结三--volatile](http://yanxuxin.iteye.com/blog/549211)

[多线程基础总结四--ThreadLocal](http://yanxuxin.iteye.com/blog/550020)

[多线程基础总结五--atomic](http://yanxuxin.iteye.com/blog/550824)

[多线程基础总结六--synchronized(2)](http://yanxuxin.iteye.com/blog/551734)

[多线程基础总结七--ReentrantLock](http://yanxuxin.iteye.com/blog/566713)

[多线程基础总结八--ReentrantReadWriteLock](http://yanxuxin.iteye.com/blog/571480)

[多线程基础总结九--Mina窥探(1)](http://yanxuxin.iteye.com/blog/577012)

[多线程基础总结十--LinkedBlockingQueue](http://yanxuxin.iteye.com/blog/582162)

[多线程基础总结十一--ConcurrentLinkedQueue](http://yanxuxin.iteye.com/blog/586943)

[Mina的线程池实现分析(1)](http://yanxuxin.iteye.com/category/86880)

[Mina的线程池实现分析(2)](http://yanxuxin.iteye.com/blog/592908)

[LinkedBlockingQueue应用--生产消费模型简单实现](http://yanxuxin.iteye.com/blog/583645)