---
layout: post
title: "Service生命周期"
excerpt: "android基础知识点（2）"
tags: [service,lifecycle]
comments: true
---

依然先上图，在说话

<figure>
	<img src="/images/servicelife.jpg">
	<figcaption>Service生命周期</figcaption>
</figure>

看完图片就有疑惑了，怎么有两个流程图呢？是的，你想的没错！Service有两种启动方式，分别是startService()和bindService()。要理解这一点，我想首先要明白service的特性，顾名思义，service指服务，服务嘛，你懂的，在你看不见的地方有条不紊的运行着。所以service是一个运行于后台，没有用户界面的组件，也就是所谓的独立于UI。而且它和Activity这样的用户界面组件不一样，它有一个较长的生命周期。

举个例子
在一个媒体播放器的应用中，应该会有多个activity，让使用者可以选择歌曲并播放歌曲。然而，音乐重放这个功能并没有对应的activity，因为使用者当然会认为在导航到其它屏幕时音乐应该还在播放的。在这个例子中，媒体播放器这个activity 会使用Context.startService()来启动一个service，从而可以在后台保持音乐的播放。同时，系统也将保持这个service 一直执行，直到这个service 运行结束。另外，我们还可以通过使用Context.bindService()方法，连接到一个service 上（如果这个service 还没有运行将启动它）。当连接到一个service 之后，我们还可以service 提供的接口与它进行通讯。拿媒体播放器这个例子来说，我们还可以进行暂停、重播等操作。


服务不能自己运行,需要通过Contex.startService()或Contex.bindService()启动服务

通过startService()方法启动的服务于调用者没有关系,即使调用者关闭了,服务仍然运行想停止服务要调用Context.stopService(),此时系统会调用onDestory(),使用此方法启动时,服务首次启动系统先调用服务的onCreate()-->onStart(),如果服务已经启动再次调用只会触发onStart()方法

使用bindService()启动的服务与调用者绑定,只要调用者关闭服务就终止,使用此方法启动时,服务首次启动系统先调用服务的onCreate()-->onBind(),如果服务已经启动再次调用不会再触发这2个方法,调用者退出时系统会调用服务的onUnbind()-->onDestory(),想主动解除绑定可使用Contex.unbindService(),系统依次调用onUnbind()-->onDestory();

关于service,还有比较容易让人混淆的一点是它和子线程的关系，可能是因为后台这个概念，好像service就是用来执行耗时操作的。然而事实是service和子线程半毛钱关系都没有，service其实是运行于主线程之内的，只是它所谓的后台就是指不依赖UI，所以我们会在service中开辟子线程。如果在Activity中开辟子线程的话，一个是当Activity被销毁之后，就没有任何其它的办法可以再重新获取到之前创建的子线程的实例，再一个是Activity中创建的子线程，另一个Activity无法对其进行操作。而Service却可以和所有的Activity进行关联，所以这是赤裸裸的优势啊。

本文同样参照于[stormzhang](http://www.stormzhang.com/)的著名博文[Android学习之路](http://www.stormzhang.com/android/2014/07/07/learn-android-from-rookie/)中的链接。