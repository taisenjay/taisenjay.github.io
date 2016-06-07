---
layout: post
title: "Android的构成基石——四大组件"
excerpt: "《Android开发进阶：从小工到专家》第1章读书笔记"
tags: [读书笔记，从小工到专家]
comments: true
---

# 1、Activity #

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

## 1.1、Activity的构成 ##

每个Activity包含一个PhoneWindow对象，PhoneWindow设置DecorView为应用窗口的根视图。在里面就是熟悉的TitleView和ContentView,没错，平时使用的setContentView()就是设置的ContentView
<figure>
	<img src="http://hujiaweibujidao.github.io/images/androidheros_ui.png">
</figure>
从图中可以看出ContentView默认的容器是一个FrameLayout。

## 1.2、Activity的4种启动模式 ##

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

## 1.3、FragmentActivity和Fragment ###

Fragment：在Android3.0之后引入，可以像Activity一样包含布局，区别是其实被嵌套在Activity中使用，可以理解为交给Activity托管的轻量级的Activity

使用场景示例：一个新闻应用需要显示新闻标题和具体新闻内容，而如果将这标题和内容分开放在两个Activity中感觉有点不合适，而用两个Fragment形成分栏显示，左边显示标题列表，点击具体标题时，右边Fragment显示与其对应的具体内容，这样会显得优雅，充分利用大屏资源。


# 2、Service与AIDL #

Service：用于实现程序后台运行，因为有时候我们的App需要做一些不需要与用户交互的功能。号称可以长期在后台运行（之所以说号称，是因为本人最近开发到相关功能，发现在华为小米手机上，根本无法保证service在后台长期运行，试过网上能找到的所有方法，依然不知道什么时候就会被系统给杀死了，坑的一笔）。后台的意思是说在Activity的后台，即不像Activity一样用户可见，但其依然运行在主线程（UI线程），因此Service中不能依然执行耗时操作。当某个应用进程被系统杀死，其service也会被杀死。

## 2.1、普通Service ##

生命周期

OnCreate（），onStartCommand()和onDestroy()。

通过Context类的startService()函数，相应服务会被创建，首次创建调用onCreate,然后回调onStartCommand()函数。无论启动多少次startService()，stopService只需要调用一次即会终止服务。和Activity一样，Service需要在AndroidManifest中注册。

## 2.2、IntentService ##

IntentService：通过普通Service执行后台任务（耗时操作需要在Service中再进行开辟子线程操作）略显麻烦，因此引入。其特点是他将会将用户的请求自动执行在一个子线程中，用户只需复写onHandlerIntent函数（在其中执行耗时操作）即可。注意：IntentService在任务执行完毕后会调用stopSelf自我销毁，因此其适合完成一些短期的耗时任务（感觉有点鸡肋啊）

## 2.3、运行在前台的任务 ##

Service默认执行在后台（不可见），因此优先级较低，被kill的几率更高。所以可以通过Service的startForeground（），调用该方法还会在通知栏出现相关信息（Notification）。

## 2.4、AIDL（Android接口定义语言）##

AIDL（接口描述语言）：用于进程间通信。编译器根据AIDL文件生成一系列对应的Java类，通过预先定义的接口以及Binder机制来进行进程间通信。换句话说，AIDL就是定义了一个接口，客户端（调用端）通过bindService来与远程服务端建立一个连接（在该连接建立时会返回一个IBinder对象，该对象是服务端的BinderProxy），在建立连接时，客户端通过asInterface函数将该BinderProxy对象包装成本地的Proxy,并将远程服务端的BinderProxy对象赋值给Proxy类的mRemote字段，就是通过mRemote执行远程函数调用。

# 3、BroadCast（广播）#

BroadCast：应用程序之间传输信息，一个广播可以有任意个接收者，是典型的“发布——订阅”模式。特点是接收、发送双方完全解耦。广播机制有3个基本要素：BroadCast（发送），BroadCastReceiver（接收），Intent（传递信息）。广播又可以分为普通广播、有序广播、本地广播和Sticky广播。

## 3.1、普通广播 ##

普通广播完全异步，Context.sendBroadCast()发送，recivers（接收方们）执行顺序不明，每个接收者不能将处理结果传递给下一个接收者，也无法终止广播Intent的传播，广播会直到没有与之匹配的接收器为止。

使用：第一步，定义一个广播接收器（定义一个类继承BroadCastReceiver，重写onReceive（）方法）；第二步，注册广播（静态注册和动态注册），静态注册即在AndroidManifest.xml中注册，类似Activity和Service注册方式，动态注册即在代码中通过调用registerReciver()方法。注意：在Activity或Fragment中动态注册广播，不要忘了在onDestroy（）时注销广播。

## 3.2、有序广播 ##

有序广播通过Context.sendOrderedBroadcast（）来发送，接收器们按照优先级来先后执行，优先级是在receiver的intent-filter中的priority属性来设置，数值越大优先级越高。先执行的广播通过setResult()函数将结果出给下个接收器，后执行的通过getResult（）函数取得上个广播接收器返回的结果，并可以用abortBroadCast（）方法丢弃该广播，使广播不再传送到别的接收器。

## 3.3、本地广播 ##

LocalBroadCastManager：在本地广播出现之前的广播都是全局的（也就是所有应用都可以接收到，这样会有安全隐患），所以LocalBroadCastManager可以做到让广播只在进程内使用。
使用：将调用的Context的sendBroadCast,registerReceiver等都替换为LocalBroadCastManager.getInstance(Context context)中对应的函数即可。

## 3.4、sticky广播 ##

通过context.sendStickyBroadCast（）方法来发送，此方法发送的广播会一直滞留，当有匹配的接收器被注册后，该广播接收器就会接受到该广播。
使用时需要权限

	<use-permission android:name="andoid.permission.BROADCAST_STICKY"/>

sendStickyBroadCast()只保留最后一条广播，并且一直保留下去（意思是即使已经有广播接收器处理了该广播，当再有匹配的广播接收器被注册时，此广播仍会被接收）。如果只想执行一次广播，可以通过removeStickyBroadcast（）方法实现。

# 4、ContentProvider（外共享数据）#

通过ContentProvider把应用内的数据共享给其他应用访问。ContentProvider统一了数据的访问方式，使其他应用可以对本应用内数据进行增删改查操作。其实际上是对SQliteOpenHelper的进一步封装，通过Uri映射来定位。


# 本章小结 #

Android系统的设计是基于组件化的思想的。Activity负责UI界面的管理，Service提供与UI无关的服务，BroadCast用于跨进程数据传输，ContentProvider用于共享数据，而Intent就是作为粘合剂将它们联结在一起，使彼此之间机会没有耦合。