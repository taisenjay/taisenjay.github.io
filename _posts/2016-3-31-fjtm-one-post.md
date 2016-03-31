---
layout: post
title: "Android的构成基石——四大组件"
excerpt: "《Android开发进阶：从小工到专家》第一章笔记"
tags: [读书笔记，从小工到专家]
comments: true
---

# Activity #

Activity:用户界面，通过加载布局来显示各种UI控件(如：TextView，Button，ListView……),并处理这些UI元素与用户的交互逻辑。Activity与Activity之间通过跳转来反映用户的操作流程，Activity是用户直接能看到的部分，因此十分重要。每个应用由多个Activity组成，应用启动后会打开一个默认的Activity，即IDE中默认的MainActivity，这个Activity在AndroidManifest.xml中有如下的intent-filter:
{% highlight xml %}

     <intent-filter>
			<action android:name="android.intent.action.Main" />
			<category android:name="android.intent.category.LAUNCHER" />
	 </intent-filter>
{% endhighlight %}

Activity生命周期：

<figure>
	<img src="http://taisenjay.com/images/activitylife.gif">

</figure>

## Activity的构成 ##

每个Activity包含一个PhoneWindow对象，PhoneWindow设置DecorView为应用窗口的根视图。在里面就是熟悉的TitleView和ContentView,没错，平时使用的setContentView()就是设置的ContentView
<figure>
	<img src="http://hujiaweibujidao.github.io/images/androidheros_ui.png">
</figure>
从图中可以看出ContentView默认的容器是一个FrameLayout。

## Activity的4种启动模式 ##

一个App包含多个Activity，而应用是通过回退栈来管理Activity实例的。由此我们就知道后启动的Activity位于先启动的Activity上方，而当前可见的Activity就位于栈顶。系统默认的启动方式是标准模式（standard），比如A中代开B，B中打开C，C中打开D，D再打开A，A在打开……（随便打开什么），那么栈底到栈顶就为ABCDA……即依次进栈。而考虑现实情况我们发现App并不能只有这种方式，因此就有了Activity的4种启动模式，即standard,singleTop,singleTask,singleInstance。

### standard ###

<figure>
	<img src="/images/standard.png">
</figure>

如图，A启动A后，又有了一个A的实例位于栈顶，标准模式不做多余判断。

### singleTop ###

<figure>
	<img src="/images/singleTop1.png">
</figure>

<figure>
	<img src="/images/singleTop2.png">
</figure>

如图，当A在栈顶时启动A，栈顶的A会复用，不再启动新的A实例。而当A不在栈顶时则无影响。

### singleTask ###

singleTask，顾名思义，在一个任务栈中只能有一个实例。如果栈中还没有该实例，会启动一个实例放于栈顶，而如果该栈中已经有该实例，则会将A实例上面的Activity都销毁，以让该实例位于栈顶（会回调该Activity的onNewIntent()方法）。
如图：

<figure>
	<img src="/images/singleTask1.png">
</figure>

<figure>
	<img src="/images/singleTask2.png">
</figure>

此外,singleTask模式启动的Activity，还有一个taskAffinity属性，即为启动的Activity指定一个不同于包名的新的任务栈，这样该Activity就将放入这个新的任务栈。

### singleInstance ###

这个启动模式跟singleTask有点类似，但它们之间的区别是，singleTask模式的Activity是可以有多个实例的，只要他们的任务栈不同即可。而singleInstance启动的Activity在系统中只能有一个实例。
如图：

<figure>
	<img src="/images/singleInstance.png">
</figure>

## FragmentActivity和Fragment ###

Fragment：在Android3.0之后引入，可以像Activity一样包含布局，区别是其实被嵌套在Activity中使用，可以理解为交给Activity托管的轻量级的Activity

使用场景示例：一个新闻应用需要显示新闻标题和具体新闻内容，而如果将这标题和内容分开放在两个Activity中感觉有点不合适，而用两个Fragment形成分栏显示，左边显示标题列表，点击具体标题时，右边Fragment显示与其对应的具体内容，这样会显得优雅，充分利用大屏资源。


# Service与AIDL #

Service：用于实现程序后台运行，因为有时候我们的App需要做一些不需要与用户交互的功能。号称可以长期在后台运行（之所以说号称，是因为本人最近开发到相关功能，发现在华为小米手机上，根本无法保证service在后台长期运行，试过网上能找到的所有方法，依然不知道什么时候就会被系统给杀死了，坑的一笔）。后台的意思是说在Activity的后台，即不像Activity一样用户可见，但其依然运行在主线程（UI线程），因此Service中不能依然执行耗时操作。当某个应用进程被系统杀死，其service也会被杀死。

## 普通Service ##

生命周期

OnCreate（），onStartCommand()和onDestroy()。

通过Context类的startService()函数，相应服务会被创建，首次创建调用onCreate,然后回调onStartCommand()函数。无论启动多少次startService()，stopService只需要调用一次即会终止服务。和Activity一样，Service需要在AndroidManifest中注册。

## IntentService ##

IntentService：通过普通Service执行后台任务（耗时操作需要在Service中再进行开辟子线程操作）略显麻烦，因此引入。其特点是他将会将用户的请求自动执行在一个子线程中，用户只需复写onHandlerIntent函数（在其中执行耗时操作）即可。注意：IntentService在任务执行完毕后会调用stopSelf自我销毁，因此其适合完成一些短期的耗时任务（感觉有点鸡肋啊）

## 运行在前台的任务 ##

Service默认执行在后台（不可见），因此优先级较低，被kill的几率更高。所以可以通过Service的startForeground（），调用该方法还会在通知栏出现相关信息（Notification）。

## AIDL（Android接口定义语言）##

