---
layout: post
title: 用Material Design创建App
excerpt: "Create Apps With Material Design."
modified: 2015-09-18
tags: [android, material design,google]
comments: true
image:
  feature: sample-image-5.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->



相信大家对Material Design这个概念都比较熟悉了吧，在我看来Material Design就像是一套界面设计标准。最近刚刚搭建了博客但不知道第一篇Blog发什么好，索性就来翻译一波Android官方文档吧翻译的同时顺便还能学习学习（PS：本人英语较渣，所以对我来说这是个很大的挑战）

##本文将向你展示
- Material主题
- cards和lists控件
- 自定义shadows和view clipping
- 矢量图形（Vector Drawables）
- 自定义动画（Animation）

##开始你的Material Design
创建Material Design应用，你需要

1、回顾[material design specification](http://www.google.com/design/spec)

2、为你的App应用Material主题

3、按照material design guidelines来创建你的布局（layouts）

4、为你的视图组件（views）指定elevation用来投射阴影（shadows）

5、为lists和cards使用系统控件（widgets）

6、在你的App中自定义动画（Animation）

If you are creating a new app with material design features, the material design guidelines provide you with a cohesive design framework. Follow those guidelines and use the new functionality in the Android framework to design and develop your app.
意思就是说按照[material design guidelines](http://www.google.com/design/spec)来设计

##应用Material主题
在你的app中使用Material主题，需要你指定一个主题继承`android:Theme.Material:`

```xml

		<!-- res/values/styles.xml -->
 		<!-- res/values/styles.xml -->
		<resources>
  			<!-- your theme inherits from the material theme -->
  			<style name="AppTheme" parent="android:Theme.Material">
    		<!-- theme customizations -->
  			</style>
	    </resources>
```

##设计布局（layouts）
除了应用和自定义material主题外，你还应该遵从material design guideline设计布局，
设计时注意以下几点

- Baseline grids(基线网格？)
- Keylines(符号注解行？)
- Spacing(间隔)
- Touch target size(触摸目标尺寸)
- Layout Structure(布局结构)

##为你的Views指定elevation
视图（view）可以投射阴影（shadow），而elevation值得大小就决定了其阴影大小和画阴影的顺序，在你的layout中用`android:elevation`实现

```xml

	<TextView
    	android:id="@+id/my_textview"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content"
   	 	android:text="@string/next"
    	android:background="@color/white"
    	android:elevation="5dp" />
```

新属性`translationZ`可以让你创建出暂时改变view的elevation大小的动画（animation）,显然这在触摸回应时非常有用

##创建lists和cards
Recycleview是listview的一个进化版本，它是一个强大的滑动组件，与经典的ListView相比，同样拥有item回收复用的功能，但是直接把viewholder的实现封装起来，用户只要实现自己的viewholder就可以了，该组件会自动帮你回收复用每一个item。它不但变得更精简，也变得更加容易使用，而且更容易组合设计出自己需要的滑动布局。普遍认为其将来能替代listview。（介绍那么多说明比较重要）cardview可以让你用类似卡片的形式进行信息展示，在不同的app上拥有了一致相似的外观。下面向你展示cardview在layout中的使用

```xml

	<android.support.v7.widget.CardView
    	android:id="@+id/card_view"
    	android:layout_width="200dp"
    	android:layout_height="200dp"
    	card_view:cardCornerRadius="3dp">
	</android.support.v7.widget.CardView>
```

##自定义动画
Android5.0包括了新的API用来自定义动画。比如你可以在一个activity内激活activity过度动画和定义退出动画。示例如下

```java

		public class MyActivity extends Activity {

   		@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	// enable transitions
        	getWindow().requestFeature(Window.FEATURE_CONTENT_TRANSITIONS);
        	setContentView(R.layout.activity_my);
    	}

    	public void onSomeButtonClicked(View view) {
        	getWindow().setExitTransition(new Explode());
        	Intent intent = new Intent(this, MyOtherActivity.class);
        	startActivity(intent,
                      	ActivityOptions
                          	.makeSceneTransitionAnimation(this).toBundle());
    		}
		}
```

当你从此activity开启另一个activity时，退出动画即被激活

##未完待续







