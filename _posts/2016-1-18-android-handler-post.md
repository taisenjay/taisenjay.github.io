---
layout: post
title: "Android消息机制"
excerpt: "Android基础要点（1）"
tags: [，面试要点]
comments: true
---

在日常开发中经常会使用Handler将更新UI的操作切换到主线程中执行，也就是经常提的Android中消息机制。从开发的角度来说，Handler是Android消息机制的上层接口，这使得在开发过程中只需要和Handler交互即可。通过Handler可以轻松地将一个任务切换到Handler所在的线程中去执行，所以Handler的主要所用是将一个任务切换到某个指定的线程中去执行。

Android的消息机制主要是指Handler的运行机制以及Handler所附带的MessageQueue和Looper的工作过程，这三者是一个整体，而我们在开发时较多的接触Handler。Handler的运行需要底层的MessageQueue和Looper的支撑。

MessageQueue，即消息队列，它的内部采用了单链表的数据结构存储了一组消息，以队列的形式对外提供插入和删除的操作。Looper，即消息循环，MessageQueue是消息的存储单元，而Looper则是处理消息的单元，Looper会以无限循环的形式去查找MessageQueue中是否有新消息，如果有的话就处理消息，否则就一直等待。

Handler创建的时候会采用当前线程的Looper来构造消息循环系统，而线程是默认没有Looper的，所以为了使用Handler我们必须为线程创建Looper。在主线程（UI线程），也就是ActivityThread，被创建时就会初始化Looper，所以主线程中默认可以使用Handler。

Handler创建完毕后，其内部的Looper以及MessageQueue就可以和Handler一起协同工作了，然后通过Handler的send方法发送一个消息。当Handler的send方法被调用时，它会调用MessageQueue的enqueueMessage方法将这个这个消息放入消息队列中，然后Looper发现有新消息到来时，就会处理这个消息，最终消息中的handleMessage方法会被调用。Looper是运行在创建Handler所在的线程中的，这样一来Handler中的业务逻辑就被切换到创建Handler所在的线程中去执行了。

<figure>
	<img src="/images/handler.png">
</figure>

MessageQueue主要包含两个操作：插入和读取。读取本身会包含着删除操作，插入和读取对应的方法分别为enqueueMessage和next，其中enqueueMessage的作用是往消息队列中插入一条信息，而next的作用是从消息队列中取出一条消息并将其从消息队列中移除。消息队列内部实现是一个单链表的数据结构来维护消息列表，因为单链表在插入和删除上比较有优势。enqueueMessage的主要操作就是单链表的插入操作。next方法是一个无限循环的方法，如果消息队列中没有消息，那么next方法会一直阻塞在这里，当有新消息来时，next方法会返回这条消息并将其从单链表中移除。

Looper在Android的消息机制中扮演消息循环的角色，其会不停地从MessageQueue中查看是否有新消息，如果有新消息就会立刻处理，否则就一直阻塞在那里。在Looper的构造方法中，它会创建一个MessageQueue，然后将当前线程的对象保存起来。我们知道创建Handler需要当前线程有Looper，通过Looper.prepare（）即可为当前线程创建一个Looper，接着通过Looper.loop（）来开启消息循环。示例如下：
{% highlight java %}

     new Thread ("Thread#2"){
          @Override
          public  void  run （）{
               Looper.prepare（）；
               Handler  handler = new  Handler（）；
               Looper.loop（）；
          }；
     }.start（）；
{% endhighlight %}

Looper提供的方法：

- prepare（）：为当前线程创建一个Looper
- prepareMainLooper（）：为主线程，也就是ActivityThread创建Looper
- getMainLooper（）：在任何地方获取主线程的Looper
- quit（）：直接退出Looper
- quitSafely（）：设定退出标记，在把消息队列中的已有消息处理完毕后安全退出（注：Looper退出后，通过Handler发生消息会失败，Handler的send方法会返回false。在子线程中，如果手动为期创建了Looper，那么在所有任务完成后应该退出Looper，否则子线程会一直处于等待状态，而退出Looper后，线程就会立即终止）
- loop（）：loop方法是一个死循环，唯一跳出循环的方式是MessageQueue的next方法返回null。当Looper的quit方法被调用时，Looper就会调用MessageQueue的quit或quitSafely方法来通知消息队列退出，否则loop方法就会无限循环下去。loop方法会调用MessageQueue的next方法来获取新消息，而next是一个阻塞操作，当没有消息时，next方法会一直阻塞在那里，这也导致了loop方法一直阻塞。如果消息队列的next方法返回了新消息，Looper就会处理这条消息：msg.target.dispatchMessage(msg)，这里的msg.target是发送这条消息的Handler对象，这Handler发送的消息最终又交给它的dispatchMessage方法来处理了。但是这里不同的是，Handler发送的消息最终又交给它的dispatchMessage方法来处理了。但是这里不同的是，Handler的dispatchMessage方法是在创建Handler时所使用的Looper中执行的，这样就成功地将代码逻辑切换到指定的线程中去执行了。
          
Handler的工作主要包含信息的发送和接收过程。消息的发送可以通过post的一系列或者send的一系列方法，post的方法最终是通过send来实现的。Handler发送消息的过程就是向消息队列中插入一条消息，MessageQueue的next方法就会返回这条消息给Looper，Looper收到消息后就开始处理了，最终消息由Looper交由Handler处理，即Handler的dispatchMessage方法会被调用，这时Handler就进入了处理消息的阶段。
     
