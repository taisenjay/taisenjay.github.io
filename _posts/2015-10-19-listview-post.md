---
layout: post
title: "LisView基本用法及实践"
excerpt: "android基础知识点（3）"
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

<font color="red">本文非原创，节选自《第一行代码》（郭霖著）</font>

ListView绝对可以称得上Android上最常用的控件，几乎所有的应用程序都会应用到它（重要程度可见一般）。由于手机屏幕空间有限，当我们需要呈现大量数据的时候，就可以借助listview实现。ListView允许用户通过手指上下滑动滚动屏幕，你每天都会用到这个控件，比如查阅联系人列表，翻看微博等。

但是比起一般的普通控件，ListView的用法又相对复杂了许多。

#ListView基本用法

首先新建一个ListViewTest项目，并让ADT自动帮我们创建好界面，然后修改activity_main.xml中代码，如下所示：

{% highlight xml %}

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	xmlns:tools="http://schemas.android.com/tools"
    	android:layout_width="match_parent"
    	android:layout_height="match_parent" tools:context=".MainActivity">

    	<ListView
        	android:id="@+id/list_view"
        	android:layout_width="match_parent"
        	android:layout_height="match_parent"></ListView>

	</LinearLayout>
{% endhighlight %}

在布局中加入ListView控件，为ListView指定id,再将宽度高度都设置为match_parent（ListVIew占满整个屏幕）。

接下来修改MainActivity.java中的代码，如下所示:

{% highlight java %}

	public class MainActivity extends Activity {

    	private String[] data={"Apple","Banana","Orange","Watermelon",
            	"Pear","Grape","Pineapple","Strawberry","Cherry","Mango"};

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.activity_main);
        	ArrayAdapter<String> adapter=new ArrayAdapter<String>
                (MainActivity.this,android.R.layout.simple_list_item_1,data);
        	ListView listView= (ListView) findViewById(R.id.list_view);
        	listView.setAdapter(adapter);
    	}
	}
{% endhighlight %}

既然listview是用于展示大量数据的，那我们就应该先将数据提供好。这些数据可以是网上下载的，也可以是从数据库读取的，视具体应用场景判断。这里我们使用了一个data数组来测试，里面包含了几种水果的名称。

但是，数组的数据无法直接传递给listview,需要借助适配器也就是adapter。Android中提供了多种Adapter,其中我认为最好用的是ArrayAdapter,因为它支持泛型，你可以自由指定它适配的数据类型。ArrayAdapter有多种构造函数提供重载，你可以视具体情况使用。由于我们这里展示的是String数组，因此将ArrayAdapter的泛型指定为String。然后在ArrayAdapter的构造函数中传入当前上下文、listview子项布局的id,以及要适配的数据。这里我们使用了android.R.layout.simple_list_item_1这个id,这是一个内置的listview布局，里面只有一个TextView（正好满足我们现在的使用情况）。最后，调用listview的setAdapter方法，将构建好的适配器传递到listview中，这样，listview和数据的关联就建立了。

<figure>
	<img src="/images/listview-1.png">
	<figcaption>图1</figcaption>
</figure>

#定制ListView界面

只能显示一段文本的listview实在太单调了，现在就让我们实现listview界面定制吧。首先准备好一组图片，与上文的水果数组相对应。

接下来，就让我们定义一个水果实体类，包括水果的名字（String name）和水果的图片（int imageId）。这个水果类就作为我们适配器的适配类型传入listview。Fruit（水果）类代码如下：

{% highlight java %}

	public class Fruit {
   		private String name;
    	private int imageId;

    	public Fruit(int imageId, String name) {
        	this.imageId = imageId;
        	this.name = name;
    	}

    	public int getImageId() {
        	return imageId;
    	}

    	public String getName() {
        	return name;
    	}
	}
{% endhighlight %}

然后需要为ListView的子项布局自定义一个布局（上文的内置布局就不够用了），在layout文件夹中新建fruit_item.xml布局文件：

{% highlight xml %}
	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	android:layout_width="match_parent"
    	android:layout_height="match_parent" >

    	<ImageView
        	android:id="@+id/fruit_image"
        	android:layout_width="wrap_content"
        	android:layout_height="wrap_content" />

    	<TextView
        	android:id="@+id/fruit_name"
        	android:layout_width="wrap_content"
        	android:layout_height="wrap_content"
        	android:layout_gravity="center"
        	android:layout_marginLeft="10dip" />

	</LinearLayout>
{% endhighlight %}

可以看到布局中有ImageView和TextView分别用来显示图片和文字（也就是水果图片和名字），接下来需要自定义一个继承自ArrayAdapter的适配器FruitAdapter，并将泛型指定为Fruit，代码如下：

{% highlight java %}
	public class FruitAdapter extends ArrayAdapter<Fruit> {
    	private int resourceId;

    	public FruitAdapter(Context context, int resource, List<Fruit> objects) {
        	super(context, resource, objects);
        	resourceId=resource;
    	}

    	@Override
    	public View getView(int position, View convertView, ViewGroup parent) {
        	Fruit fruit=getItem(position);
        	View view= LayoutInflater.from(getContext()).inflate(resourceId, null);
        	ImageView ivFruit= (ImageView) view.findViewById(R.id.fruit_image);
        	TextView tvFruit= (TextView) view.findViewById(R.id.fruit_name);
        	ivFruit.setImageResource(fruit.getImageId());
        	tvFruit.setText(fruit.getName());
        	return view;
    	}
	}
{% endhighlight %}

FruitAdapter重写了父类的一组构造函数，用于将上下文，ListView子项布局的id和数据都传递进来。另外又重写了getView(),这个方法在每个子项被滚动到屏幕内的时候调用。在getView方法中，首先通过getItem()得到当前的Fruit实例，然后使用LayoutInflater来为这个子项加载我们的布局，接着通过view.findViewById()来找到布局的ImageView和TextView,最后调用ImageView的setImageResource（）和TextView的setText()来设置显示的图片和文字，最终将布局返回，我们自定义的适配器就完成了。

下面修改MainActivity中的代码：

{% highlight java %}
	public class MainActivity extends Activity {

    	private List<Fruit> fruitList = new ArrayList<Fruit>();

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.activity_main);
        	initFruits();
        	ArrayAdapter<Fruit> adapter=new ArrayAdapter<Fruit>
               (MainActivity.this,android.R.layout.simple_list_item_1,fruitList);
        	ListView listView= (ListView) findViewById(R.id.list_view);
        	listView.setAdapter(adapter);
    	}

    	private void initFruits() {
        	Fruit apple = new Fruit("Apple", R.drawable.apple_pic);
        	fruitList.add(apple);
        	Fruit banana = new Fruit("Banana", R.drawable.banana_pic);
        	fruitList.add(banana);
        	Fruit orange = new Fruit("Orange", R.drawable.orange_pic);
        	fruitList.add(orange);
        	Fruit watermelon = new Fruit("Watermelon", 	R.drawable.watermelon_pic);
        	fruitList.add(watermelon);
        	Fruit pear = new Fruit("Pear", R.drawable.pear_pic);
        	fruitList.add(pear);
        	Fruit grape = new Fruit("Grape", R.drawable.grape_pic);
        	fruitList.add(grape);
        	Fruit pineapple = new Fruit("Pineapple", R.drawable.pineapple_pic);
        	fruitList.add(pineapple);
        	Fruit strawberry = new Fruit("Strawberry", 	R.drawable.strawberry_pic);
        	fruitList.add(strawberry);
        	Fruit cherry = new Fruit("Cherry", R.drawable.cherry_pic);
        	fruitList.add(cherry);
        	Fruit mango = new Fruit("Mango", R.drawable.mango_pic);
        	fruitList.add(mango);
    	}

	}
{% endhighlight %}

可以看到，这里通过initFruits初始化所有的水果数据，在Fruit类构造函数中传入水果的名字和图片Id，然后把创建好的对象添加到水果列表中，接着我们在onCreate方法中创建了FruitAdapter对象，并将FruitAdapter作为适配器传递给ListView，这样定制ListView界面的任务就完成了，运行效果如下：

<figure>
	<img src="/images/listview-2.png">
	<figcaption>图2</figcaption>
</figure>

#提升listview运行效率

之所以说ListView控件难用，就是在于ListView有很多的细节可以优化，其中运行效率就是很重要的一环。目前我们的listview的运行效率是很低的，因为在FruitAdapter中的getView()中每次都将布局重新加载了一次，当listview快速滚动时，这就会成为性能的瓶颈。

仔细观察会发现getView（）还有convertView这个参数，其用于将之前加载好的布局进行缓存，以便以后进行重用，修改FruitAdapter中代码，如下：

{% highlight java %}
	public class FruitAdapter extends ArrayAdapter<Fruit> {
    	private int resourceId;

    	public FruitAdapter(Context context, int resource, List<Fruit> objects) {
        	super(context, resource, objects);
        	resourceId=resource;
    	}

    	@Override
    	public View getView(int position, View convertView, ViewGroup parent) {
        	Fruit fruit=getItem(position);
			if(converView==null){
        		View view= LayoutInflater.from(getContext()).inflate(resourceId, null);
			}else{
				view=convertView;
			}
        	ImageView ivFruit= (ImageView) view.findViewById(R.id.fruit_image);
        	TextView tvFruit= (TextView) view.findViewById(R.id.fruit_name);
        	ivFruit.setImageResource(fruit.getImageId());
        	tvFruit.setText(fruit.getName());
        	return view;
    	}
	}
{% endhighlight %}

可以看到，我们增加了逻辑判断，如果convertView为空，我们就加载布局，不为空就重用convertView。这样的话，在快速滚动listview时，我们就增加了运行效率。

但是，到目前为止我们还可以继续优化，仔细观察发现，每次getView()中还是会调用view的findViewById()方法来获取一次控件的实例。我们可以借助一个ViewHolder来对这部分进行性能优化，修改FruitAdapter中代码如下：

{% highlight java %}
	public class FruitAdapter extends ArrayAdapter<Fruit> {

    	private int resourceId;

    	public FruitAdapter(Context context, int textViewResourceId,
                        List<Fruit> objects) {
        	super(context, textViewResourceId, objects);
        	resourceId = textViewResourceId;
    	}

    	@Override
    	public View getView(int position, View convertView, ViewGroup parent) {
        	Fruit fruit = getItem(position);
        	View view;
        	ViewHolder viewHolder;
        	if (convertView == null) {
            	view = LayoutInflater.from(getContext()).inflate(resourceId, 	null);
            	viewHolder = new ViewHolder();
            	viewHolder.fruitImage = (ImageView) view.findViewById	(R.id.fruit_image);
            	viewHolder.fruitName = (TextView) view.findViewById	(R.id.fruit_name);
            	view.setTag(viewHolder);
        	} else {
            	view = convertView;
            	viewHolder = (ViewHolder) view.getTag();
        	}
        	viewHolder.fruitImage.setImageResource(fruit.getImageId());
        	viewHolder.fruitName.setText(fruit.getName());
        	return view;
    	}

    	class ViewHolder {
        	ImageView fruitImage;
        	TextView fruitName;
    	}
	}
{% endhighlight %}

我们新增了一个内部类ViewHolder，用于对控件的实例进行缓存。当convertView为空时，创建ViewHolder，将控件的实例传入ViewHolder，然后调用View的setTag()存储ViewHolder。当convertView不为空时，再调用View的getTag（）取出ViewHolder。这样，所有控件的的实例都缓存在了ViewHolder里，就不必每次都通过findViewById（）来获取控件实例了。

通过这两部的优化后，我么的listView的运行效率总算是上得了台面了。

#ListView的点击事件

我们好像忘了些什么重要的事，我们是实现了listView的视觉效果，但是我们的listView好像不能点击啊。如果它不能点击的话，还有什么用呢，所以接下来我们就来实现listView的点击事件。修改MainActivity中代码如下：

{% highlight java %}
	public class MainActivity extends Activity {

    	private List<Fruit> fruitList = new ArrayList<Fruit>();

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.activity_main);
        	initFruits();
        	FruitAdapter adapter = new FruitAdapter(MainActivity.this,
                	R.layout.fruit_item, fruitList);
        	ListView listView = (ListView) findViewById(R.id.list_view);
        	listView.setAdapter(adapter);
        	listView.setOnItemClickListener(new OnItemClickListener() {
            	@Override
            	public void onItemClick(AdapterView<?> parent, View view,
                                    int position, long id) {
                	Fruit fruit = fruitList.get(position);
                	Toast.makeText(MainActivity.this, fruit.getName(),
                        	Toast.LENGTH_SHORT).show();
            	}
        	});
    	}

    	private void initFruits() {
        	Fruit apple = new Fruit("Apple", R.drawable.apple_pic);
        	fruitList.add(apple);
        	Fruit banana = new Fruit("Banana", R.drawable.banana_pic);
        	fruitList.add(banana);
        	Fruit orange = new Fruit("Orange", R.drawable.orange_pic);
        	fruitList.add(orange);
        	Fruit watermelon = new Fruit("Watermelon", 	R.drawable.watermelon_pic);
        	fruitList.add(watermelon);
        	Fruit pear = new Fruit("Pear", R.drawable.pear_pic);
        	fruitList.add(pear);
        	Fruit grape = new Fruit("Grape", R.drawable.grape_pic);
        	fruitList.add(grape);
        	Fruit pineapple = new Fruit("Pineapple", R.drawable.pineapple_pic);
        	fruitList.add(pineapple);
        	Fruit strawberry = new Fruit("Strawberry", 	R.drawable.strawberry_pic);
        	fruitList.add(strawberry);
        	Fruit cherry = new Fruit("Cherry", R.drawable.cherry_pic);
        	fruitList.add(cherry);
        	Fruit mango = new Fruit("Mango", R.drawable.mango_pic);
        	fruitList.add(mango);
    	}
	}
{% endhighlight %}

可以看到我们使用setOnItemClickedListener()方法来为ListView注册了一个监听器，当用户点击子项时，OnItemClick（）方法就会被调用，在这个方法中可以通过position参数来判断用户点击的是哪一项，然后获取相应的水果，并通过Toast显示出来，重新运行一下程序并点击一下西瓜，效果如图：

<figure>
	<img src="/images/listview-3.png">
	<figcaption>图3</figcaption>
</figure>


