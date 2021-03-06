---
layout: post
title: "创造丰富多彩的UI-View与动画"
excerpt: "《Android开发进阶：从小工到专家》第2章读书笔记"
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

自定义View类型：

- 继承自View完全自定义
- 继承现有控件（如ImageView）实现特定效果
- 继承自ViewGroup实现布局

自定义View的重点有View的测量与布局、View的绘制、处理触摸事件、动画等。

## 2.1、自定义View ##

继承自View完全实现自定义控件是最自由的一种实现，但是也相对复杂。你需要正确地测量View的尺寸，并且手动绘制各种视觉效果。

对于继承自View类的自定义控件来说，核心的步骤分别为尺寸测量与绘制，对应的函数式onMeasure、onDraw。因为View类型的子类也是视图树的叶子结点，因此其只需负责绘制好自身内容即可。

实现过程：

- 继承自View创建自定义控件
- 如有需要自定义View属性，也就是在values/attrs.xml中定义属性集
- 在xml中引入命名空间，设置属性
- 在代码中读取xml中的属性，初始化视图
- 测量视图大小
- 绘制视图内容

## 2.2、View的尺寸测量 ##

在Android中，视图树在创建时会调用根视图的measure（测量）、layout（布局）、draw（绘制）三个函数。其中对于非ViewGroup的View而言，不需要layout。

视图树渲染时，系统的绘制流程会从ViewRoot的PerformTraversals()中开始，在其内部调用measure（widthMeasureSpec,heightMeasureSpec）方法,这两个参数分别用于确定视图的宽度、高度的规格和大小。MeasureSpec的值由specSize（规格）和specMode（大小）共同组成。

SpecMode（模式）类型：

- EXACTLY，表示父视图希望子视图的大小由specSize的值确定（<font color="green">系统默认会按照此规则来设置子视图大小</font>）。<font color="red">match_parent和具体数值（如20dp）</font>对应这个模式。
- AT_MOST，表示子视图最大只能是specSize中指定的大小（<font color="green">系统默认会按照此规则来设置子视图大小</font>）。一般情况，<font color="red">wrap_content对应这种模式</font>。
- UNSPECIFIED，表示开发人员完全自由设置视图的大小，没有任何限制。这种情况很少见，基本不常用。

MeasureSpec的来源：ViewRootImpl（视图树控制类）的measureHierarchy函数会通过getRootMeasureSpec()方法来获取widthMeasureSpec和heightMeasureSpec。构建完根视图的MeasureSpec后会执行performMeasure函数从根视图开始一层一层测量视图的大小，最终调用setDimension函数设置该视图的大小。

## 2.3、Canvas与Paint ##

对于Android来说，整个View就是一张画布（Canvas），开发者可以通过Paint（画笔）在这张画布上绘制各种各样的图形、元素，例如矩形、圆形、椭圆、文字、圆弧、图片等。需要注意Canvas类的save（保存画布状态）和restore（恢复到上一个保存的画布状态）函数，应用在画布进行了平移，缩放，旋转，倾斜、裁剪等操作后。

## 2.4、自定义ViewGroup ##

自定义ViewGroup是另一种重要的自定义View形式，不同在于，开发者还需要实现onLayout方法（将ViewGroup下中包含的View进行合理布局）。

## 3、Scroller的使用 ##

Scroller是一个帮助View滚动的辅助类。Scroller 封装了滚动时间、要滚动的目标x轴和y轴，以及在每个时间内View应该滚动到的（x,y）轴的坐标点。

示例：

{% highlight Java %}

     public void ScrollLayout extends FrameLayout{
		private String TAG = ScrollLayout.class.getSimpleName();
		Scroller mScroller;
		
		public ScrollLayout(Context context ){
			super(context);
			mScroller = new Scroller(context);
		}

		//该函数会在View重绘时被调用
		@Override
		public void computeScroll(){
			if(mScroller.computeScrollOffset()){
				//滚动到此，View应该滚动到的x,y坐标上
				this.scrollTo(mScroller.getCurX(),mScroller.getCurrY());
				//请求重绘该View，从而又会导致computeScroll被调用，然后继续滚动，直到
				//computeScrollOffset返回false
				this.postInvalidate();
			}
		}

		//调用这个方法进行滚动，这里我们只滚动竖直方向
		public void scrollTo(int y){
			//参数1和2分别为滚动的起始点的水平、竖直方向的滚动偏移量
			//3和4为水平和竖直方向上滚动的距离
			mScroller.startScroll(getScrollX(),getScrollY(),0,y);
			this.invalidate();
		}
	 }

	//调用代码
	ScrollLayout scrollView = new ScrollLayout(getContext());
	scrollView.scrollTo(100);
{% endhighlight %}

示例代码分析：首先调用scrollTo(int y),然后在该方法中通过mScroller.startScroll（）方法来设置滚动的参数，再调用invalidate()方法使得该View重绘。重绘时会调用computeScroll(),在该方法中通过mScroller.computeScrollOffset（）判断滚动是否完成，如果返回true，代表没有滚动完成，此时把该View滚动到此刻应该滚动的x,y位置，这个位置通过mScroller的getCurrX和getCurrY获得。然后继续调用重绘方法，继续执行滚动过程，直至滚动完成。

[<font color="blue">下拉刷新组件实现</font>](https://github.com/hehonghui/android_my_pull_refresh_view)

# 4、让应用更精彩——动画 ##

动画分类：帧动画、补间动画（最早），属性动画（Android3.0之后），Vector Drawable（Android5.0）
动画实质：在指定的时间内持续的修改某个属性的值，使该值在指定取值范围内平滑的过渡。

## 4.1、帧动画 ##

Frame动画（帧）是一系列图片按照一定的顺序展示的过程（类似于放电影）。
实现方式：1、在xml中定义；2、在Java代码中实现。

在xml中实现：放置在/res目录下的anim目录或drawable目录下，必须由<animation-list>元素作为根元素，其可以包含一个或多个<item>元素。andorid:onshot如果定义为true的话表示此动画只会执行一次，false则循环。<item>元素代表一帧动画，android:drawable指定此帧对应的图片，android:duration代表此帧持续的时间，单位为毫秒。

示例(hear_anim.xml)：

{% highlight xml %}
	
	<animation-list xlmns:andrid="http://…………………………"
			android:oneshot="true">
		<item
			android:duration="500"
			android:drawable="@drawable/ic_heart_0">
		<item
			android:duration="500"
			android:drawable="@drawable/ic_heart_1">
		<item
			android:duration="500"
			android:drawable="@drawable/ic_heart_2">
		<item
			android:duration="500"
			android:drawable="@drawable/ic_heart_3">
		<item
			android:duration="500"
			android:drawable="@drawable/ic_heart_4">
	</animation-list>
{% endhighlight %}

再将该动画xml设置给某个View，比如：

{% highlight xml %}
	
	<ImageView
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:background="@drawable/heart_anim"/>
{% endhighlight %}

但是动画并不会在View显示时自动播放，还需要通过代码启动

{% highlight Java %}
	
	((AnimationDrawable)mImageView.getBackground()).start();
{% endhighlight %}

当然，也可以完全用代码实现帧动画：

{% highlight Java %}
	
	AnimationDrawable anim = new AnimationDrawable();
	for (int i=0;i<=4;i++){
		//获取图片的资源id
		int id = getResources().getIdentifier("ic_heart_"+ 	i,"drawble",getPackageName());
		Drawable drawable = getResources().getDrawable(id);
		//将Drawable添加到帧动画中
		anim.addFrame(drawable,300);
	}
	anim.setOneShot(false);
	//将动画设置为ImageView的背景
	mImageView.setBackgroundDrawable(anim);
	anim.start();
{% endhighlight %}

## 4.2、补间动画 ##

tween（补间）动画是操作某个控件让其展现出旋转、渐变、移动、缩放的一种转换过程。

xml实现：
<set>为根节点（对应Java类-AnimationSet），<set>的属性有android:interpolator(代表一个插值器资源)，android:shareInterpolator代表<set>里面的多个动画（有<alpha><scale><translate><rotate>）是否共享插值器，默认为true。

- <alpha>(Java对应类：AlphaAnimation)渐变动画
	- android:fromAlpha:起始alpha值（浮点值，范围在0.0——1.0）
	- adnroid:toAlpha:结尾alpha值

- <scale>（Java对应类：ScaleAnimation）缩放动画
	- android:fromXScale:起始X方向上相对自身的缩放比例（浮点值，1.0代表无变化，0.5代表缩小一倍，2.0代表放大一倍）
	- android:toXScale:结尾X方向上相对自身的缩放比例
	- android:fromYScale:起始y方向上相对自身的缩放比例（浮点值，1.0代表无变化，0.5代表缩小一倍，2.0代表放大一倍）
	- android:toYScale:结尾y方向上相对自身的缩放比例
	- android:pivotX:缩放的中轴点的X坐标
	- android:pivotY:缩放的中轴点的Y坐标

- <translate>（Java对应类：TranslateAnimation）位移动画
	- android:fromXDelta:起始X方向的位置（支持3种单位：浮点，num%，num%p）
	- android:toXDelta:结尾X方向的位置
	- android:fromYDelta:起始Y方向的位置
	- android:toYDelta:结尾Y方向的位置 

- <rotate>(Java对应类：RotateAnimation)旋转动画
	- android:fromDegrees:起始角度（浮点值，度）
	- android:toDegrees:结尾角度（浮点值，度）
	- android:pivotX:旋转的中轴点的X坐标（支持3种单位：浮点，num%，num%p）
	- android:pivotY:旋转的中轴点的Y坐标 

## 4.3、属性动画 ##

属性动画（不仅限于移动，缩放，旋转，淡入淡出）不是针对View来设计的，不再只是一种视觉上的效果。	其实际上是真正的在一定时间段内不断修改某个对象的某个属性值的机制。我们需要告诉系统动画操作的属性，动画时长，哪种动画，以及动画的初始结束值。

## 4.3.1、属性动画的核心类——ValueAnimator ##

ValueAnimator：

- 作用：在一定时间内不断修改对象的某个属性值
- 原来：内部使用一种时间循环的机制来计算值与值之间的动画过渡
- 使用：将属性的取值范围、运行时长提供给ValueAnimator
- 此外：其还负责管理动画的播放次数、播放模式，以及对动画设置监听

通常我们通过ofFloat，ofInt等静态工厂函数构建ValueAnimator，示例：

{% highlight Java %}

		private void startValueAnimation(){
			ValueAnimator animator = ValueAnimator.ofFloat(0.0f,1.0f);
			animator.setDuration(1000);
			animator.addUpdateListener(mAnimationListener);
		}
		ValueAnimator.AnimatorUpdateListener mAnimationListener = new
				ValueAnimator.AnimatorUpdateListener(){
			@Override
			public void onAnimationUpdate(ValueAnimator animation){
				float newValue = animation.getAnimatedValue();
				//将不断变化的属性数值设置给具体的View
				//这里设置的是alpha(透明度)这个属性（此动画完成了透明度从0.0过渡到1.0的效果）
				myView.setAlpha(newValue);
				Log.e("","### 新的属性值： "+ newValue)；
			}
		};
		
{% endhighlight %}

也可以在res/anim目录下的xml文件中定义该动画，然后在代码中获取：

{% highlight xml %}
		//R.anim.value_animator
		<animator xmlns: ……
			android:valueFrom="0.0"
			android:valueTo="1.0"
			android:valueType="floatType"/>		

		ValueAnimator animator = AnimatorInflater.loadAnimator	(getApplicationContext(),R.anim.value_animator);
		
{% endhighlight %}

## 4.3.2、对任意属性进行动画操作——ObjectAnimator ##

ValueAnimator功能强大，自由度高，但是使用起来效率较低，。实际开发中用的更多的应该是ObjectAnimator。
ObjectAnimator是ValueAnimator的子类，其功能强大，能够操作任意对象的任意属性。

{% highlight java %}

	ObjectAnimator animator = ObjectAnimator.ofFloat(myView,"alpha",1.0f,0.3f,0.7f);
	animator.setDuration(2000);
	animator.start();
{% endhighlight %}

## 4.3.3、实现丰富多彩的动画效果——AnimatorSet ##

AnimatorSet将多个动画组合起来。AnimatorSet提供一个play(Animator)方法，然后返回一个AnimatorSet.Builder的实例，AnimatorSet.Builder有以下5个核心方法：

- after(Animator anim)：前一个动画执行完后再执行这个动画
- after(long delay)：前一个动画执行完后延迟指定毫秒数
- before(Animator anim):在anim动画执行完之前再执行调用after函数的动画
- with(Animator anim):将现有动画和传入的动画同时执行
- playTogether(Animator…… anims):将多个动画一起执行

示例：

{% highlight java %}

	AnimatorSet animSet = new AnimatorSet();
	animSet.play(anim1).with(anim2).after(anim3);
	animSet.setDuration(2000);
	animSet.start();
{% endhighlight %}

## 4.3.4、动画执行时间——TypeEvaluator与TimeInterpolator ##

TypeEvalutor：根据当前动画已执行时间占总时间的百分比来计算新的属性值。

核心方法：public abstract T evaluate(float fraction,T startValue,T endValue)
参数1：已执行时间占总时间的百分比（0.0——1.0），参数2：属性的起始值，参数3：属性的最终值

示例：

{% highlight java %}

	//实现
	public class TranslateXEvalutor implements TypeEvaluator<Integer>{
		@Override
		public Integer evaluate(float fraction,Integer startValue,Integer endValue){
			//计算新的属性值
			int newValue = startValue + (int)(fraction + (endvalue - startValue));
			return newValue;
		}	
	}
	//使用
	private void useCustomEvaluator(){
		ObjectAnimator animator = ObjectAnimator.ofObject(mView,"x",new TranslateXEvaluator(),0,200);
		animator.setDuration(500);
		animator.start();
	}
{% endhighlight %}

TimeInterpolator:修改动画已执行时间与总时间的百分比，也就是fraction参数值。

- LinearInterpolator：匀速线性插值
- AccelerateInterpolator:加速插值器
- DecelerateInterpolator:减速插值器
- AccelerateDecelerateInterpolator：加减速插值器

过程：在获得已执行时间百分比之后，通过调用TimeInterpolator的getInterpolation函数来对该百分比做出修改，并且返回。