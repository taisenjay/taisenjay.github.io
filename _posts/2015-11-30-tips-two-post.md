---
layout: post
title: "SearchView中文本提交后键盘不隐藏"
excerpt: "tips编程中遇到的小问题（2）"
tags: [android studio,stackoverflow]
comments: true
---

使用SearchView的时候，给SearchView设置监听setOnQueryTextListener,new一个OnQueryTextListener接口后需要重写OnTextSubmit（）方法。

可是在实际使用中，当提交搜索内容后，会发现，键盘根本就不会自动隐藏。

下面是解决问题的代码：

{% highlight java %}

		mSearchItem.collapseActionView();
        View view=getActivity().getCurrentFocus();
        if(view!=null){
            InputMethodManager imm= (InputMethodManager)
                    getActivity().getSystemService(Context.INPUT_METHOD_SERVICE);
            imm.hideSoftInputFromWindow(view.getWindowToken(),0);
        }
{% endhighlight %}

可以看到先调用mSearchItem这个SearchView指向的SearchItem对象的collapseActionView()方法，然后再定义一个View通过调用当前Activity(Context)的getCurrentFocus(),也就是当前焦点的界面，最后拿到InputMethodManager，调用其hideSoftInputFromWindow()方法。

问题这就解决了吗，运行之后发现然并卵。

其实还差最后一步，就是得在item文件中将mSearchItem指向的布局的showAsAction属性从原来的“ifRoom”更改为“ifRoom|collapseActionView”