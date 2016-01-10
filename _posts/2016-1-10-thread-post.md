---
layout: post
title: "Java多线程及并发知识点"
excerpt: "java基础要点（2）"
tags: [多线程,并发，面试题]
comments: true
---

多线程和并发问题是Java面试中主要考察的一个方面，这里总结Java并发的一些基础知识点和常考面试题。

#线程的创建

- 进程：一个程序的执行
- 线程：程序中单个顺序的流控制称为线程
- 一个进程中可以含有多个线程。一个进程中的多个线程可以分享CPU（并发的或以时间片的方式）、共享内存（如多个线程访问同一对象）
- Java中支持多线程：从语言级别支持多线程：如Object中的wait()、notify()方法；java.lang中的类Thread
- 线程体通过run方法来实现。线程启动后就自动调用run()方法，通常run()方法执行一个时间较长的操作（如一个循环、显示一系列图片、下载一个文件）
- 创建线程的两种方法：1、通过继承Thread类创建线程（子类中重写run()方法）2、通过向Thread（） 构造方法传递Runnable对象来创建线程（Runnable对象中重写run()方法）

##线程的控制
     
线程的状态与生命周期：

<figure>
	<img src="/images/thread-1.png">
</figure>

- New（创建）：这种情况指的是，通过New关键字创建了Thread类（或其子类）对象；
- Runnable（就绪）：这种情况指的是Thread类的对象调用了start()方法，这时的线程就等待时间片轮转到自己这，以便获得CPU；第二种情况是线程处于Running状态时并没有运行完自己的run（）方法，时间片用完后回到Runnable状态；还有种情况就是处于Blocked状态的线程结束了当前的阻塞状态之后重新回到Runnable状态。
- Running（运行）：这时是我线程指的是获得CPU的Runnable线程，Running状态时所有线程都希望获得的状态。
- Dead（终止）：处于Running状态的线程在执行完Run（）方法后，就变成Dead状态了。
- Blocked（阻塞）：这种状态指的是处于Running状态的线程，出于某种原因，比如调用了sleep()方法、等待用户输入等而让出当前的CPU给其他的线程。
     
处于Runnable状态的线程变为Blocked状态的原因，除了该线程调用了sleep()方法，等待输入原因外，还有就是在当前线程中调用了其他线程的join()方法、当访问一个对象的方法时，该方法被锁定等。相应地，当处于Blocked状态的线程在满足一下条件时就会由该状态转到Runnable状态，这些条件是：sleep()的线程醒来（sleep的时间到了）、获得了用户的输入、调用了join的其他线程结束、获得了对象锁。一般情况下，都是处于Runnable的线程和处于Running状态的线程互相切换，直到运行完run()方法，线程结束，进入Dead状态。

线程调度器是一个操作系统服务，它负责为Runnable状态的线程分配CPU时间。一旦我们创建一个线程并启动它，它的执行便依赖于线程调度器的实现。时间分片是指将可用的CPU时间分配给可用的Runnable线程的过程。分配CPU时间可以基于线程优先级或者线程等待的时间。线程调度并不受到Java虚拟机控制，所以就是说不要让你的程序依赖于线程的优先级。

- 线程的启动：start（）；
- 线程的结束：设定一个标记变量，以结束相应的循环及方法，线程无法强行终止；
- 暂时阻止线程的执行：sleep（int time）
- 设定线程的优先级：setPriority（int priority）（MIN_PRIORITY,MAX_PRIORITY,NORM_PRIORITY）
- 线程的种类：一类是普通线程（非DaeMon线程），在java程序中，只要还有普通线程，则整个程序就不会结束；一类是Daemon线程，又叫守护线程或后台线程，如果普通线程结束了，则后台线程自动终止（垃圾回收线程就是后台线程）。后台线程通过setDaemon(true)实现。

##线程的同步

同时运行的线程需要共享数据。此时就必须考虑其他的线程的状态和行为，这时就需要实现同步。Java引入了对象互斥锁的概念，来保证共享数据操作的完整性。每个对象都对应于一个monitor（监视器）它上面一个称为“互斥锁（lock,mutex）”的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象。关键词synchronized用来与对象的互斥锁联系。

synchronized的用法:

- 对代码片段：synchronized（对象）{……}
- 对某个方法：synchronized放在方法声明中；public synchronized void push(char c){……}；相当于对synchronized(this),表示整个方法为同步方法。
     
线程同步控制：

- 使用wait()方法可以释放对象锁；
- 使用notify()或notifyAll()可以让等待的一个或所有线程进入就绪状态；
- Java里可以将wait和notify放在synchronized里面，是因为Java是这样处理的：在synchronized代码被执行期间，线程调用对象的wait()方法会释放对象锁标志，然后进入等待状态，然后由其他线程调用notify()或者notifyAll()方法通知正在等待的线程。
     
当一个线程访问object的一个synchronized(this)同步代码块时，它就获得了这个object的对象锁。结果，其他线程对该object对象所有同步代码部分的访问都被暂时阻塞。synchronized方法控制对类成员变量的访问：每个类实例对应一把锁，每个synchronized方法都必须获得调用该方法的类实例的锁方能执行，否则所属线程阻塞，方法一旦执行就独占该锁，直到从该方法返回时才将锁释放，此后被阻塞的线程方能获得该锁，重新进入可执行状态。这种机制确保了同一时刻对于每一个类实例，其所有声明为synchronized的成员函数中至多只有一个处于可执行状态（因为至多只有一个能够获得该类实例对应的锁），从而有效避免了类成员变量的访问冲突（只要所有可能访问该类成员变量的方法均被声明为synchronized）。同步函数使用的锁是this,静态同步函数使用的锁是该类的字节码文件对象。synchronized块是这样一个代码块，其中的代码必须获得对象syncObject（如前所述，可以是类实例或类）的锁方能执行，具体机制同前所述。利用同步代码块可以解决线程安全问题。

#多线程相关重要面试题

1、什么是线程？

线程是操作系统能够进行运算调度的最小单位，它被包含在进程之中，是进程的实际运作单位。程序员可以通过它进行多处理器编程，你可以使用多线程对运算密集型任务提速。比如，如果一个线程完成一个任务要100ms，那么用是个线程完成该任务只需10ms。

2、进程和线程有什么区别？

线程是进程的子集，一个进程可以有很多线程，每条线程并执行不同的任务。不同的进程使用不同的内存空间，而所有的线程共享一片相同的内存空间。别把它和栈内存搞混，每个线程都拥有单独的栈内存用来存储本地数据。

3、如何在java中实现线程？

在语言层有两种方式。java.lang.Thread类的实例就是一个线程但是它需要调用java.lang.Runnable接口来执行，由于线程本身就是调用的Runnable 接口所以你可以继承java.lang.Thread类或者直接调用Runnable接口来重写run()方法实现线程。

4、用 Runnable还是Thread?

大家都知道可以通过继承Thread类或者调用Runnable接口来实现线程，问题是，哪个方法更好呢？什么情况下使用它？因为Java不支持类的多重继承，但可以实现多个接口。所以如果你要继承其他类，就可以调用Runnable接口实现。

5、Thread类中的start()方法和run()方法有什么区别？

start()方法被用来启动新创建的线程，而start()内部调用了run()方法，这和直接调用run()方法的效果不一样。当你调用run()方法的时候，只会是在原来的线程中调用，没有新的线程启动，start()方法才会启动新线程。

6、Java中Runnable和Callable有什么不同？

Runnable和Callable都代表那些要在不同的线程中执行的任务。Runnable从JDK1.0开始就有了，Callable是在JDK1.5增加的。它们的主要区别是Callable的call（）方法可以返回值和抛出异常，而Runnable的run()方法没有这些功能。Callable可以返回装载有计算结果的Future对象。

7、Java中CyclicBarrier和CountDownLatch有什么不同？

CyclicBarrier和CountDownLatch都可以用来让一组线程等待其它线程。与CyclicBarrier不同的是，CountdownLatch不能重新使用。

8、Java内存模型是什么？

Java内存模型规定和指引Java程序在不同的内存架构、CPU和操作系统间有确定性地行为。它在多线程的情况下尤其重要。Java内存模型对一个线程所做的变动能被其他线程可见提供了保障，它们之间是先行发生关系。这个关系定义了一些规则让程序员在并发编程时思路更清晰。比如，先行发生关系确保了：
-  线程内的代码能够按顺序执行，这被称为程序次序规则。
-  对于同一个锁，一个解锁操作一定要发生在时间上后发生的另一个锁定操作之前，也叫做管程锁定机制。
-  前一个对violatile的写操作在后一个violatile的读操作之前，也叫violatile变量规则。
-  一个线程内的任何操作必需在这个线程的start()调用之后，也叫作线程启动规则。
- 一个线程的所有操作都会在线程终止之前，线程终止规则。
- 一个对象的终结操作必需在这个对象构造完成之后，也叫对象终结规则。
- 可传递性

9、Java中的violatile变量是什么？

violatile是一个特殊的修饰符，只有成员变量才能使用它。在Java并发程序缺少同步类的情况下，多线程对成员变量的操作对其他线程是透明的。violatile变量可以保证下一个读取操作会在前一个写操作之后发生。
编译器为了加快程序运行的速度,对一些变量的写操作会先在寄存器或者是CPU缓存上进行,最后才写入内存。而在这个过程,变量的新值对其他线程是不可见的.而volatile的作用就是使它修饰的变量的读写操作都必须在内存中进行!  volatile能够使变量在值发生改变时能尽快地让其他线程知道。

10、violatile和synchronized的区别？

violatile本质是在告诉jvm当前变量在寄存器中的值是不确定的，需要从主存中读取，synchronized则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住。
violatile仅能使用在变量级别，synchronized则可以使用在变量，方法。
violatile仅能实现变量的修改可见性，但不具备原子性，而synchronized则可以保证变量的修改可见性和原子性。violatile不会造成线程的阻塞，而synchronized可能会造成线程的阻塞。
violatile标记的变量不会被编译器优化，而synchronized标记的变量可以被编译器优化。

11、原子性、可见性的概念？

原子性是说一个操作是否可分割。非原子操作都会存在线程安全问题，需要我们使用同步技术来让它变成一个原子操作。可见性是指线程之间的可见性，一个线程修改的状态对另一个线程是可见的，也就是一个线程修改的结果，另一个线程马上就能看的。

12、wait()和sleep()的区别？

sleep（）来自Thread类，wait （）来自Object类。
调用sleep（）方法的过程中，线程不会释放对象锁，而调用wait（）方法线程会释放对象锁。
sleep（）不出让系统资源，wait（）让出系统资源其他线程可以占用CPU
sleep(milliseconds)需要指定一个睡眠时间，时间一到会自动唤醒

13、什么是线程安全？Vector是一个线程安全类吗？

如果你的代码所在进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是安全的。一个线程安全的计数器类的同一个实例对象在被多个线程使用的情况下也不会出现计算失误。很显然你可以将集合类分成线程安全的和非线程安全的。Vector是用同步方法来实现线程安全的，而和它相似的ArrayList不是线程安全的。

14、如何确保线程安全？

在Java中有很多方法来保证线程安全，同步，使用原子类（atomic concurrent classes），实现并发锁(Lock)，使用violatile关键字，使用不变类和线程安全类。

15、如何在两个线程间共享数据？

你可以通过共享对象来实现这个目的，或者是使用阻塞队列这样并发的数据结构。

16、什么是阻塞队列？如何使用阻塞队列？

java.util.concurrent.BlockingQueue的特性是：当队列是空的时，从队列中获取或删除元素的操作将会被阻塞，或者当队列是满时，往队列里添加元素的操作会被阻塞。阻塞队列不接受空值，当你尝试向队列中添加空值的时候，它会抛出NullPointerException。阻塞队列的实现都是线程安全的，所有的查询方法都是原子的，并且使用了内部锁或者其他形式的并发控制。BlockingQueue接口是java collections框架的一部分，它主要用于实现生产者-消费者问题。

17、什么是ThreadLocal?

ThreadLocal用于创建线程的本地变量，我们知道一个对象的所有线程会共享它的全局变量，所以这些变量不是线程安全的，我们可以使用同步技术。但是当我们不想使用同步的时候，我们可以选择ThreadLocal变量。每个线程都会拥有他们自己的ThreadLocal变量，它们可以使用get()\set()方法区获取他们的默认值或者在线程内部改变他们的值。ThreadLocal实例通常是希望它们同线程状态关联起来是private static属性。

18、Java中的同步集合与并发集合有设么区别？

同步集合与并发集合都为多线程提供了合适的线程安全的集合，不过并发集合的可扩展性更高。在Java1.5之前程序员们只有同步集合来用且在多线程并发的时候会导致争用，阻碍了系统的扩展性。Java5介绍了并发集合像ConcurrentHashMap,不仅提供线程安全还用锁分离和内部分区等现代技术提高了可扩展性。

19、什么是线程池？为什么要使用它？

创建线程要花费昂贵的资源和时间，如果任务来了才创建线程那么相应时间会变长，而且一个进程能创建的线程数有限。为了避免这些问题，在程序启动的时候就创建若干线程来响应处理，它们被称为线程池，里面的线程叫工作线程。从JDK1.5开始，Java API提供了Executor框架让你可以创建不同的线程池。比如单线程池，每次处理一个任务；数目固定的线程池或者是缓存线程池（一个适合很多生存期短的任务的程序的可扩展线程池）

20、如何避免死锁？

Java多线程中的死锁是指两个或两个以上的进程在执行过程中因争夺资源而造成的一种相互等待的现象，若无外力作用，它们都将无法推进下去。死锁的发生必须满足以下条件：

- 互斥条件：一个资源每次只能被一个进程使用。
- 请求和保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
- 不剥夺条件：进程已获得的资源，在未使用完之前，不能强行剥夺。
- 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。

避免死锁最简单的方法就是阻止循环等待条件，将系统中所有的资源设置标志位、排序，规定所有的进程申请资源必须以一定的顺序做操作来避免死锁。

21、Java中synchronized和ReentrantLock有什么不同？

Java在过去很长一段时间只能通过synchronized关键字来实现互斥，它有一些缺点。比如那你不能扩展锁之外的方法或者块边界，尝试获取锁时不能中途取消等。Java5通过Lock接口提供了更复杂的控制来解决这些问题，ReentrantLock类实现Lock，它拥有与synchronized相同的并发性和内存语义且它还具有可扩展性。

22、Java中ConcurrentHashMap的并发度是什么？

ConcurrenthashMap把实际map划分成若干部分来实现它的可扩展性和线程安全。这种划分是使用并发度获得的，它是ConcurrentHashMap类构造函数的一个可选参数，默认值为16，这样在多线程情况下就能避免争用。

23、Java中notify()和notifyAll()有什么区别？

notify()方法不能唤醒某个具体的线程，所以只有一个线程在等待的时候它才有用武之地。而notifyAll()唤醒所有线程并允许他们争夺锁确保了至少有一个线程能继续运行。

24、为什么wait(),notify()和notifyAll()方法定义在Object类中而不是Thread中？

Java提供的锁是对象级的而不是线程级的，每个对象都有锁，通过线程获得。如果线程需要等待某些锁那么调用对象中的wait()方法就有意义了。如果wait()方法定义在Thread类中，线程正在等待的是哪个锁就不明显了。简单地说，由于wait,notify和notifyAll都是锁级别的操作，所以把它们定义在Object类中因为锁属于对象。

25、实现生产者-消费者？

{% highlight java %}

	public  class  ProducerConsumerTest{
     public  static  void  main(String[ ] args){
          PublicResource resource = new PublicResources();
          new Thread( new ProducerThread(resource)).start();
          new Thread( new ConsumerThread(resource)).start();
          new Thread( new ProducerThread(resource)).start();
          new Thread( new ConsumerThread(resource)).start();
          new Thread( new ProducerThread(resource)).start();
          new Thread( new ConsumerThread(resource)).start();
     }
	}
	/**
	* 生产者线程，负责生产公共资源
	*/
	class  ProducerThread  implements  Runnable{
     private  PublicResource  resource ;
     public  ProducerThread (PublicResouce resource){
          this.resource = resource ;
     }
     @Override
     public void run() {
          while(true){
               try{
                    Thread.sleep((long)(Math.random()*1000)) ;
               }catch( InterruptedException e){
                    e.printStackTrace();
               }
               resource.increase();
          }
     }
	}
	/**
	*消费者线程，负责消费公共资源
	*/
	class  ConsumerThread  implements  Runnable {
     private  PublicResource  resource ;
     public  ConsumerThread ( PublicResource resource ){
          this.resource = resource ;
     }
     @Override
     public  void  run() {
          while(true){
               try{
                     Thread.sleep((long)(Math.random()*1000));
                }catch(InterruptedException e){
                    e.printStackTrace();
               }
               resource.decrease();
          }
     }
	} 
	/**
	*公共资源类
	*/
	class PublicResource {
     private  int  number = 0 ;
     private  int  size = 10 ;
     
     public  synchronized  void  increase () {
          while ( number >= size ){
               try {
                    wait();
               }catch(InterruptedException e){
                    e.printStackTrace();
               }
          }
          number++;
          System.out.println("生产了一个，总共有："+ number)；
          notifyAll();
     } 

     public  synchronized  void  decrease () {
          while ( number <= 0 ){
               try {
                    wait();
               }catch(InterruptedException e){
                    e.printStackTrace();
               }
          }
          number - - ;
          System.out.println("消费了一个，总共有："+ number)；
          notifyAll();
     } 
	}
{% endhighlight %}

