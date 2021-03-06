---
layout: post
title: 用Material Design创建App
excerpt: "翻译（Google官方文档）"
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

<font color="blue">（最后一次更新，10月17号）本篇文章翻译到一半的时候才发现原来居然那么长，顿时好后悔，中途有过好几次想要放弃，但是想到自己的第一篇Blog就半途而废实在是说不过去，因此只能每天抽出一点零碎的时间来更新，现在一看居然都过去一个月了（汗！），好在是自己坚持将其完成了。谈点感受，怎么说呢，自己选择的路就一定要坚持到底，不轻言放弃！</font>

##本文将向你展示
- Material主题
- cards和lists控件
- 自定义shadows和裁剪view
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

###应用Material主题
在你的app中使用Material主题，需要你指定一个主题继承`android:Theme.Material:`

{% highlight xml %}

		<!-- res/values/styles.xml -->
 		<!-- res/values/styles.xml -->
		<resources>
  			<!-- your theme inherits from the material theme -->
  			<style name="AppTheme" parent="android:Theme.Material">
    		<!-- theme customizations -->
  			</style>
	    </resources>
{% endhighlight %}

###设计布局（layouts）
除了应用和自定义material主题外，你还应该遵从material design guideline设计布局，
设计时注意以下几点

- Baseline grids(基线网格？)
- Keylines(符号注解行？)
- Spacing(间隔)
- Touch target size(触摸目标尺寸)
- Layout Structure(布局结构)

###为你的Views指定elevation
视图（view）可以投射阴影（shadow），而elevation值得大小就决定了其阴影大小和画阴影的顺序，在你的layout中用`android:elevation`实现

{% highlight xml %}

	<TextView
    	android:id="@+id/my_textview"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content"
   	 	android:text="@string/next"
    	android:background="@color/white"
    	android:elevation="5dp" />
{% endhighlight  %}

新属性`translationZ`可以让你创建出暂时改变view的elevation大小的动画（animation）,显然这在触摸回应时非常有用

###创建lists和cards
Recycleview是listview的一个进化版本，它是一个强大的滑动组件，与经典的ListView相比，同样拥有item回收复用的功能，但是直接把viewholder的实现封装起来，用户只要实现自己的viewholder就可以了，该组件会自动帮你回收复用每一个item。它不但变得更精简，也变得更加容易使用，而且更容易组合设计出自己需要的滑动布局。普遍认为其将来能替代listview。（介绍那么多说明比较重要）cardview可以让你用类似卡片的形式进行信息展示，在不同的app上拥有了一致相似的外观。下面向你展示cardview在layout中的使用

{% highlight xml %}

	<android.support.v7.widget.CardView
    	android:id="@+id/card_view"
    	android:layout_width="200dp"
    	android:layout_height="200dp"
    	card_view:cardCornerRadius="3dp">
	</android.support.v7.widget.CardView>
{% endhighlight %}

###自定义动画
Android5.0包括了新的API用来自定义动画。比如你可以在一个activity内激活activity过度动画和定义退出动画。示例如下

{% highlight java %}

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
{% endhighlight %}

当你从此activity开启另一个activity时，退出动画即被激活

##使用Material主题
新的Material主题提供以下特点

- 可以让你设置调色板的系统组件
- 系统组件的触摸反馈动画
- activity过渡动画

你可以根据你的品牌识别用一种你可以控制的调色板来自定义Material主题的外观，你可以用主题的参数来给你的ActionBar和StatusBar着色，就像图3.

系统组件有新的设计和触摸反馈动画。你可以调色板，反馈动画和你app的activity过渡。

Material主题定义如下

- @android:style/Theme.Material (dark version)
- @android:style/Theme.Material.Light (light version)
- @android:style/Theme.Material.Light.DarkActionBar

想看到你可以使用的material styles集合？请参考R.style的API

<figure class="half">
	<a href="/images/MaterialDark.png"><img src="/images/MaterialDark.png"></a>
    <a href="/images/MaterialLight.png"><img src="/images/MaterialLight.png"></a>
	<figcaption>图1.dark material theme,图2.light material theme</figcaption>
</figure>

>注意：material主题只有Android5.0（API21）以上支持。` v7 Support Libraries`为一些控件
>提供material design style的一些主题，以及对自定义调色板提供支持。

###自定义调色板（color palette）
自定义主题的基础颜色来搭配你的品牌，当你继承material主题时，你应该用主题参数来自定义颜色。
示例如下

{% highlight xml %}

		<resources>
  			<!-- inherit from the material theme -->
  			<style name="AppTheme" parent="android:Theme.Material">
    			<!-- Main theme colors -->
    			<!--   your app branding color for the app bar -->
    			<item name="android:colorPrimary">@color/primary</item>
    			<!--   darker variant for the status bar and contextual app bars -->
    			<item name="android:colorPrimaryDark">@color/primary_dark</item>
    			<!--   theme UI controls like checkboxes and text fields -->
    			<item name="android:colorAccent">@color/accent</item>
  			</style>
		</resources>
{% endhighlight %}



###自定义StatusBar

![Smithsonian Image]({{site.url}}/images/ThemeColors.png)
{: .image-pull-right}

material主题让你非常简单的自定义statusbar,你可以指定一个适合你品牌的颜色并且提供足够的对比来显示白色的status图标.为statusbar自定义颜色，你应该在继承material主题的时候使用`android:statusBarColor`参数，`android:statusBarColor` 默认继承`android:colorPrimaryDark`的值。
你也可以在statusbar后台自己绘制。比如，如果你想让statusbar在一张图片上透明的显示，用一种精细的渐变暗来保证白色的status图标可见。为了达到这种效果，需要将`android:statusBarColor`参数设为`@android:color/transparent`。你也可以为了动画（animation）和褪色（fading）使用
` Window.setStatusBarColor()`方法。

>注意：statusbar几乎永远要有一个清晰的来自主要工具栏（primary toolbar）的描述，除非你想要>在这些bars后面展示丰富的边到边影像或者多媒体内容并且你用一个梯度渐变来使这些图标依然可见。

当你自定义status和navigation bars时，使他们一起保持透明或者只修改status bar,navigation 
 bar在其他所有情况下都应该保持黑色。

###个人主题视图

在XML布局中定义元素可以指定`android:theme`参数，也就是一个指向主题资源的参数。这个参数为元素和各个子元素修改主题，这在一个界面的特定部分需要变更主题调色板时很有用。

##创建Lists和Cards
想要在你的app中应用mterial design风格创建lists和cards,你可以使用RecyclerView和CardView组件。

###创建Lists

RecyclerView组件是ListView的一个更高级更灵活的版本，这个组件是一个大数据集的容器，它在维护有限的views时可以非常高效的滚动。当你需要展示拥有在运行时基于用户操作和网络活动的元素的数据集时，你应该使用RecyclerView。

RecyclerView通过提供以下两点来简化展示和大数据处理

- 为安放条目（items）的layoutmanager
- 为常用的条目操作准备好的默认动画，比如移除条目或是添加条目

在使用RecyclerView组件式时，你同样拥有自定义layout managers和动画的灵活性

<figure>
	<img src="/images/RecyclerView.png">
	<figcaption>图1.RecyclerView</figcaption>
</figure>

使用RecyclerView你必须为其指定一个adapter和layout manager,继承RecyclerView.adapter这个类来创建adapter。实现的细节取决于你的数据集（dataset）和视图（views）种类的细节。想要了解更多，请看下面的例子

![Smithsonian Image]({{site.url}}/images/list_mail.png)
{: .image-pull-right}

layout manager在RecycleView中定位item views，并且决定什么时候重用不可见的item views.重用一个view的时候，layout manager会要求adapter从数据集中取出另一个元素来替换被替换的view的内容。用这种方式来重用views避免了创建不必要的views和代价高昂的findviewbyid查找，提升了展示效果。

RecyclerView提供以下内置的layout managers

- 以垂直或水平方式展示滚动条目的LinearLayoutManager
- 以网格(grid)方式展示条目的GridLayoutManager
- 以交错网格（staggered grid）展示条目的StaggeredGridLayoutManager

如果要创建自定义layout manager,需要继承RecyclerView.LayoutManager类

###动画

在RecyclerView中添加和删除条目动画师默认支持的。想要自定义这些动画的话，需要继承RecyclerView.ItemAnimator类以及使用RecyclerView.setItemAnimator() 方法。

###例子

下面的代码描述如何在布局中添加RecyclerView

{% highlight xml %}
	<!-- A RecyclerView with some commonly used attributes -->
	<android.support.v7.widget.RecyclerView
    	android:id="@+id/my_recycler_view"
    	android:scrollbars="vertical"
    	android:layout_width="match_parent"
    	android:layout_height="match_parent"/>

{% endhighlight %}

你在布局上添加了RecyclerView后,你需要获得对象的处理，将它连接layout manager,并且为展示的数据集附上adapter

{% highlight java %}
	public class MyActivity extends Activity {
    private RecyclerView mRecyclerView;
    private RecyclerView.Adapter mAdapter;
    private RecyclerView.LayoutManager mLayoutManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.my_activity);
        mRecyclerView = (RecyclerView) findViewById(R.id.my_recycler_view);

        // use this setting to improve performance if you know that changes
        // in content do not change the layout size of the RecyclerView
        mRecyclerView.setHasFixedSize(true);

        // use a linear layout manager
        mLayoutManager = new LinearLayoutManager(this);
        mRecyclerView.setLayoutManager(mLayoutManager);

        // specify an adapter (see also next example)
        mAdapter = new MyAdapter(myDataset);
        mRecyclerView.setAdapter(mAdapter);
    }
    ...
}
{% endhighlight %}

adapter提供你的数据集中的条目的存取，为条目创建view,当原来的item不可见时用新的items数据取代之前views的内容。下面的代码展示了一个包括用TextView展现一个String数组的数据集的简单实现：

{% highlight java %}
	public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {
    private String[] mDataset;

    // Provide a reference to the views for each data item
    // Complex data items may need more than one view per item, and
    // you provide access to all the views for a data item in a view holder
    public static class ViewHolder extends RecyclerView.ViewHolder {
        // each data item is just a string in this case
        public TextView mTextView;
        public ViewHolder(TextView v) {
            super(v);
            mTextView = v;
        }
    }

    // Provide a suitable constructor (depends on the kind of dataset)
    public MyAdapter(String[] myDataset) {
        mDataset = myDataset;
    }

    // Create new views (invoked by the layout manager)
    @Override
    public MyAdapter.ViewHolder onCreateViewHolder(ViewGroup parent,
                                                   int viewType) {
        // create a new view
        View v = LayoutInflater.from(parent.getContext())
                               .inflate(R.layout.my_text_view, parent, false);
        // set the view's size, margins, paddings and layout parameters
        ...
        ViewHolder vh = new ViewHolder(v);
        return vh;
    }

    // Replace the contents of a view (invoked by the layout manager)
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        // - get element from your dataset at this position
        // - replace the contents of the view with that element
        holder.mTextView.setText(mDataset[position]);

    }

    // Return the size of your dataset (invoked by the layout manager)
    @Override
    public int getItemCount() {
        return mDataset.length;
    }
}

{% endhighlight %}

![Smithsonian Image]({{site.url}}/images/card_travel.png)
{: .image-pull-right}

###创建Cards
CardView继承自FrameLayout类，它让你通过这个平台用卡片的形式展现一致的外观。CardView可以有阴影和圆润的边角。

创建一个带阴影的card，用card_view:cardElevation参数。CardView在Android5.0(API21)及以上版本用真的三面图值（elevation）和动态的阴影，在早期版本使用过去的静态实现。想了解更多，看Maintaining Compatibility内容。

用以下的这些属性来自定义CardView组件的外观

- 用card_view:cardCornerRadius属性设置边角半径
- 在代码中设置边角半径的话，用CardView.setRadius方法
- 用card_view:cardBackgroundColor属性来设置card的背景颜色

下面的代码向你展示怎样在布局中使用CardView

{% highlight xml %}
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	xmlns:tools="http://schemas.android.com/tools"
    	xmlns:card_view="http://schemas.android.com/apk/res-auto"
    	... >
    	<!-- A CardView that contains a TextView -->
    	<android.support.v7.widget.CardView
        	xmlns:card_view="http://schemas.android.com/apk/res-auto"
        	android:id="@+id/card_view"
        	android:layout_gravity="center"
        	android:layout_width="200dp"
        	android:layout_height="200dp"
        	card_view:cardCornerRadius="4dp">

        	<TextView
            	android:id="@+id/info_text"
            	android:layout_width="match_parent"
            	android:layout_height="match_parent" />
    	</android.support.v7.widget.CardView>
	</LinearLayout>
{% endhighlight %}

了解更多信息，参考CardView的API

###增加依赖（add dependencies）
RecyclerView和CardView是v7 Support Libraries的一部分，想使用这些控件，需要在你app的module中添加这些Gradle dependencies 

{% highlight java %}
	dependencies {
    	...
    	compile 'com.android.support:cardview-v7:21.0.+'
    	compile 'com.android.support:recyclerview-v7:21.0.+'
	}
{% endhighlight %}

##自定义阴影（shadows)和裁剪视图（clipping views）

Material Design为UI元素引进了海拔属性（elevation）,Elevation使用户们认识到每一个元素之间的相对数据的重要性，且将注意力集中在即将发生的任务上。

一个视图（view）的海拔值用Z来表示，它决定了视图可视的阴影形状。也就是说Z大的view挡住了Z小的view，但是呢，view的实际大小并没有改变。

阴影通过设置了海拔高度的view的父view来绘制，因此，标准的视图裁剪主题，也是默认通过父view来裁剪。

海拔在创建一些动画时也很有用，比如响应某些行为时组件在视图平面上暂时的升起。

想要了解更多关于material design中elevation的相关信息，关注Objects in 3D space内容。

###为你的views指定elevation
view的Z值有两个组成部分

- Elevation(海拔):静止的部分
- Transition（平移）:用于动画的动态部分

Z = elevation + translationZ

<figure>
	<img src="/images/shadows-depth.png">
	<figcaption>Shadows for different view elevations.</figcaption>
</figure>

在布局定义中用android:elevation参数来定义view的elevation值，在activity的代码中用 View.setElevation() 方法来设置。

设置translation值得话，用 View.setTranslationZ()方法来设置。

新方法 ViewPropertyAnimator.z()和ViewPropertyAnimator.translationZ()可以让你轻易的设置views的elevation值，想了解更多，看 ViewPropertyAnimator的API和Property Animation 的官方开发者指南。

你也可以使用StateListAnimator，一种声明的方式来指定这些动画。这种方法在处理状态改变产生动画时特别有用，比如用户按下一个button.想了解更多信息，请看Animate View State Changes。

###自定义视图阴影和轮廓（Outlines）

视图背景图片的边界决定了视图阴影的默认形状。轮廓（Outlines）表示一个图形对象的外部形状，定义了触摸反馈的波纹面积。

看下面的视图，就是带背景图片的：

{% highlight xml %}
	<TextView
    	android:id="@+id/myview"
    	...
    	android:elevation="2dp"
    	android:background="@drawable/myrect" />
{% endhighlight %}

背景图片定义为带了圆角的矩形

{% highlight xml %}
	<!-- res/drawable/myrect.xml -->
	<shape xmlns:android="http://schemas.android.com/apk/res/android"
       		android:shape="rectangle">
    	<solid android:color="#42000000" />
    	<corners android:radius="5dp" />
	</shape>
{% endhighlight %}

当背景图片（drawable）定义了view的轮廓时，view会投射出带圆角的阴影。提供一个自定义轮廓重写view的默认阴影形状。

在你的代码中为你的view自定义轮廓

- 继承ViewOutlineProvider类
- 重写getOutline()方法
- 用View.setOutlineProvider()方法给你的view指定新的outline provider

你使用Outline类中方法可以创建带圆角的椭圆形或矩形轮廓。view默认的outline provider从view的背景中得到轮廓。要防止view投射阴影的话，设置它的outline provider为空就好了。

###裁剪Views

裁剪views让你可以轻松的改变view的形状。你可以为了使视图和其他设计元素风格搭配来裁剪view或者为了响应用户输入改变形状。你可以使用View.setClipToOutline()方法或者android:clipToOutline参数view的轮廓区域。只有矩形，圆形和圆角矩形支持轮廓裁剪，并且裁剪的权限由Outline.canClip()方法决定。

裁剪图片背景视图的话，设置该图片作为这个view的背景并且调用View.setClipToOutline()方法。

裁剪视图是代价昂贵的操作，所以不要把你要裁剪视图的形状设置为动画。如果想达到这种效果，使用Reveal Effect动画

##利用Drawable开发

下面的为drawables配备的功能能帮助你在你的app中实现material design:

- 图片着色
- 出色的色彩抽取
- 矢量图

本课程向你展示如何在你app中使用这些特性

Tint Drawable Resources
###给图片资源着色

在Android5.0（API21）以上，你可以将bitmap图和定义为alpha masks的点9图。你可以用颜色资源或者指向颜色资源的主题参数来给它们着色（比如?android:attr/colorPrimary）通常情况下，你只会一次性创建这些资源，将它们自动涂成适合你的主题的颜色

你可以使用setTint()方法申请将BitmapDrawable或是NinePatchDrawable对象上色。你也可以在布局文件中用android:tint和android:tintMode参数来设置颜色。

###从图片中提取出色的颜色

Android支持类库r21及以上包含了调色板类，也就是可以让你从图片中提取出色的颜色。这个类提取以下的出色颜色：

- Vibrant
- Vibrant dark
- Vibrant light
- Muted
- Muted dark
- Muted light

想要提取这些颜色，传递一个Bitmap对象到你加载图片的背景线程中的Palette.generate()静态方法。如果你用不了这个线程，调用Palette.generateAsync()方法且提供一个listener来代替。

你可以在Palette类中使用getter方法从图片中取回突出色，比如Palette.getVibrantColor。

在你的项目中使用Palette类，需要添加以下的Gradle dependency到你的app module中

	dependencies {
    	...
    	compile 'com.android.support:palette-v7:21.0.0'
	}

了解更多，看Palette类的API手册

###创建矢量图

在Android5.0（API21)及以上，你可以定义矢量图，也就是在缩放时不失真的图。你为一张矢量图，只需要准备一个asset文件，与一张bitmap图为了适配各种屏幕的情况相反。创建一个矢量图，你需要在<vector>XML文件中定义图形的细节。

下面的示例用heart的shape定义了一个矢量图：
{% highlight xml %}
	<!-- res/drawable/heart.xml -->
	<vector xmlns:android="http://schemas.android.com/apk/res/android"
    	<!-- intrinsic size of the drawable -->
    	android:height="256dp"
    	android:width="256dp"
    	<!-- size of the virtual canvas -->
    	android:viewportWidth="32"
    	android:viewportHeight="32">

  	<!-- draw a path -->
  	<path android:fillColor="#8fff"
      	android:pathData="M20.5,9.5
                        c-1.955,0,-3.83,1.268,-4.5,3
                        c-0.67,-1.732,-2.547,-3,-4.5,-3
                        C8.957,9.5,7,11.432,7,14
                        c0,3.53,3.793,6.257,9,11.5
                        c5.207,-5.242,9,-7.97,9,-11.5
                        C25,11.432,23.043,9.5,20.5,9.5z" />
	</vector>
{% endhighlight %}

矢量图形在Android中代表着VectorDrawable对象。想了解更多关于pathDate语法的信息，请看SVG Path参考手册。想了解更多关于绘制矢量图形属性的信息，请看Animating Vector Drawable。


##自定义动画

当用户与你的app交互时，material design中的动画对用户的行为进行反馈并提供视觉效果上的连续性。material主题为buttin和activity切换提供了一些默认的动画，Android5.0（API21）及以上让你可以自定义这些动画和创建新以下新的内容：

- 触摸反馈（Touch feedback）
- 圆环显示（Circular Reveal）
- Activity切换（Activity transitions）
- 弯曲移动（Curved motion）
- 视图状态转变（View state changes）

###自定义触摸反馈

material design中的触摸反馈机制使得用户在与UI元素进行交互镇妖联系时能提供即时的视觉证明。buttons默认的触摸反馈动画使用了新的RippleDrawable类，它在不同的状态之间转换呈现涟漪状的效果。

在大多数情况下，你在你的视图XML中通过下面的方式指定视图背景来应用这个功能：

- '?android:attr/selectableItemBackground' for a bounded ripple（有界限的涟漪）
- '?android:attr/selectableItemBackgroundBorderless' for a ripple that extends beyond the view（延伸的超出视图的涟漪）

>Note: selectableItemBackgroundBorderless是API21介绍的新参数

另一种选择是，你可以使用ripple元素将一个RippleDrawable定义成一个XML资源

你可以给RippleDrawable对象指定一个颜色。如果要改变默认的触摸反馈颜色的话，使用主题的android:colorControlHighlight参数。

了解更多信息，请看RippleDrawable类的API参考手册。

###使用显示效果

当展示或隐藏一组UI元素时，显示动画给用户提供了视觉上的连续性。ViewAnimationUtils.createCircularReveal()方法使你能够绘制一个裁剪的圆环效果来显示或隐藏一个view.

显示一个预先不可见的view使用这个效果：

{% highlight java %}

	// previously invisible view
	View myView = findViewById(R.id.my_view);

	// get the center for the clipping circle
	int cx = (myView.getLeft() + myView.getRight()) / 2;
	int cy = (myView.getTop() + myView.getBottom()) / 2;

	// get the final radius for the clipping circle
	finalRadius = Math.max(myView.getWidth(), myView.getHeight());

	// create the animator for this view (the start radius is zero)
	Animator anim =
    	ViewAnimationUtils.createCircularReveal(myView, cx, cy, 0, finalRadius);

	// make the view visible and start the animation
	myView.setVisibility(View.VISIBLE);
	anim.start();
{% endhighlight %}

用这个效果隐藏一个预先可见的view

{% highlight java %}

	// previously visible view
	final View myView = findViewById(R.id.my_view);

	// get the center for the clipping circle
	int cx = (myView.getLeft() + myView.getRight()) / 2;
	int cy = (myView.getTop() + myView.getBottom()) / 2;

	// get the initial radius for the clipping circle
	int initialRadius = myView.getWidth();

	// create the animation (the final radius is zero)
	Animator anim =
    	ViewAnimationUtils.createCircularReveal(myView, cx, cy, initialRadius, 0);

	// make the view invisible when the animation is done
	anim.addListener(new AnimatorListenerAdapter() {
    	@Override
    	public void onAnimationEnd(Animator animation) {
        	super.onAnimationEnd(animation);
        	myView.setVisibility(View.INVISIBLE);
    	}
	});

	// start the animation
	anim.start();
{% endhighlight %}

###自定义Activity切换

![ContactsAnim]({{site.url}}/images/ContactsAnim.jpg)
{: .image-pull-right}

在material design的app中的Activity切换提供了在通过移动的不同状态和一般元素的转换之间的视觉连接。你可以为进入或退出转换和activities中的共享元素指定自定义动画。

- 进入转换决定了activity在进入场景时views如何展现。举个例子，在爆炸式进入转换中，views从外部进入场景并且飞入屏幕的中央。
- 退出转换决定了activity在退出场景时views如何展现。举个例子，在爆炸式退出转换中，views从中央退出场景。
- 分享元素转换决定了在这些activities中这两个activities切换的分享view如何展示。举个例子，如果两个activities有同样的一张图片但是位置和尺寸不同，changeImageTransform分享元素切换会在这些activities之间顺畅的平移并缩放这张图片。

Android5.0（API21）支持这些进入和退出转换：

- 爆炸式-views从场景的中央移进移出
- 滑动式-views从一个场景的边缘移进移出
- 渐变式-从场景中通过改变透明度来添加或移出view

任何继承了Visibility类的转换都支持成为进入或退出转换。了解更多信息，请看Transition类的API参考.

Android5.0（API21）还支持这些分享元素转换：

- changeBounds - Animates the changes in layout bounds of target views.
- changeClipBounds - Animates the changes in clip bounds of target views.
- changeTransform - Animates the changes in scale and rotation of target views.
- changeImageTransform - Animates changes in size and scale of target images.

当你在你的app中激活了activiy转换时，进入和退出的activities默认的cross-fading转换就被激活了。

<figure>
	<img src="/images/SceneTransition.png">
	<figcaption>A scene transition with one shared element.</figcaption>
</figure>

####指定自定义转换（transitions）

首先用android:windowContentTransitions参数激活窗体内容转换（transitions）当你定义一个继承了material主题的style.你一样也可以在你的style定义中指定进入（enter）,退出（exit），分享元素（shared element）转换：

{% highlight xml %}

	<style name="BaseAppTheme" parent="android:Theme.Material">
  	<!-- enable window content transitions -->
  	<item name="android:windowContentTransitions">true</item>

  	<!-- specify enter and exit transitions -->
  	<item name="android:windowEnterTransition">@transition/explode</item>
  	<item name="android:windowExitTransition">@transition/explode</item>

  	<!-- specify shared element transitions -->
  	<item name="android:windowSharedElementEnterTransition">
    	@transition/change_image_transform</item>
  	<item name="android:windowSharedElementExitTransition">
    	@transition/change_image_transform</item>
	</style>
{% endhighlight %}

在这个例子中的'change_image_transform'应该如下定义：
{% highlight xml %}

	<!-- res/transition/change_image_transform.xml -->
	<!-- (see also Shared Transitions below) -->
	<transitionSet xmlns:android="http://schemas.android.com/apk/res/android">
  		<changeImageTransform/>
	</transitionSet>
{% endhighlight %}

changeImageTransform元素相当于ChangeImageTransform类。了解更多，请看Transition的API参考。
相同效果的也可以在代码中激活窗体内容转换（window content transitions），调用Window.requestFeature()方法：

{% highlight xml %}

	// inside your activity (if you did not enable transitions in your theme)
	getWindow().requestFeature(Window.FEATURE_CONTENT_TRANSITIONS);

	// set an exit transition
	getWindow().setExitTransition(new Explode());
{% endhighlight %}

在你的代码中指定转换，用一个Transition对象调用这些方法：

- Window.setEnterTransition()
- Window.setExitTransition()
- Window.setSharedElementEnterTransition()
- Window.setSharedElementExitTransition()

setExitTransition（）和setSharedElementExitTransition（）方法定义了正在调用activity的退出转换（exit transition）。setEnterTransition()和setSharedElementEnterTransition()则定义了被调用activity的进入动画。

想要实现转换的全部效果，你必须同时激活正在调用和被调用的activities的窗体内容转换（window content transitions）。否则的话，正在调用的activity会开始退出转换，但是然后你会看到一个窗体转换（像是scale或fade）。

想要尽快开始一个进入转换（enter transition）,在被调用的activity上使用Window.setAllowEnterTransitionOverlap()方法，这会让你有更多的生动的进入转换。

####使用转换（transitions）开启一个activity

如果你给一个activity设置了一个退出转换并激活，这个转换会在你像下面这样启动另一个activity时启动：

{% highlight java %}

	startActivity(intent,
              ActivityOptions.makeSceneTransitionAnimation(this).toBundle());
{% endhighlight %}

如果你给第二个activity设置了进入转换（enter transition）,当这个activity开启时转换也会被激活。如果想在start另一个activity时禁掉转换的话，提供一个空options bundle

####开启一个带分享元素的activty

想要在两个activity之间制造一个带分享元素的屏幕转换：

- 在主题中激活window content 转换
- 在你的style中指定一个分享元素
- 把你的转换定义成一个XML资源
- 用android:transitionName参数为分享元素（两个布局中）起一个名称
- 使用ActivityOptions.makeSceneTransitionAnimation()方法

{% highlight java %}

	// get the element that receives the click event
	final View imgContainerView = findViewById(R.id.img_container);

	// get the common element for the transition in this activity
	final View androidRobotView = findViewById(R.id.image_small);

	// define a click listener
	imgContainerView.setOnClickListener(new View.OnClickListener() {
    	@Override
    	public void onClick(View view) {
        	Intent intent = new Intent(this, Activity2.class);
        	// create the transition animation - the images in the layouts
        	// of both activities are defined with android:transitionName="robot"
        	ActivityOptions options = ActivityOptions
            	.makeSceneTransitionAnimation(this, androidRobotView, "robot");
        	// start the new activity
        	startActivity(intent, options.toBundle());
    	}
	});
{% endhighlight %}

想要在你的中产生动态的分享视图，使用View.setTransitionName()方法在两个activities中指定一个常用元素名称。

当你结束第二个activity时想要回退转换动画场景，调用Activity.finishAfterTransition()方法代替使用Activity.finish()。

####开启一个带多个分享元素的activity

想要在带多个分享元素的两个activity之间制造一个场景转换动画，用android:transitionName参数定义两个布局中的分享元素。（或者在两个activity中使用View.setTransitionName()方法），且像下面这样创建一个ActivityOptions对象：

{% highlight java %}

	ObjectAnimator mAnimator;
	mAnimator = ObjectAnimator.ofFloat(view, View.X, View.Y, path);
	...
	mAnimator.start();
{% endhighlight %}

###激活视图状态转换

StateListAnimator类让你定义在view状态改变时运行的animator。下面的例子向你展示如何定义一个
StateListAnimator作为一个XML资源：

{% highlight xml %}

	<!-- animate the translationZ property of a view when pressed -->
	<selector xmlns:android="http://schemas.android.com/apk/res/android">
  		<item android:state_pressed="true">
    		<set>
      			<objectAnimator android:propertyName="translationZ"
        		android:duration="@android:integer/config_shortAnimTime"
        		android:valueTo="2dp"
        		android:valueType="floatType"/>
        		<!-- you could have other objectAnimator elements
             	here for "x" and "y", or other properties -->
    		</set>
  		</item>
  	<item android:state_enabled="true"
    	android:state_pressed="false"
    	android:state_focused="true">
    		<set>
      		<objectAnimator android:propertyName="translationZ"
        	android:duration="100"
        	android:valueTo="0"
        	android:valueType="floatType"/>
    		</set>
  	</item>
	</selector>
{% endhighlight %}

给view附上自定义视图状态动画，在这个例子中在XML资源文件中使用selector元素定义一个animator,并且使用android:stateListAnimator参数分配到你的view中。如果在你的代码中指定state list分配到你的view中的话，用AnimationInflater.loadStateListAnimator()方法，并且用View.setStateListAnimator()方法分配animator到你的view。

当你的主题是继承material主题的话，buttons默认会有一个Z animation。想要在你的buttons避免如此的话，设置android:stateListAnimator参数为@null。

AnimatedStateListDrawable类让你在有关联view的状态改变之间创建展示动画的图片。Android5.0中的一些系统组件默认的使用了这些动画。下面的例子展示怎样定义一个AnimatedStateListDrawable为一个XML资源：

{% highlight xml %}

	<!-- res/drawable/myanimstatedrawable.xml -->
	<animated-selector
    	xmlns:android="http://schemas.android.com/apk/res/android">

    	<!-- provide a different drawable for each state-->
    	<item android:id="@+id/pressed" android:drawable="@drawable/drawableP"
        	android:state_pressed="true"/>
    	<item android:id="@+id/focused" android:drawable="@drawable/drawableF"
        	android:state_focused="true"/>
    	<item android:id="@id/default"
        	android:drawable="@drawable/drawableD"/>

    	<!-- specify a transition -->
    	<transition android:fromId="@+id/default" android:toId="@+id/pressed">
        	<animation-list>
            	<item android:duration="15" android:drawable="@drawable/dt1"/>
            	<item android:duration="15" android:drawable="@drawable/dt2"/>
            	...
        	</animation-list>
    	</transition>
    	...
	</animated-selector>
{% endhighlight %}

###绘制矢量图

矢量图可以不失真的伸缩。AnimatedVectorDrawable类让你绘制一个矢量图的内容。

一般在三个XML文件中绘制矢量图：

- 在res/drawable/中的带<vector>元素的矢量图
- 在res/drawable/中的带<animated-vector>元素的矢量图
- 在res/anim/中的的带<objectAnimator>的一个或多个object animators

绘制矢量图可以绘制<group>和<path>元素的参数。<group>元素定义了一系列paths或subpaths,<path>元素定义了将要绘制的路径（paths to be drawn）

当你定义一个你想要绘制的矢量图时，使用android:name参数来给groups和paths指定一个唯一的名字，所以你能从你的animator定义中指向它们。举例如下：

{% highlight xml %}

	<!-- res/drawable/vectordrawable.xml -->
	<vector xmlns:android="http://schemas.android.com/apk/res/android"
    	android:height="64dp"
    	android:width="64dp"
    	android:viewportHeight="600"
    	android:viewportWidth="600">
    	<group
        	android:name="rotationGroup"
        	android:pivotX="300.0"
        	android:pivotY="300.0"
        	android:rotation="45.0" >
        	<path
            	android:name="v"
            	android:fillColor="#000000"
            	android:pathData="M300,70 l 0,-70 70,70 0,0 -70,70z" />
    	</group>
	</vector>
{% endhighlight %}

在矢量图中的绘制矢量图定义通过它们的名字指向groups和paths:

{% highlight xml %}

	<!-- res/drawable/animvectordrawable.xml -->
	<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
  	android:drawable="@drawable/vectordrawable" >
    	<target
        	android:name="rotationGroup"
        	android:animation="@anim/rotation" />
    	<target
        	android:name="v"
        	android:animation="@anim/path_morph" />
	</animated-vector>
{% endhighlight %}

未完待续
动画绘制定义表示ObjectAnimator和AnimatorSet对象。在这个例子中的第一个动画让目标group旋转360度。

{% highlight xml %}

	<!-- res/anim/rotation.xml -->
	<objectAnimator
    	android:duration="6000"
    	android:propertyName="rotation"
    	android:valueFrom="0"
    	android:valueTo="360" />
{% endhighlight %}

第二个动画改变矢量图的坐标从一个形状到另一个形状。两个坐标必须都能兼容变形：它们必须有同样数量的命令并且每个命令必须有同样数量的参数。

{% highlight xml %}

	<!-- res/anim/path_morph.xml -->
	<set xmlns:android="http://schemas.android.com/apk/res/android">
    	<objectAnimator
        	android:duration="3000"
        	android:propertyName="pathData"
        	android:valueFrom="M300,70 l 0,-70 70,70 0,0   -70,70z"
        	android:valueTo="M300,70 l 0,-70 70,0  0,140 -70,0 z"
        	android:valueType="pathType" />
	</set>
{% endhighlight %}

了解更多，请看AnimatedVectorDrawable的API参考手册。

##兼容性维护

一些material design特征像是material主题和自定义activity切换只在Android5.0及以上才可用。但是，你可以设计你的运行在支持material design的的设备上的app来利用这些特征并且在运行之前发行的Android版本的设备上依然兼容。

###自定义可选styles

你可以配置你的app在支持material主题的设备上使用material主题，并且在在运行更早Android版本的设备上转换成老版的主题：

  1、在res/values/styles.xml文件中定义一个继承自老版主题（如Holo）的主题。
  2、在res/values-v21/styles.xml文件中定义一个继承material主题的同样名字的主题。
  3、在manifest文件中设置这个主题作为你app的主题。

>Note: 如果你的app使用了material主题但是没有用这种方式提供一个可选的主题，你的app将不会在运行Android5.0之前的android版本的设备上运行。

###提供可选布局

如果你根据material design向导来设计你的布局不用任何在Android 5.0(API21)中介绍的新XML参数的话，它们可以在之前的Android版本上奏效。否则的话，你可以提供可选的布局。你也可以提供可选的布局来自定义你的app在早先Android版本上的效果。

在res/layout-v21/中为Android5.0（API21）创建你的布局文件且在res/layout/中为早先Android版本创建你的可选布局文件。举个例子，res/layout/my_activity.xml是res/layout-v21/my_activity.xml的一个可选布局。

为了避免复制代码，在res/value中定义你的styles,为新的API在res/value-v21/中修改styles,并且使用style继承，在res/values/中定义基styles并且在res/values-v21中继承他们。

###使用支持类库

V7支持类库r21及以上包括下面的material design特征：

- 当你应用Theme.AppCompat themes中的一个主题时为一些系统控件准备的Material Design风格    
- Theme.AppCompat主题中的调色板（Color palette）主题参数
- 展示数据集的RecyclerView控件
- 创建卡片的CardView
- 从图片中提取突出色的Palette类

####系统控件

Theme.AppCompat主题为以下这些控件提供material design风格

- EditText
- Spinner
- CheckBox
- RadioButton
- SwitchCompat
- CheckedTextView

####调色板（Color Palette）

使用Android V7 Library获取material design风格和自定义调色板，应用Theme.AppCompat主题中的一个：

{% highlight xml %}

	<!-- extend one of the Theme.AppCompat themes -->
	<style name="Theme.MyTheme" parent="Theme.AppCompat.Light">
    	<!-- customize the color palette -->
    	<item name="colorPrimary">@color/material_blue_500</item>
    	<item name="colorPrimaryDark">@color/material_blue_700</item>
    	<item name="colorAccent">@color/material_green_A200</item>
	</style>
{% endhighlight %}

####Lists和Cards

通过带以下限制的Android V7 Support Library使得RecyclerView和CardView控件在早先的版本上可用：

- 使用额外的padding使得CardView回落到一个可编程的阴影实现
- CardView不会裁剪它的和圆角相交的子views

####依赖（Dependencies）

在android5.0(API21)之前版本上使用这些特征的话，在你的项目中包含Android v7 Support library的Gradle依赖：

	dependencies {
    	compile 'com.android.support:appcompat-v7:21.0.+'
    	compile 'com.android.support:cardview-v7:21.0.+'
    	compile 'com.android.support:recyclerview-v7:21.0.+'
	}


###检查系统版本

下面的特征只在Android5.0（API21）以上可用：

- 界面切换（Activity transitions）
- 触摸反馈（Touch feedback）
- 显示动画（Reveal animations）
- 基于路径的动画（Path-based animations）
- 矢量图（Vector drawables）
- 图片着色（Drawable tinting）

为了维护早先android版本的兼容性，在你调用一下的API之前在运行时检测系统版本：

{% highlight xml %}

	// Check if we're running on Android 5.0 or higher
	if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
    	// Call some material design APIs here
	} else {
    	// Implement this feature without material design
	}
{% endhighlight %}

>Note:为了确定你的app支持哪一个Android版本，在你的清单文件中使用android:minSdkVersion和android:targetSdkVersion参数。在Android5.0中使用material design特征，设置android:targetSdkVersion为21。了解更多，请看<use-sdk>API向导。













