---
layout: post
title: "设计模式（了解）"
excerpt: "java基础要点（4）"
tags: [集合框架，面试题]
comments: true
---

#单例模式
- 定义：确保一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。
- 使用场景：确保某个类有且只有一个对象的场景，避免产生多个对象消耗过多的系统资源，或者某种类型的对象有且只有一个。例如，创建一个对象需要消耗的资源过多，如要访问IO和数据库等资源，这时就要考虑使用单例模式。
- 实现要点：
	- 构造函数不对外开放，一般为private
    - 通过一个静态方法或者枚举返回单例类对象
    - 确保单例类的对象有且只有一个，尤其是在多线程环境下
    - 确保单例类对象在反序列化时不会重新构建对象
    
代码实现：
（1）饿汉式
{% highlight java %}
          public  class Singleton {
               private  static  Singleton  instance = new Singleton ( ) ;
               private  Singleton ( ) { }
               public  static  Singleton  getInstance ( ) {
                    return  instance ;
               }     
          }
{% endhighlight %}

（2）懒汉式
{% highlight java %}
          public  class  Singleton {
               private  static  Singleton  instance ;
               private  Singleton ( ) { }
               public  static  synchronized  Singleton  getInstance ( ) {
                    if ( instance == null ) {
                         instance = new Singleton ( ) ;
                    }
                    return instance ;
               }
          }
{% endhighlight %}

分析：懒汉式单例相对饿汉式的优点是单例只有在使用时才会被实例化，节省了资源。缺点是为了保证多线程安全，我们将getInstance()设为同步方法，问题是每次调用getInstance方法都要进行同步，即使在instance已经被初始化的情况下。所以懒汉式一般采用DCL（Double Check Lock）方式实现：
         
{% highlight java %}
          public  class  Singleton {
               private  static  Singleton  instance = null ;
               private  Singleton ( ) { }
               public  static  Singleton  getInstance ( ) {
                    if ( instance == null ) {
                         synchronized ( Singleton.class ) {
                              if ( mInstance == null ) {
                                   instance = new Singleton ( ) ; 
                              }
                         }
                    }
                    return instance ;
               }
          }
{% endhighlight %}

分析：getInstance方法中对instance进行了两次判空：第一层判空主要是为了避免不必要的同步，第二层的判空是为了在null的情况下创建实例。其实很好理解：第一层判空不需要同步，在instance已经初始化的情况下省略同步的步骤，直接返回instance,而第二层执行的是new对象操作，其需要同步，再次判空。但是其面临DCL失效问题。还有一种方式用静态内部类实现单例:

{% highlight java %}
     public   class  Singleton {
          private  Singleton ( ) { }
          private  static  Singleton  getInstance ( ) {
               return  SingletonHolder.sInstance ;
          }

          private  static  class  SingleHolder {
               private  static  final  Singleton  sInstance = new Singleton ( ) ;
          }
     }
{% endhighlight %}

2、工厂方法模式

- 定义：定义一个用于创建对象的接口，让子类决定实例化哪个类
- 使用场景：在任何需要生成复杂对象的地方，都可以使用工厂方法模式。复杂对象适合使用工厂模式，用new就可以完成创建的对象无需使用工厂模式。

代码：
{% highlight java %}
     public  abstract  class {
          public  abstract  void  method ( ) ; 
     }
     public  class  ConcreteProductA  extends  Product {
          @Override 
          public  void  method () {
               System.out.println ("我是具体的产品A")；
          }
     }
     public  class  ConcreteProductB  extends  Product {
          @Overide
          public  void  method(){
               System.out.println("我是具体的产品B")；
          }
     }
     
     public  abstract  class  Factory {
          @Override
          public  abstract  Product  createProduct ();
     }
     public  class  ConcreteFactory  extends  Factory{
          @Override
          public  Product  createProduct(){
               return  new  ConcreteProductA ()；
          }
     }

     public  class  Client  {
          public  static  void  main ( String[ ] args ){
               Factory  factory = new ConcreteFactory ( ) ;
               Product  p = factory.createProduct （）；
               p.method（）；
          }
     }
{% endhighlight %}

分析：这里的几个角色都很简单，主要分为四大模块，一是抽象工厂，其为工厂方法模式的核心；二是具体工厂，其实现了具体的业务逻辑；三是抽象产品，是工厂方法模式所创建的产品的父类；四是具体产品，为实现抽象产品的某个具体产品的对象。我们在Client类中构造了一个工厂对象，并通过其生产了一个产品对象，这里我们得到的产品对象时ConcreteProductA的实例，如果想得到ConcreteproductB的实例，更改ConcreteFactory中的逻辑即可。

可以利用反射实现生产具体产品对象，此时，需要在工厂方法的参数列表中传入一个Class类来决定是哪一个产品类：
{% highlight java %}
     public  abstract  class  Factory {
          public  abstract  <T extends  Product> T createProduct ( Class <T> clz )；
     }
     public  class  ConcreteFactory  extends  Factory {
          @Override
          Product  p = null ;
          try {
               p = (Product) Class.forName(clz.getName()).newInstance();
          }catch(Exception e){
               e.printStackTrace();
          }
          return  (T) p；
     }

     public  class  Client {
          public  static  void  main ( String[ ] args ){
               Factory  factory = new ConcreteFactory ( ) ;
               Product  p = factory.createProduct(ConcreteProductB.class)；
               p.method（）；
          }
     }
{% endhighlight %}

3、MVP和MVVM
[Android的MVP设计模式](http://blog.waynell.com/2015/05/29/mvp-on-android/)  
[Android MVVM到底是啥？看完就明白了](http://mp.weixin.qq.com/s?__biz=MzA4MjU5NTY0NA==&mid=401410759&idx=1&sn=89f0e3ddf9f21f6a5d4de4388ef2c32f#rd)

     
          
