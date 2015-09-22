---
layout: post
title: "Activity生命周期"
excerpt: "android基础知识点（1）"
tags: [activity,lifecycle]
comments: true
---

废话不多说，上大图。（这种基础知识点必须要掌握啊）

<figure>
	<img src="/images/activitylife.gif">
	<figcaption>图1.Activity生命周期</figcaption>
</figure>

这张图片真的看懂了吗，看懂了吗，看懂了吗，重要的事情说三遍。

##举例

- 你正用某浏览器看着小说，这时来了一个电话，你怒点拒接，回到你的小黄文，这些过程调用了哪些生命周期方法呢？

答案是：OnPause(小说)->OnCreate(电话)->OnStart(电话)->OnResume(电话)->OnStop(小说)->
OnPause(电话)->OnStop(电话)->OnDestroy(电话)->OnRestart(小说)

看完小例子是不是感觉融汇贯通了，我觉得难以区分的就是onPause和OnStop，其实也很简单，看不见了就是OnStop了，如例子中电话界面覆盖了小说界面，小说见面看不到当然就Stop了。再比如这时你的浏览器弹出一个广告弹窗，但你下面的小说界面依然清晰可见，此时小说界面就是在OnPause了，也就是俗称的它失去了焦点，焦点被与你正在交互的窗口（广告）所拿到。

如果你还有疑惑的话请看[两分钟彻底让你明白Android Activity生命周期(图文)!](http://blog.csdn.net/android_tutor/article/details/5772285)和[Android四大基本组件介绍与生命周期](http://www.cnblogs.com/bravestarrhu/archive/2012/05/02/2479461.html)来自我们Android界精神领袖[stormzhang](http://www.stormzhang.com/)推荐的优秀博文。

