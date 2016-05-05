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







