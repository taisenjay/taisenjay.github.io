---
layout: post
title: "Fragment基本用法及实现Tab"
excerpt: "android基础知识点（4）"
tags: [service,lifecycle]
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

Fragment是在Android3.0时推出的，主要是为了利用平板电脑的大屏幕。fragment非常类似于Activity,可以像Activity一样包含布局。Fragment通常是嵌套在Activity中使用的。可以想象如果应用中用了太多的Activity，将不利于项目的维护，而使用fragment却非常灵活。到如今，fragment的应用已经非常广泛了，现在主要用于实现Tab分页。对fragment的基本介绍就到这里了。下面就创建一个fragment动手练习一下。

#fragment的简单使用

在一个Activity中添加两个fragment平分Activity空间，首先新建一个左侧布局left_fragment.xml,代码如下：

{% highlight xml %}

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	android:layout_width="match_parent"
    	android:layout_height="match_parent"
    	android:orientation="vertical" >
    
    	<Button 
        	android:id="@+id/button"
        	android:layout_width="wrap_content"
        	android:layout_height="wrap_content"
        	android:layout_gravity="center_horizontal"
        	android:text="Button"
        	/>

	</LinearLayout>
{% endhighlight %}

通过代码可以看到left_fragment布局上只有一个Button，它的text就是“button”。接下来再创建一个fragment布局，名称为right_fragment.xml,布局依然不需要复杂控件，只添加一个TextView就好（TextView的文本当然随便取了，我们就将text设为“this is right fragment”吧），再将LinearLayout设置一个背景颜色，即`android:background="#00ff00"`（绿色），（这样看起来区分明显一点）。

接下来进行下一步，创建Fragment代码，正式关联fragment的布局吧，下面是关联left_fragment.xml的LeftFragment类的代码：

{% highlight java %}

	public class LeftFragment extends Fragment {
	
		@Override
		public View onCreateView(LayoutInflater inflater, ViewGroup container,
				Bundle savedInstanceState) {
			View view = inflater.inflate(R.layout.left_fragment, container, false);
			return view;
		}
	}
{% endhighlight %}

可以看到我们的LeftFragment继承了Fragment类，重写了OnCreate（）方法（一看就知道是和Activity的OnCreate（）一个性质的），可以看到OnCreateView（）方法传入了一个LayoutInflater参数，通过调用它的inflater（）方法，我们将R.layout.left_fragment填充到view视图中，并返回这个view。按照同样的方法我们再继续创建RightFragment关联right_fragment.xml布局。

接下来我们再修改activity_main.xml布局:

{% highlight xml %}

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:layout_width="match_parent"
		android:layout_height="match_parent" >
		<fragment
			android:id="@+id/left_fragment"
			android:name="com.example.fragmenttest.LeftFragment"
			android:layout_width="0dp"
			android:layout_height="match_parent"
			android:layout_weight="1" />
		<fragment
			android:id="@+id/right_fragment"
			android:name="com.example.fragmenttest.RightFragment"
			android:layout_width="0dp"
			android:layout_height="match_parent"
			android:layout_weight="1" />
	</LinearLayout>
{% endhighlight %}

可以看到我们在activity_main.xml中通过<fragment>标签添加了我们的LeftFragment和RightFragment,观察一下就会发现，我们是同过指定android：name这个属性来指定到具体的Fragment类的，这里就是LeftFragment和RightFragment的路径名加类名（具有唯一性）。运行一下，效果如图：

<figure>
	<img src="/images/fragment-1.png">
</figure>

#动态添加fragment

上一节我们使用fragment,只是完成了静态添加，这是最简单最基础的（并没有实用价值），在实际开发中，还是动态添加fragment为主，下面就来尝试一下动态添加fragment.新建another_right_fragment.xml，代码如下：

{% highlight xml %}

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	android:layout_width="match_parent"
    	android:layout_height="match_parent"
    	android:background="#ffff00"
    	android:orientation="vertical" >
    
		<TextView 
	    	android:layout_width="wrap_content"
	    	android:layout_height="wrap_content"
	    	android:layout_gravity="center_horizontal"
	    	android:textSize="20sp"
	    	android:text="This is another right fragment"
	    	/>
    </LinearLayout>
{% endhighlight %}

可以看到我们依然构建了一个超简单的fragment布局，背景设置为黄色，文本设置为"This is another right fragment"，与上文一样，创建一个AnotherRightFragmentl类关联此布局。接下来我们就要动态的将该fragment动态添加到我们的MainActivity中。先编辑我们的activity_main.xml,代码如下：

{% highlight xml %}

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:layout_width="match_parent"
		android:layout_height="match_parent">
		<fragment
			android:id="@+id/left_fragment"
			android:name="com.example.fragmenttest.LeftFragment"
			android:layout_width="0dp"
			android:layout_height="match_parent"
			android:layout_weight="1" />
		<FrameLayout
			android:id="@+id/right_layout"
			android:layout_width="0dp"
			android:layout_height="match_parent"
			android:layout_weight="1" >
			<fragment
				android:id="@+id/right_fragment"
				android:name="com.example.fragmenttest.RightFragment"
				android:layout_width="match_parent"
				android:layout_height="match_parent" />
		</FrameLayout>
	</LinearLayout>
{% endhighlight %}

可以看到我们添加了一个FrameLayout，并将right_fragment添加到了其中。之后我们将在代码中用AnotherRightFragment来替换RightFragment，实现动态添加fragment。代码如下：

{% highlight xml %}

	public class MainActivity extends Activity implements OnClickListener {

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
			Button button = (Button) findViewById(R.id.button);
			button.setOnClickListener(this);
		}

		@Override
		public void onClick(View v) {
			switch (v.getId()) {
			case R.id.button:
				AnotherRightFragment fragment = new AnotherRightFragment();
				FragmentManager fragmentManager = getFragmentManager();
				FragmentTransaction transaction = fragmentManager
						.beginTransaction();
				transaction.replace(R.id.right_layout, fragment);
				transaction.addToBackStack(null);
				transaction.commit();
				break;
			default:
				break;
				}
			}
		}
{% endhighlight %}

可以看到我们给button设置了点击监听，当点击Button时，我们就开始替换原来的fragment，实现动态添加fragment。我们可以看到一共是以下这六行代码实现了这个功能：

	AnotherRightFragment fragment = new AnotherRightFragment();
	FragmentManager fragmentManager = getFragmentManager();
	FragmentTransaction transaction = fragmentManager.beginTransaction();
	transaction.replace(R.id.right_layout, fragment);
	transaction.addToBackStack(null);
	transaction.commit();

我们来看一下

1、new了一个AnotherRightFragment类，也就是我们准备拿它替换原fragment的的fragment
2、通过调用getFragmentManager()得到了一个FragmentManager，顾名思义这是一个Fragment管理者类
3、通过调用FragmentManager的beginTransaction()方法拿到一个FragmentTransaction类，中文“事务”的意思，说明我们进行添加fragment的动作是被封装在FragmentTransaction这个叫事务的类中
4、通过事务的replace（）方法进行替换，可以看到有两个参数，第一个是我们FrameLayout(也就是fragment的父布局)的id，第二个就是我们的AnotherRightFragment了
5、调用事务的addToBackStack()方法，添加到返回栈的意思，意思就是说，它本来是不在返回栈的（当我们按退出键时就会直接退出Activity），而调用了它后，我们再按退出键时，则会将该fragment退出，而不会退出它的宿主Activity
6、最后但同样重要的是，事务必须得提交后，才能生效，调用commit（）。

重新运行下程序，效果和此前一样，再点击一下按钮，我们的fragment就进来了，效果如图：

<fragment>
	<img src="/images/fragment-2.png">
</fragment>

#fragment和Activity之间进行通信

虽然fragment是嵌入在Activity中显示的，但实际上它们依然是分离的。首先明显的是，Activity和Fragment是两个独立的类，它们之间并没有直接的通信方式。那要是我们在Activity中想要调用Fragment中的方法该如何实现呢，在fragment中调用Activity中的方法又如何实现呢？

FragmentManager提供了一个类似于findViewById的方法，专门用于从布局文件中获取fragment实例，代码如下：

	RightFragment rightFragment=（RightFragment）getFragmentManager（）.findFragmentById(R.id.right_fragment);

这下就可以在Activity中调用fragment中的方法了。那又如何在fragment中调用Activity中的方法呢？那就更简单了，代码如下：

	MainActivity mainActivity=（MainActivity）getActivity（）；

这下fragment就可以轻松使用Activity的方法了，此外，由于Activity是一个Context（上下文）对象，所以当fragment需要Context时，也可以通过getActivity()来实现。

#Fragment实现Tab

进行移动端的应用开发，必然会受屏幕小的限制，所以在实际的商业开发中，App都必须要充分的利用空间，我们代开任意的一个App。它下面或者是上面都会有几个标签进行分页，你点哪个标签，与那个标签匹配的内容就会显现。比如QQ，它在下面就有三个分页，分别是“消息”“联系人”“动态”。所以说现在的App实现分页是非常的普遍了，所以现在这也是必须要掌握的了。那这个Tab又是怎么实现的呢，我现在只接触到了ViewPager和Fragment（高端的以后再学吧），今天就练习一下用Fragment实现Tab。

我们创建一个FragmentDemo项目，首先编辑activity_main.xml布局，代码如下

{% highlight xml %}

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">
    </FrameLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:background="@drawable/tab_bg"
        android:orientation="horizontal">

        <LinearLayout
            android:id="@+id/ll_message"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:orientation="vertical"
            android:gravity="center_vertical">

            <ImageView
                android:id="@+id/iv_message"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@mipmap/message_unselected"
                android:layout_gravity="center_horizontal"/>
            <TextView
                android:id="@+id/tv_message"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="消息"
                android:textColor="#82858b"
                android:layout_gravity="center_horizontal"/>
        </LinearLayout>

        <LinearLayout
            android:id="@+id/ll_contacts"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:orientation="vertical"
            android:gravity="center_vertical">

            <ImageView
                android:id="@+id/iv_contacts"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@mipmap/contacts_unselected"
                android:layout_gravity="center_horizontal"/>
            <TextView
                android:id="@+id/tv_contacts"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="联系人"
                android:textColor="#82858b"
                android:layout_gravity="center_horizontal"/>

        </LinearLayout>

        <LinearLayout
            android:id="@+id/ll_news"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:orientation="vertical"
            android:gravity="center_vertical">

            <ImageView
                android:id="@+id/iv_news"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@mipmap/news_unselected"
                android:layout_gravity="center_horizontal"/>
            <TextView
                android:id="@+id/tv_news"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="动态"
                android:textColor="#82858b"
                android:layout_gravity="center_horizontal"/>

        </LinearLayout>

    </LinearLayout>
	</LinearLayout>
{% endhighlight %}

可以看到我们上面放了一个framelayout（很明显这是用来添加Fragment的），然后下面是一个水平的线性布局，并给它设置一个背景图片，就是我们下方三个tab按钮所在的线性布局了。然后是分别三个垂直的线性布局，分别放置了"消息""联系人""动态"的三张图片和三个显示文本的TextView。

接下来就来实现这三个fragment布局，首先新建fragment_message.xml布局，代码如下：

{% highlight xml %}

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	android:orientation="vertical" android:layout_width="match_parent"
    	android:layout_height="match_parent">
    
    	<TextView
        	android:layout_width="wrap_content"
        	android:layout_height="wrap_content"
        	android:text="这里是消息布局"/>
	</LinearLayout>
{% endhighlight %}

可以看到我们只放了一个TextView显示文本来标识这个是在MessageFragment布局中，其他的联系人fragment,动态fragment我们也依葫芦画瓢，只简单标识它就好。接下来就要新建MessageFragment类来关联fragment_message布局了（其他两个fragment也一样）。

新建MessageFragment类继承Fragment类，代码如下：

{% highlight java %}

	public class MessageFragment extends Fragment {  
  		 public View onCreateView(LayoutInflater inflater, ViewGroup container,  
            	Bundle savedInstanceState) {  
        	View view = inflater.inflate(R.layout.message_layout, container, 	false);  
        	return view;  
    	}  
 	}  
{% endhighlight %}

三个Fragment都构建好关联好布局后，接下来就进入最关键最重要的环节，将三个Fragment真正的应用到我们的MainActivity中，也就是主布局。MainActivity代码如下：

{% highlight java %}
	public class MainActivity extends Activity implements View.OnClickListener{

    private MessageFragment messageFragment;
    private ContactsFragment contactsFragment;
    private NewsFragment newsFragment;

    private LinearLayout messageLayout;
    private LinearLayout contactsLayout;
    private LinearLayout newsLayout;

    private ImageView ivMessage;
    private ImageView ivContacts;
    private ImageView ivNews;
    private TextView tvMessage;
    private TextView tvContacts;
    private TextView tvNews;

    private FragmentManager fragmentManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        setContentView(R.layout.activity_main);

        initViews();
        fragmentManager=getFragmentManager();
        setTabSelection(0);
    }

    private void initViews() {
        messageLayout= (LinearLayout) findViewById(R.id.ll_message);
        contactsLayout=(LinearLayout)findViewById(R.id.ll_contacts);
        newsLayout=(LinearLayout)findViewById(R.id.ll_news);

        ivMessage= (ImageView) findViewById(R.id.iv_message);
        ivContacts= (ImageView) findViewById(R.id.iv_contacts);
        ivNews= (ImageView) findViewById(R.id.iv_news);
        tvMessage= (TextView) findViewById(R.id.tv_message);
        tvContacts= (TextView) findViewById(R.id.tv_contacts);
        tvNews= (TextView) findViewById(R.id.tv_news);

        messageLayout.setOnClickListener(this);
        contactsLayout.setOnClickListener(this);
        newsLayout.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.ll_message:
                setTabSelection(0);
                break;
            case R.id.ll_contacts:
                setTabSelection(1);
                break;
            case R.id.ll_news:
                setTabSelection(2);
                break;
        }
    }

    private void setTabSelection(int index) {
        clearSelection();
        FragmentTransaction transaction = fragmentManager.beginTransaction();
        hideFragments(transaction);

        switch (index){
            case 0:
                ivMessage.setImageResource(R.mipmap.message_selected);
                tvMessage.setTextColor(Color.WHITE);
                if(messageFragment==null){
                    messageFragment=new MessageFragment();
                    transaction.add(R.id.fl_fragment_content,messageFragment);
                }else{
                    transaction.show(messageFragment);
                }
                break;
            case 1:
                ivContacts.setImageResource(R.mipmap.contacts_selected);
                tvContacts.setTextColor(Color.WHITE);
                if(contactsFragment==null){
                    contactsFragment=new ContactsFragment();
                    transaction.add(R.id.fl_fragment_content,contactsFragment);
                }else{
                    transaction.show(contactsFragment);
                }
                break;
            case 2:
                ivNews.setImageResource(R.mipmap.news_selected);
                tvNews.setTextColor(Color.WHITE);
                if(newsFragment==null){
                    newsFragment=new NewsFragment();
                    transaction.add(R.id.fl_fragment_content,newsFragment);
                }else{
                    transaction.show(newsFragment);
                }
                break;
            default:
                break;
        }
        transaction.commit();
    }

    private void clearSelection() {
        ivMessage.setImageResource(R.mipmap.message_unselected);
        tvMessage.setTextColor(Color.parseColor("#82858b"));
        ivContacts.setImageResource(R.mipmap.contacts_unselected);
        tvContacts.setTextColor(Color.parseColor("#82858b"));
        ivNews.setImageResource(R.mipmap.news_unselected);
        tvNews.setTextColor(Color.parseColor("#82858b"));
    }

    private void hideFragments(FragmentTransaction transaction) {
        if(messageFragment!=null){
            transaction.hide(messageFragment);
        }
        if(contactsFragment!=null){
            transaction.hide(contactsFragment);
        }
        if(newsFragment!=null){
            transaction.hide(newsFragment);
        }
    }
	}
{% endhighlight %}

接下来就来仔细分析一下代码。在onCreate()中,我们首先调用了initViews()初始化这三个线性布局三个ImageView和TextView，并给三个线性布局注册了点击监听，我们传入的参数是this（回到类的上边去看你就发现我们的MainActivity类实现了点击监听接口，所以此时我们可以直接将当前类this作为一个监听器类当做参数传入）。然后我们通过getFragmentManager()方法获得了FragmentManager，再接着我们调用了setTabSelection()方法并传入参数0（参数0代表是第一个Tab）。接下来看setTabSelection()内部代码，其实看我们的方法名就知道这个方法是用来设置我们选中的Tab标签的，我们按下哪个，它就选中哪个。在方法中，我们首先调用了clearSelection(),我们试想一下，再设置当前Tab的时候，我们改变当前Tab按钮的颜色图片标志着它是被选中的，那之前被选中的记录得要清除吧，得把它颜色图片改成没选中的状态吧。然后是此外同样重要的是我们的按钮是来管理上面显示的Fragment的，选中了这个标签，就得将之前选中显示的Fragment隐藏，然后显示当前选中的Fragment，也就是我们调用的hideFragments（）方法，最后我们就判断当前选中的Tab，并显示对应的Fragment。

演示效果如图

<figure class="half">
	<img src="/images/fragment-3.png">
	<img src="/images/fragment-4.png">
</figure>

参考：<font color="blue">郭霖书籍《第一行代码》及其博客</font>







