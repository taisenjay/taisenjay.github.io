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

当数据源发生变化后，通过Adapter的notifyDataSetChanged（）方法来更新。GridView和ListView相似，同一个Adapter可以设置给ListView和GridView。

## 1.2、RecyclerView ##

ListView和GridView基本上只有布局方式不一样，其他机制基本一样。RecyclerView就是作为ListView和GridView的替代者实现的。

RecyclerView也使用Adapter,不过该Adapter是RecyclerView的静态内部类，该Adapter还有一个泛型参数VH，代表ViewHolder（该类中有一个item view字段，代表每一项数据的根视图，需要在构造函数中传递给ViewHolder对象）。用户需要实现的函数有：

- onCreateViewHolder()(告诉RecyclerView每项数据是怎样的)
- onBindViewHolder()（将数据绑定）
- getItemCount()(告诉RecyclerView有多少项数据)。

RecyclerView亮点->将布局抽象为LayoutManager:

- LinearLayoutManager，线性布局
- GridLayoutManager，网格布局
- StaggeredGridLayoutManager，交错网格布局
- 定制布局管理器实现自定义布局

除此之外，RecyclerView还可以通过ItemDecotation为Item View添加装饰；也可以通过ItemAnimator为Item View添加动画。

## 1.3、ViewPager ##

主要应用场景：与Fragment配合使用，实现滑动页面进行页面导航

常用FragmentPagerAdapter来实现。

[<font color="red">网易新闻客户端Tab</font>](http://blog.csdn.net/xiaanming/article/details/10766053)

# 2、自定义控件 #