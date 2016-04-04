---
layout: post
title: "创造丰富多彩的UI-View与动画"
excerpt: "《Android开发进阶：从小工到专家》第二章章笔记"
tags: [读书笔记，从小工到专家]
comments: true
---

# 1、重要的View控件 #

用户界面通常由Activity组成，Activity中关联了一个PhoneWindow创建，在这个窗口下则管理了一棵视图树，其顶级视图为DecorView（ViewGroup），DecorView下就是各个视图控件。
如图：

<figure>
	<img src="/images/ui_view_tree.png">
</figure>

## 1.1、ListView和GridView ##

ListView:以列表形式展示具体内容，能够根据数据的长度自适应显示。

列表数据的显示需要4个元素，分别为：

- 用来展示列表的ListView
- 用来把数据映射到ListView上的Adapter
- 需要展示的数据集
- 数据展示需要的模板

具体数据如何展示，是通过Adapter模式来实现，用户只需要复写几个特定的方法就可以将每项数据构建出来，这些方法为：

- getCount（）：获取数据的个数
- getItem（int）：获取position位置的数据
- getItemId（int）：获取position位置的数据id,一般直接返回posiiton
- getView（int，View，ViewGroup）：获取position位置上的item view视图

ListView加载时会根据数据的个数来创建itemView,每个Item View通过getView（）方法实现，在这个函数中用户必须构建Item View，然后将该position位置上数据绑定到Item View上。

Android采用视图复用原则来创建Item View，比如某个ListView中只需8个Item View就能铺满屏幕，那么即使有100个数据项需要展示，那么也只会创建8个Item View。当屏幕向下滑动时，第一项不可见后，就会进入ListView的Recycler中，Recycler会将该视图缓存。

当数据源发生变化后，通过Adapter的notifyDataSetChanged（）方法来更新。