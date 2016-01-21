---
layout: post
title: "View的绘制流程及分发机制"
excerpt: "Android基础要点（3）"
tags: [绘制流程,传递机制，面试要点]
comments: true
---


## View的绘制流程 ##
View的绘制主要涉及三个方法：onMeasure（）、onLayout（）和onDraw（）。
1、onMeasure主要用于计算view的大小，onLayout主要用于确定view在ContentView中的位置，onDraw主要是绘制view;
2、在执行onMeasure（）、onLayout（）方法时都会先通过相应的标志位或者对应的坐标点来判断是否需要执行相应的函数，如我们经常调用的invalidate（）方法就只会执行onDraw（）方法，因为此时的视图大小和位置都没有发生改变，除非调用requestLayout方法完整强制进行view的绘制，从而执行上面三个方法。

[公共技术点之view绘制流程](http://a.codekk.com/detail/Android/lightSky/%E5%85%AC%E5%85%B1%E6%8A%80%E6%9C%AF%E7%82%B9%E4%B9%8B%20View%20%E7%BB%98%E5%88%B6%E6%B5%81%E7%A8%8B)

## View的事件分发机制 ##
所谓事件分发机制其实就是对MotionEvent事件的分发过程。所有的Touch事件都被封装成了MotionEvent对象，包括Touch的位置，时间，历史记录以及第几个手指（多点触摸）等。当一个MotionEvent产生了之后，系统需要把这个事件传递给一个具体的View，而这个传递的过程就是分发过程。点击事件的分发过程由三个很重要的方法共同来完成：dispatchTouchEvent、onInterceptTouchEvent和onTouchEvent。

- public boolean dispatchTouchEvent(MotionEvent ev)：用来进行事件的分发。如果事件能够传递给当前View，那么此方法移一定会被调用，返回结果受当前View的onTouchEvent和下级View的dispatchTouchEvent方法的影响，表示是否消耗当前事件。
- public boolean onInterceptTouchEvent（MotionEvent ev）：在上述方法内部调用，用来判断是否拦截某个事件，如果当前View拦截了某个事件，那么在同一个事件序列当中，此方法不会被再次调用，返回结果表示是否拦截当前事件。
- public boolean onTouchEvent（MotionEvent ev）在dispatchTouchEvent方法中调用，用来处理点击事件，返回结果表示是否消耗当前事件，如果不消耗，则在同一个时间序列中，当前View无法再次接收到事件。

这三个方法的关系可以用伪代码来表示：

{% highlight java %}

     public  boolean  dispatchTouchEvent(MotionEvent ev){
          boolean  consume = false ;
          if (onInterceptTouchEvent(ev)){
               consume = onTouchEvent(ev);
          }else{
               consume = child.dispatchTouchEvent(ev);
          }
          return consume;
     }
{% endhighlight %}

[公共技术点之View事件传递](http://a.codekk.com/detail/Android/Trinea/%E5%85%AC%E5%85%B1%E6%8A%80%E6%9C%AF%E7%82%B9%E4%B9%8B%20View%20%E4%BA%8B%E4%BB%B6%E4%BC%A0%E9%80%92)
