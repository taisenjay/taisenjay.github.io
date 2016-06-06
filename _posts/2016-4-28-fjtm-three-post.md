---
layout: post
title: "保证App流畅的关键因素——多线程"
excerpt: "《Android开发进阶：从小工到专家》第三章笔记"
tags: [读书笔记，从小工到专家]
comments: true
---

Android应用在启动时会创建一个线程，即主线程或UI线程，Android应用的所有默认操作都运行在主线程中。为了保证UI的流畅性，必须要将耗时操作（IO操作，网络请求等）放到子线程中。

# 1、Android中的消息机制 #

需要理解为什么使用多线程以及为什么多线程能够解决UI线程阻塞的问题

## 1.1、处理消息的手段——Handler、Looper与MessageQueue ##

Android中的主线程会关联一个消息队列，所有的操作都会被封装成消息然后交给Handler处理。为了保证主线程不会主动退出，会将获得消息的操作放在一个死循环中，这样程序就相当于一直在执行死循环，因此不会退出。

<figure>
	<img src="/images/handler.png">
</figure>

UI线程的消息循环是在ActivityThread的main方法中创建的，main方法就是Android应用程序的入口.
main中调用Looper.prepareMainLooper()创建消息循环Looper，然后调用Looper.loop()执行消息循环。

main执行后，应用程序开始启动，并且一直从消息队列中取消息，然后处理信息，使得系统运转起来。系统通过Handler来将消息投递到消息队列以及从消息队列中获取消息并且处理消息。

Handler在Android中最常用的功能之一是将一个消息（比如在子线程中执行完耗时操作，然后需要更新UI）post到UI线程中，然后在Handler的handleMessage方法中进行处理。但是，该Handler必须在主线程中创建。这是因为每个Handler都会关联一个消息队列（这一点很好理解，Handler就是一个消息队列处理器，专门处理与它关联的消息队列中消息），消息队列被封装在Looper中，而每个looper又会关联一个线程，一句话总结就是每个消息队列关联一个线程。

默认情况下，消息队列只有一个，即主线程的消息队列，这个消息队列就是在ActivityThread的main方法中调用Looper.prepareMainLooper()创建的。消息队列通过Looper与线程关联上，而Handler又与Looper关联，因此Handler最终就和线程、线程的消息队列关联上了。因此，更新UI的Handler必须要在主线程中创建，Handler要与主线程的消息队列关联上，这样handleMessage才会执行在UI线程，此时更新UI才是线程安全的。

通过Handler向消息队列发送消息的两种方式：1、post（Runnable runnable）2、sendMessage（）

## 1.2、在子线程中创建Handler为何会抛出异常 ##

Looper对象是ThreadLocal的，即每个线程都有自己的Looper，除了主线程默认创建了Looper外，其他子线程默认不会创建Looper，所以在子线程中创建Handler会抛出异常，所以在子线程中使用Handler需要通过Looper.prepare（）来创建Looper，并通过Looper.loop()来启动循环。这样该线程就有了自己的Looper也就有了自己的消息队列。

# 2、Android中的多线程 #

## 2.1、Thread和Runnable ##

* new一个Thread,复写其run()方法，在run方法中进行耗时操作
* new一个Thread，在其构造函数中new一个Runnable参数，在Runnable的run方法中进行耗时操作

两者差别不大，Thread本身就是一个Runnable（其实现了Runnable接口）。Thread只是对Runnable的包装。当启动一个线程时，如果Thread的target不为空（即Thread构造函数中传的Runnable）则执行这个target的run方法，否则执行自身的run方法。

## 2.2、线程的wait、sleep、join、yield

* wait()，当线程执行到wait()方法时，它就进入到一个和该对象相关的等待池中，同时失去（释放）了对象的机锁，使得其他线程可以访问。用户可以使用notify(),notifyAll()或者指定睡眠时间来唤醒当前等待池中的线程（注：wait()、notifyAll()、notify()必须放在同步代码块中）
* sleep(),Thread类的静态函数，使调用线程进入睡眠状态，不能改变对象的机锁
* join(),等待目标线程执行完成之后再继续执行
* yield(),目标线程让出执行权限

## 2.3、与多线程相关的方法——Callable、Future和FetureTask ##

与Runnable不同，这三个类只能运用到线程池中。Runnable既能运用在Thread中，又能运用在线程池中。

* Callable和Runnable功能相似，但Callable支持泛型(call方法返回泛型参数)
* Future支持泛型，提供了对Runnable或Callable任务的执行结果进行取消、查询是否完成、获取结果、设置结果操作，分别对应cancel、isDone、get、set方法
* FutureTask是Future的实现类（Future只是定义了一些规范的接口）

## 2.4、构建服务器应用程序的有效方法——线程池 ##

线程池会创建多个线程并且进行管理，提交给线程池的任务会被线程池指派给其中的线程进行执行线程。

* ThreadPoolExecutor,指定数量的线程池
* ScheduledThreadPool,定时执行任务的线程池

## 2.5、同步集合 ##

* CopyOnWriteArrayList,其基本思路是：多个线程共享同一个列表，当某个线程需要修改列表时，会把列表中的元素copy一份然后进行修改，修改完成之后再将新的元素设置给这个列表，这是一种延时懒惰策略。
* ConcurentHashMap,HashTable是HasMap的实现，但HashTable效率低下（因为所有访问HashTable的线程都必须竞争同一把锁），而在ConcurrentHashMap中有多把锁，每一个锁用于容器其中一部分数据，所谓锁分段技术
* BlockingQueue,阻塞队列，生产者消费者实现，其有多个子类（如ArrayBlockingQueue、LinkedBlockingQueue）

## 2.6、 同步锁 ##

* 同步机制关键字——synchronized,能够作用于对象、方法、类。每一个对象只有一个锁，锁方法时其实也是锁的对象，作用于class时就是锁的Class类而并非某个具体对象。
* 显示锁——ReentrantLock与Condition,显示锁和内置锁（synchronized）的区别：更灵活，获取和释放的灵活、轮训锁和定时锁、公平性
* 信号量Semahore,计数信号量，本质是共享锁
* 循环栅栏CyclicBarrier,同步辅助类，允许一组线程互相等待，直到到达某个公共屏障点
* 闭锁CountDownLatch,同步辅助类，在完成一组正在其他线程中执行的操作之前，它允许一个或多个线程一直等待，直到条件满足。

## 2.7、创建异步任务更简单——AsyncTask的原理 ##











