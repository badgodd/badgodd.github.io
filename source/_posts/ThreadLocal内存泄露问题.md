---
title: ThreadLocal内存泄露问题
date: 2023-05-13 14:42:52
tags:
- ThreadLocal
categories:
- JUC
---

ThreadLocal是java.lang下面的一个类，是用来解决java多线程程序中并发问题的一种途径；通过为每一个线程创建一份共享变量的副本来保证各个线程之间的变量的访问和修改互相不影响；

ThreadLocal存放的值是线程内共享的，线程间互斥的，主要用于线程内共享一些数据，避免通过参数来传递，这样处理后，能够优雅的解决一些实际问题。

比如一次用户的页面操作请求，我们可以在最开始的filter中，把用户的信息保存在ThreadLocal中，在同一次请求中，在使用到用户信息，就可以直接到ThreadLocal中获取就可以了。

ThreadLocal有四个方法，分别为：
●initialValue
返回此线程局部变量的初始值
●get
返回此线程局部变量的当前线程副本中的值。如果这是线程第一次调用该方法，则创建并初始化此副本。
●set
将此线程局部变量的当前线程副本中的值设置为指定值。许多应用程序不需要这项功能，它们只依赖于 initialValue() 方法来设置线程局部变量的值。
●remove
移除此线程局部变量的值。



## ThreadLocal的实现原理



ThreadLocal中用于保存线程的独有变量的数据结构是一个内部类：ThreadLocalMap，也是k-v结构。
key就是当前的ThreadLoacal对象，而v就是我们想要保存的值。

![image-20231013144555156](ThreadLocal%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E9%97%AE%E9%A2%98/image-20231013144555156.png)



上图中基本描述出了Thread、ThreadLocalMap以及ThreadLocal三者之间的包含关系。

Thread类对象中维护了ThreadLocalMap成员变量，而ThreadLocalMap维护了以ThreadLocal为key，需要存储的数据为value的Entry数组。



## ThreadLocal内存泄露问题



ThreadLocal对象，是有两个引用的，一个是栈上的ThreadLocal引用，一个是ThreadLocalMap中的Key对他的引用。那么栈上的ThreadLocal引用不在使用了，即方法结束后这个对象引用就不再用了，那么，ThreadLocal对象因为还有一条引用链在，所以就会导致他无法被回收，久而久之可能就会对导致OOM。

这就是我们所说的ThreadLocal的内存泄露问题，为了解决这个问题，ThreadLocalMap使用了弱引用。如果用了弱引用，那么ThreadLocal对象就可以在下次GC的时候被回收掉了。

![image-20231013144809878](ThreadLocal%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E9%97%AE%E9%A2%98/image-20231013144809878.png)



当一个线程调用ThreadLocal的set方法设置变量的时候，当前线程的ThreadLocalMap就会存放一个记录，这个记录的键为ThreadLocal的弱引用，value就是通过set设置的值，这个value值被强引用。

这样做可以很大程度上的避免因为ThreadLocal的使用而导致的OOM问题，但是这个问题却无法彻底避免。

因为我们可以看到，虽然key是弱引用，但是value的那条引用，还是个强引用呢！而且他的生命周期是和Thread一样的，也就是说，只要这个Thread还在， 这个对象就无法被回收。

那么，什么情况下，Thread会一直在呢？那就是线程池。

在线程池中，重复利用线程的时候，就会导致这个引用一直在，而value就一直无法被回收。

那么如何解决呢？

ThreadLocalMap底层使用数组来保存元素，使用“线性探测法”来解决hash冲突的，在每次调用ThreadLocal的get、set、remove等方法的时候，内部会实际调用ThreadLocalMap的get、set、remove等操作。

而ThreadLocalMap的每次get、set、remove，都会清理过期的Entry。

所以，当我们在一个ThreadLocal用完之后，手动调用一下remove，就可以在下一次GC的时候，把Entry清理掉。