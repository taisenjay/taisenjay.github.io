---
layout: post
title: "Android中的线程形态"
excerpt: "Android基础要点（2）"
tags: [异步任务，AsyncTask,面试要点]
comments: true
---

在Android中，除了Thread外，可以扮演线程角色的还有其他的一些类，比如AsyncTask，IntentService和HandlerThread。AsyncTask，IntentService和HandlerThread看起来和传统的线程没关系，其实它们的本质就是线程。AsyncTask的底层用到了线程池，IntentService和HandlerThread的底层直接使用了线程。

##AsyncTask

AsyncTask是一种轻量级的异步任务类，其封装了线程池和Handler，主要作用是方便开发者在子线程中更新UI，通过AsyncTask可以更方便地执行后台任务以及在主线程中访问UI。

AsyncTask是一个抽象泛型类，提供了Params、Progress和Result这三个泛型参数。在使用AsyncTask时，必须创建一个子类去继承它，在继承时可以为这三个参数指定类型。

- Params：在执行AsyncTask时需要传入的参数，可用于在后台任务中使用；
- Progress：后台任务执行时，如果需要在界面显示进度，则使用这里指定的泛型作为进度但我；
- Result：当任务执行完毕后，如果需要对结果进行返回，则使用这里指定的泛型作为返回值类型。

如果AsyncTask确实不需要传递具体的参数，那么这三个泛型参数可以用Void来代替。在具体地使用AsyncTask时，需要重写其一些核心方法：



- onPreExecute（），在主线程中执行，在异步任务执行之前，此方法会被调用，一般可以用于做一些准备工作。
- doInBackground（Params……params），在线程池中执行，此方法用于执行异步任务，params参数表示异步任务的输入参数。在此方法中可以通过publishProgress方法来更新任务的进度，publishProgress方法会调用onProgressUpdate方法。另外此方法需要返回计算结果给onPostExecute方法。
- onProgressUpdate（Progress……values），在主线程中执行，当后台任务的执行进度发生改变时此方法会被调用。
- onPostExecute（Result result），在主线程中执行，在异步任务执行之后，此方法会被调用，其中result参数是后台任务的返回值，即doInBackground的返回值。

在这几个方法中，onPreExecute会先执行，接着是doInBackground，最后才是onPostExecute（）。除了上述四个方法外，AsyncTask还提供了onCancelled（）方法，它同样在主线程中执行，当异步任务呗取消时，onCancelled（）会被调用，这个时候OnPostExecute则不会被调用。

 AsyncTask在具体使用时，有一些限制条件：

- AsyncTask的类必须在主线程中加载，这就意味着第一次访问AsyncTask必须发生在主线程，当然这个过程在Android4.1及以上版本中已经被系统自动完成。查看ActivityThread源码会发现在main（）方法中，会调用AsyncTask的init方法。
- AsyncTask的对象必须在主线程中创建。
- execute方法必须在主线程调用
- 不要再程序中直接调用onPreExecute（），onPostExecute(),doInBackground()和onProgressUpdate（）方法
- 一个AsyncTask对象只能执行一次，即只能调用一次execute方法，否则会报运行时异常。
- 在Android1.6之前，AsyncTask是串行执行任务的，Android1.6的时候AsyncTask开始采用线程池来处理并行任务，但是从Android3.0开始，为了避免AsyncTask所带来的并发错误，AsyncTask又采用一个线程来串行执行任务。但是，在Android3.0以及后续版本中，任然可以通过AsyncTask的executeOnExecutor方法来并行地执行任务。

[Android异步处理四：AsyncTask的实现原理 - lzc的专栏 - 博客频道 - CSDN.NET](http://blog.csdn.net/mylzc/article/details/6774131)

##HandlerThread

HandlerThread继承了Thread，它是一种可以使用Handler的Thread，它的实现就是在其run方法中通过Looper.prepare（）来创建消息队列，并通过Looper.lool（）来开启消息循环，这样在实际的使用中就允许在HandlerThread中创建Handler了。HandlerThread的run（）方法如下：

{% highlight java %}

     public  void  run (){
          mTid = Process.myTid（）；
          Looper.prepare （）；
          synchronized（this）{
               mLooper = Looper.myLooper（）；
               notifyAll（）；
          }
          Process.setThreadPriority(mPriority)；
          onLooperPrepared（）；
          Looper.loop（）；
          mTid = -1；
     }
{% endhighlight %}

从HandlerThread的实现来看，它和普通的Thread是不同的，普通的Thread主要用于在run方法中执行一个耗时任务，而HandlerThread在内部创建了消息队列，外界需要通过Handler的消息方式来通知HandlerThread执行一个具体的任务。HandlerThread是一个很有用的类，它在Android中的一个具体的使用场景时IntentService。由于HandlerThread的run方法是一个无限循环，因此当明确不需要使用HandlerThread时，可以通过它的quit（）或者quitSafely（）方法来终止线程的执行，这是个良好的编程习惯。

##IntentService

IntentService是一个服务，系统对其进行了封装使其可以更方便地执行后台任务，IntentService内部采用HandlerThread来执行任务，当任务执行完毕后IntentService会自动退出。从任务的执行角度来看，IntentService的作用很像一个后台线程，但是IntentService是一种服务，它不容易被系统杀死从而可以尽量保证任务的执行，而如果是一个后台线程，由于这个时候进程中还没有活动的四大组件，那么这个进程的优先级就会非常低，会很容易被系统杀死，这就是IntentService的优点。

[Android基本功：IntentService的使用 - 小彭 的技术专栏 - 博客频道 - CSDN.NET](http://blog.csdn.net/p106786860/article/details/17885115)

[HandlerThread与IntentService原理解析 - Lai18.com IT技术文章收藏夹](http://www.lai18.com/content/956319.html)