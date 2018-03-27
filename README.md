# Android Notes
## Java
**Java Object类有哪些方法？**  
Object类中的方法有：getClass()，hashCode()，equals(Object obj)，protected Object clone()，toString()， notify()，notifyAll()，wait(long millis)，wait(long millis, int nanos)，wait()，protected void finalize()

- getClass()返回Runtime中这个对象的类
- hashCode()计算出这个对象的hash值
- equals(Object obj)判断2个对象是否相同
- clone()得到当前对象的副本
- toString()类的string表示，可复写并输出自己感兴趣的信息
- wait()让当前线程进入等待状态，直到被唤醒或是达到预定的一段时间
- notify()唤醒1个在等待的线程，去竞争对象的锁；如果有多个线程都在这个对象中等待，选择其中一个线程唤醒；被选中的线程由具体实现方式决定
- finalize()由垃圾回收器调用，回收资源

**HashMap与HashTable的区别**  
相同点：  
HashMap是基于哈希表实现的，每一个元素是一个key-value对，其内部通过单链表解决冲突问题，容量不足（超过了阀值）时，同样会自动增长。 HashMap是非线程安全的，多线程环境下可以采用concurrent并发包下的concurrentHashMap。 HashMap 实现了Serializable接口，支持序列化；也实现了Cloneable接口，能被克隆。

Hashtable同样是基于哈希表实现的，同样每个元素是一个key-value对，其内部也是通过单链表解决冲突问题，容量不足（超过了阀值）时，同样会自动增长。 Hashtable也是JDK1.0引入的类，是线程安全的，能用于多线程环境中。 Hashtable同样实现了Serializable接口，它支持序列化，实现了Cloneable接口，能被克隆。

区别：  
1、继承的父类不同
Hashtable继承自Dictionary类，而HashMap继承自AbstractMap类。但二者都实现了Map接口。

2、线程安全性不同
javadoc中关于hashmap的一段描述如下：此实现不是同步的。如果多个线程同时访问一个哈希映射，而其中至少一个线程从结构上修改了该映射，则它必须保持外部同步。 Hashtable 中的方法是Synchronize的，而HashMap中的方法在缺省情况下是非Synchronize的。 在多线程并发的环境下，可以直接使用Hashtable，不需要自己为它的方法实现同步，但使用HashMap时就必须要自己增加同步处理。

**HashMap与HashTable的key能否为null？**  
HashMap对象的key、value值均可为null。 HahTable对象的key、value值均不可为null。  
HahTable对为空的key和value做了处理，会抛出NullPointerException  

**HashMap是如何存储null key的？**  
hashMap是根据key的hashCode来寻找存放位置的。若key为null，hash值记为0；然后继续插入方法。

**ArrayList扩容策略**  
java.util.ArrayList是个固定大小的对象，自身并不直接存储元素内容，而是持有一个引用指向一个数组，真正负责存储元素内容的正是这个数组。 当需要扩容时，扩容的是这个数组，而不是ArrayList对象自身。
- 1.计算的到新的容量capacity，新创建一个大小更大的数组
- 2.把原数组的内容拷贝到新数组去
- 3.把新数组的引用赋值到ArrayList对象的elementData字段上，完成扩容

**JVM内存模型有哪几种？**  
Java虚拟机运行时数据区，分为以下5种：方法区，虚拟机栈（VM Stack），本地方法栈（Native Method Stack），堆（Heap），程序计数器（Program Counter Register）  

**线程共有：Heap(Java堆) 和 Method Area(方法区)**

 Heap(Java堆)  
几乎所有对象实例和数组都要在堆上分配(栈上分配、标量替换除外), 因此是VM管理的最大一块内存, 也是垃圾收集器的主要活动区域. 由于现代VM采用分代收集算法, 因此Java堆从GC的角度还可以细分为: 新生代(Eden区、From Survivor区和To Survivor区)和老年代; 而从内存分配的角度来看, 线程共享的Java堆还还可以划分出多个线程私有的分配缓冲区(TLAB). 而进一步划分的目的是为了更好地回收内存和更快地分配内存.

Method Area(方法区)  
即我们常说的永久代(Permanent Generation), 用于存储被JVM加载的类信息、常量、静态变量、即时编译器编译后的代码等数据  
其中包括运行常量池。Class文件中除了有类的版本、字段、方法、接口等描述信息外,还有一项常量池(Constant Pool Table)用于存放编译期生成的各种字面量和符号引用, 这部分内容会存放到方法区的运行时常量池中(如前面从test方法中读到的signature信息). 但Java语言并不要求常量一定只能在编译期产生, 即并非预置入Class文件中常量池的内容才能进入方法区运行时常量池, 运行期间也可能将新的常量放入池中, 如String的intern()方法.

**“线程私有”，线程级：程序计数器，Java Stack(虚拟机栈)，Native Method Stack(本地方法栈)**

程序计数器  
一块较小的内存空间（“线程私有”内存）, 作用是当前线程所执行字节码的行号指示器(类似于传统CPU模型中的PC), PC在每次指令执行后自增, 维护下一个将要执行指令的地址. 在JVM模型中, 字节码解释器就是通过改变PC值来选取下一条需要执行的字节码指令,分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖PC完成(仅限于Java方法, Native方法该计数器值为undefined).

不同于OS以进程为单位调度, JVM中的并发是通过线程切换并分配时间片执行来实现的. 在任何一个时刻, 一个处理器内核只会执行一条线程中的指令. 因此, 为了线程切换后能恢复到正确的执行位置, 每条线程都需要有一个独立的程序计数器, 这类内存被称为“线程私有”内存.

Java虚拟机栈（Java Virtual Machine Stacks）  
线程私有的，它的生命周期与线程相同。描述的是Java方法执行的内存模型: 每个方法被执行时会创建一个栈帧(Stack Frame)用于存储局部变量表、操作数栈、动态链接、方法出口等信息。 每个方法被调用至返回的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程(VM提供了-Xss来指定线程的最大栈空间, 该参数也直接决定了函数调用的最大深度).

Native Method Stack(本地方法栈)  
与Java Stack作用类似, 区别是Java Stack为执行Java方法服务, 而本地方法栈则为Native方法服务, 如果一个VM实现使用C-linkage模型来支持Native调用, 那么该栈将会是一个C栈, 但HotSpot VM直接就把本地方法栈和虚拟机栈合二为一.

**Java引用类型有哪几种？**  
Java中提供了4个级别的引用：强应用、软引用、弱引用和虚引用  

* [Java面试题全集（上） CSDN](https://blog.csdn.net/jackfrued/article/details/44921941)
* [Java面试题全集（中） CSDN](https://blog.csdn.net/jackfrued/article/details/44931137)
* https://github.com/lietoumai/Hunter


**Java垃圾回收机制**  
回收针对的是动态分配出来的内存，我们关注的是堆内存。

**一，如何确定某个对象是“垃圾”？**

判定对象是否存活都与“引用”有关  
引用计数算法  
给对象中添加一个引用计数器，每当有一个地方引用它时，计数器就加1；当引用失效时，计数器值就减1；任何时刻计数器都为0的对象就是不可能再被使用的。 Java语言没有选用引用计数法来管理内存，因为引用计数法不能很好的解决循环引用的问题。

可达性分析法  
该方法的基本思想是通过一系列的“GC Roots”对象作为起点进行搜索，如果在“GC Roots”和一个对象之间没有可达路径（成为引用链 Reference Chain），则称该对象是不可达的，不过要注意的是被判定为不可达的对象不一定就会成为可回收对象。被判定为不可达的对象要成为可回收对象必须至少经历两次标记过程，如果在这两次标记过程中仍然没有逃脱成为可回收对象的可能性，则基本上就真的成为可回收对象了。

哪些对象可以作为GC Roots？  
- 虚拟机栈（栈帧中的本地变量表）中的引用的对象
- 方法区中的类静态属性引用的对象
- 方法区中的常量引用的对象
- 本地方法栈中JNI（Native方法）的引用对象

**二，典型的垃圾收集算法**  
1.Mark-Sweep（标记-清除）算法  
这是最基础的垃圾回收算法，之所以说它是最基础的是因为它最容易实现，思想也是最简单的。标记-清除算法分为两个阶段：标记阶段和清除阶段。标记阶段的任务是标记出所有需要被回收的对象，清除阶段就是回收被标记的对象所占用的空间。

标记-清除算法实现起来比较容易，但是有一个比较严重的问题就是容易产生内存碎片，碎片太多可能会导致后续过程中需要为大对象分配空间时无法找到足够的空间而提前触发新的一次垃圾收集动作。

2.Copying（复制）算法  
为了解决Mark-Sweep算法的缺陷，Copying算法就被提了出来。它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用的内存空间一次清理掉，这样一来就不容易出现内存碎片的问题。

这种算法虽然实现简单，运行高效且不容易产生内存碎片，但是却对内存空间的使用做出了高昂的代价，因为能够使用的内存缩减到原来的一半。 很显然，Copying算法的效率跟存活对象的数目多少有很大的关系，如果存活对象很多，那么Copying算法的效率将会大大降低。

3.Mark-Compact（标记-整理）算法  
为了解决Copying算法的缺陷，充分利用内存空间，提出了Mark-Compact算法。该算法标记阶段和Mark-Sweep一样，但是在完成标记之后，它不是直接清理可回收对象，而是将存活对象都向一端移动，然后清理掉端边界以外的内存。

4.Generational Collection（分代收集）算法  
分代收集算法是目前大部分JVM的垃圾收集器采用的算法。它的核心思想是根据对象存活的生命周期将内存划分为若干个不同的区域。一般情况下将堆区划分为老年代（Tenured Generation）和新生代（Young Generation），老年代的特点是每次垃圾收集时只有少量对象需要被回收，而新生代的特点是每次垃圾回收时都有大量的对象需要被回收，那么就可以根据不同代的特点采取最适合的收集算法。

目前大部分垃圾收集器对于新生代都采取Copying算法，因为新生代中每次垃圾回收都要回收大部分对象，也就是说需要复制的操作次数较少，但是实际中并不是按照1：1的比例来划分新生代的空间的，一般来说是将新生代划分为一块较大的Eden空间和两块较小的Survivor空间，每次使用Eden空间和其中的一块Survivor空间，当进行回收时，将Eden和Survivor中还存活的对象复制到另一块Survivor空间中，然后清理掉Eden和刚才使用过的Survivor空间。

而由于老年代的特点是每次回收都只回收少量对象，一般使用的是Mark-Compact算法。

注意，在堆区之外还有一个代就是永久代（Permanet Generation），它用来存储class类、常量、方法描述等。对永久代的回收主要回收两部分内容：废弃常量和无用的类。

**Synchronized同步静态方法和非静态方法总结**  
synchronized关键字加到static静态方法上是给那个Class类上锁 而加到非static静态方法是给类的对象加锁

**String和StringBuilder、StringBuffer的区别？**  
Java平台提供了两种类型的字符串：String和StringBuffer/StringBuilder，它们可以储存和操作字符串。其中String是只读字符串，也就意味着String引用的字符串内容是不能被改变的。而StringBuffer/StringBuilder类表示的字符串对象可以直接进行修改。  
区别：  
StringBuffer 字符串变量（线程安全）  
StringBuilder 字符串变量（非线程安全）  
单线程环境下，推荐使用StringBuilder，比StringBuffer快。
**for循环1000次，和循环10万次，怎么组合，1000在外循环，还是10万在外循环**  
中心思想：减少方法的压栈次数，将循环次数多的循环放在内侧，循环次数少的循环放在外侧，减少相关循环变量的实例化次数、初始化次数、比较次数、自增次数，其性能会提高。

## Handle消息处理机制
### 什么是消息机制？ —— 不同线程之间的通信。
为了避免ANR，我们会通常把耗时操作放在子线程里面去执行，因为子线程不能更新UI，所以当子线程需要更新的UI的时候就需要借助到安卓的消息机制，也就是Handler机制了。
### Handler的工作机制简单来说是这样的：
1、Handler通过sendMessage()发送消息message到消息队列MessageQueue，然后MessageQueue会调用enqueueMessage()插入该条消息到MessageQueue。  
2、Looper不断轮询调用MeaasgaQueue的next方法。    
3、如果发现message就调用handler的dispatchMessage，ldispatchMessage进行分发消息，接着我们就可以在复写的handlerMessage()方法中进行消息的处理。  
4、在消息使用完毕后，在Looper.loop()方法中调用msg.recycle()，将消息进行回收，即将消息的所有字段恢复为初始状态。  
## Surfaceview介绍
### surfaceview的核心在于提供了两个线程：UI线程和渲染线程。这里应注意：
1、所有SurfaceView和SurfaceHolder.Callback的方法都应该在UI线程里调用，一般来说就是应用程序主线程。 渲染线程所要访问的各种变量应该作同步处理。  
2、由于surface可能被销毁，它只在SurfaceHolder.Callback.surfaceCreated()和 SurfaceHolder.Callback.surfaceDestroyed() 之间有效，所以要确保渲染线程访问的是合法有效的surface。  
SurfaceView在Android系统中，是一种特殊的视图。它拥有独立的绘图表面，即它不与宿主窗口共享同一个绘图表面。由于拥有独立的绘图表面，因此SurfaceView的UI就可以在一个独立的线程中进行绘制，又由于不会占用主线程资源，运用SurfaceView可以实现复杂而高效的UI，另一方面又不会导致用户输入得不到及时响应。比较适合应用在视频播放，图片浏览，对画面要求高的游戏上面。
### 本质区别：
View：必须在UI的主线程中更新画面，用于被动更新画面。  
surfaceView：UI线程和子线程中都可以。在一个新启动的线程中重新绘制画面，主动更新画面。  
## IntentService介绍
IntentService是继承于Service并处理异步请求的一个类，在IntentService内有一个工作线程来处理耗时操作，启动IntentService的方式和启动传统Service一样，同时，当任务执行完后，IntentService会自动停止，而不需要我们去手动控制。另外，可以启动IntentService多次，而每一个耗时操作会以工作队列的方式在IntentService的onHandleIntent回调方法中执行，并且，每次只会执行一个工作线程，执行完第一个再执行第二个，以此类推。
还有一个说明是：所有请求都在一个单线程中，不会阻塞应用程序的主线程（UI Thread），同一时间只处理一个请求。
### IntentService好处：
1、不用在Service中手动开线程。  
2、当操作完成时，我们不用手动停止Service。  
### 自动停止原理：
在IntentService的onCreate方法中开启了一个异步线程HandlerThread来处理我们的请求，并利用Looper和Handler来管理我们的请求命令队列。在Handler中我们处理完每一个命令都会调用stopSelf(int)方法来停止服务：该方法需要来自onStartCommand方法中的启动ID，只有在接收到最新的启动ID时才会停止服务，就是说，我们的IntentService直到命令队列中的所有命令被执行完后才会停止服务。
## Binder介绍
Binder是Android系统中的一种IPC进程间通信结构。  
Binder的整个设计是C/S结构，客户端进程通过获取服务端进程的代理，并通过向这个代理接口方法中读写数据来完成进程间的数据通信。  
Android之所以选择Binder而不是传统的ipc机制，主要有2个方面的原因：  
1、是安全，每个进程都会被Android系统分配UID和PID，不像传统的在数据里加入UID，这就让那些恶意进程无法直接和其他进程通信，进程间通信的安全性得到提升。  
2、是高效，像Socket之类的IPC每次数据拷贝都需要2次，而Binder只要1次，在手机这种资源紧张的情况下很重要。
## AsyncTask介绍
在Android中提供了一个异步任务的类AsyncTask，简单来说，这个类中的任务是运行在后台线程中的，并可以将结果放到UI线程中进行处理，它定义了三种泛型，分别是Params、Progress和Result，分别表示请求的参数、任务的进度和获得的结果数据。
### 使用原因：
1、其中使用了线程池技术，而且其中的方法很容易实现调用。  
2、可以调用相关的方法，在开启子线程前和后，进行界面的更新。  
3、一旦任务多了，不用每次都new新的线程，可以直接使用。  
### 执行的顺序：
onPreExecute()【执行前开启】--- > doInBackground() --- > onProgressUpdate() --- > onPostExecute()。  
### 实现原理：
1、线程池的创建：  
在创建了AsyncTask的时候，会默认创建一个线程池ThreadPoolExecutor，并默认创建出5个线程放入到线程池中，最多可防128个线程；且这个线程池是公共的唯一一份。  
2、任务的执行：  
在execute中，会执行run方法，当执行完run方法后，会调用scheduleNext()不断的从双端队列中轮询，获取下一个任务并继续放到一个子线程中执行，直到异步任务执行完毕。  
3、消息的处理：  
在执行完onPreExecute()方法之后，执行了doInBackground()方法，然后就不断的发送请求获取数据；在这个AsyncTask中维护了一个InternalHandler的类，这个类是继承Handler的，获取的数据是通过handler进行处理和发送的。在其handleMessage方法中，将消息传递给onProgressUpdate()进行进度的更新，也就可以将结果发送到主线程中，进行界面的更新了。  
4、需要注意的是：   
1）这个AsyncTask类必须由子类调用。  
2）虽然是放在子线程中执行的操作，但是不建议做特别耗时的操作，如果操作过于耗时，建议使用线程池ThreadPoolExecutor和FutureTask。    
### 各版本区别：  
#### 1、版本2.3 - 3.0：  
在3.0版本以前AsyncTask的线程只有1个线程池，核心线程数为5，最大线程数为128，任务队列容量为10。，也就是说当我们启动了10个任务时，只有5个任务能够立刻执行，另外的5个任务则需要等待，当有一个任务执行完毕后，第6个任务才会启动。当线程池中的线程数量没到5个，那么有新的任务会直接启动一个核心线程来执行任务，如果线程池中的线程数量达到了5个，然后任务会被插入到任务队列中等待执行，要是任务队列也满了，就会判断线程池中的数量是否已经达到最大线程数128，如果没有达到就会立刻启动一个非核心线程来执行任务。如果线程数量已经达到线程池规定的最大值，那么就会拒绝执行该任务。也就是说该线程池最多能同时接纳138个任务，其中有128个任务可以同时执行。而且该版本只有一个execute(Params... params) 方法，说明不能自定义线程池来执行任务。  
#### 2、版本3.0 - 4.2：  
3.0之后的AsyncTask同时只能有1个任务在执行。为什么升级之后可以同时执行的任务数反而变少了呢？这是因为更新后的AsyncTask已变得更加灵活，如果不想使用默认的线程池，还可以自由地进行配置。我们可以自定义线程池或者使用SerialExecutor来执行任务，例子：  
```java
    Executor exec = new ThreadPoolExecutor(15, 200, 10,TimeUnit.SECONDS, new LinkedBlockingQueue<Runnable>());  
    new DownloadTask().executeOnExecutor(exec);  
```
这样就可以使用我们自定义的一个Executor来执行任务，而不是使用SerialExecutor。上述代码的效果允许在同一时刻有15个任务正在执行，并且最多能够存储200个任务。  
#### 3、版本4.4以后：  
4.4版本以后的线程池数量改为了动态的，以双核心为例，先获取CPU的核心数为2，线程池的核心线程为3，最大线程数为5,而阻塞队列的容量变为了128。为什么会有这样的改动？可能谷歌公司觉得开启的线程数过多会影响效率吧。而阻塞队列从容量为10变为了128是一个很有意思的事情。在4.4以前的版本，如果已经达到了线程池的核心线程数5，切阻塞队列也达到了10，再有任务加入，就会启动新的非核心线程，也就是说只要同时又16个任务进入就会开启非核心线程。而现在需要132（3+128+1）个任务加入才会开启非核心线程。也就是说要开启新的线程的成本更大了。
## Volley，Okhttp，Retrofit
### Volley
 Volley是2013年Google官方出的一套小而巧的异步请求库，该框架封装的扩展性很强，Volley设计的初衷本身也就是为频繁的、数据量小的网络请求而生。目前已停止了更新，已不怎么使用。
#### 优点：
1、非常适合进行数据量不大，但通信频繁的网络操作。  
2、内部封装了异步线程。  
3、支持get，post网络请求。  
4、封装了ImageLoader，可实现简单的图片加载需求。  
5、可直接在主线程调用服务端并处理返回结果。  
6、扩展性强，面向接口编程。  
#### 缺点：
1、对大文件的上传下载 Volley的表现非常糟糕。      
2、默认是不支持Https请求。    
3、图片加载性能一般。    
4、不支持post大数据。    
5、使用的是httpclient，HttpURLConnection。不过在android 6.0不支持httpclient了，如果想支持得添加org.apache.http.legacy.jar。  
### Okhttp  
Square 公司开源的 OkHttp 是一个专注于连接效率的 HTTP 客户端，封装的一个高性能http请求库。OkHttp 提供了对 HTTP/2 和 SPDY 的支持，并提供了连接池，GZIP 压缩和 HTTP 响应缓存功能，可以把它理解成是一个封装之后的类似HttpUrlConnection的东西，但是在使用的时候仍然需要自己再做一层封装，这样才能像使用一个框架一样更加顺手。从Android 4.4开始HttpUrlConnection内部默认使用的也是OkHttp。
#### 优点
1、支持http请求，https请求。  
2、支持文件下载。  
3、使用的是HttpURLConnection,不要担心android版本的变换。  
4、支持get，post请求。  
5、基于Http的文件上传。  
6、加载图片。  
7、支持session的保持。    
#### 缺点
1、callback回来是在线程里面, 不能刷新UI，需要我们手动处理。  
2、封装比较麻烦。  
### Retrofit
Retrofit是Square公司出品的默认基于OkHttp封装的一套RESTful网络请求框架，RESTful是目前流行的一套api设计的风格， 并不是标准。Retrofit的封装可以说是很强大，里面涉及到一堆的设计模式,可以通过注解直接配置请求，可以使用不同的http客户端，虽然默认是用http ，可以使用不同Json Converter 来序列化数据，同时提供对RxJava的支持，使用Retrofit + OkHttp + RxJava + Dagger2 可以说是目前比较潮的一套框架，但是需要有比较高的门槛。
#### 优点
1、Retrofit可以无缝结合RxJava使用，配合Jackson(或者Gson)和RxJava，再加上Dagger2，效率大大提高。  
2、内部对OkHtttp客户端做了封装，简化了网络请求流程。  
3、具有OkHttp的所有优点。  
#### 缺点：
1、结合RxJava使用需要比较高的门槛。  
## 内存溢出
Android的虚拟机是基于寄存器的Dalvik，它的最大堆大小一般是16M，有的机器为24M。因此我们所能利用的内存空间是有限的。如果我们的内存占用超过了一定的水平就会出现OutOfMemory的错误。
### 造成内存溢出的原因：
#### 1、资源释放问题：  
程序代码的问题，长期保持某些资源（如Context）的引用，造成内存泄露，资源得不到释放。
#### 2、对象内存过大问题：
保存了多个耗用内存过大的对象（如Bitmap），造成内存超出限制。
#### 3、static：
static是Java中的一个关键字，当用它来修饰成员变量时，那么该变量就属于该类，而不是该类的实例。所以用static修饰的变量，它的生命周期是很长的，如果用它来引用一些资源耗费过多的实例（Context的情况最多），这时就要谨慎对待了。
```Java
  public class ClassName {  
     private static Context mContext;  
     //省略  
  }  
```
以上的代码是很危险的，如果将Activity赋值到mContext的话。那么即使该Activity已经onDestroy，但是由于仍有对象保存它的引用，因此该Activity依然不会被释放。  
针对static的解决方案：  
第一、应该尽量避免static成员变量引用资源耗费过多的实例，比如Context。  
第二、Context尽量使用Application Context，因为Application的Context的生命周期比较长，引用它不会出现内存泄露的问题。  
第三、使用WeakReference代替强引用。比如可以使用WeakReference<Context> mContextRef。
#### 4、线程导致内存溢出：
线程生命周期的不可控导致线程产生内存泄露。
```Java
public class MyActivity extends Activity {  
    @Override  
    public void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.main);  
        new MyThread().start();  
    }  
    private class MyThread extends Thread{  
        @Override  
        public void run() {  
            super.run();  
            //do somthing  
        }  
    }  
}  
```
假设MyThread的run函数是一个很费时的操作，当我们开启该线程后，将设备的横屏变为了竖屏，一 般情况下当屏转换时会重新创建Activity，销毁老的Activity，然而事实上并非如此。由于我们的线程是Activity的内部类，所以MyThread中保存了Activity的一个引用，当MyThread的run函数没有结束时，MyThread是不会被销毁的，因此它所引用的老的Activity也不会被销毁，因此就出现了内存泄露的问题。
### 常见的内存使用不当的情况
1、查询数据库没有关闭游标。  
2、构造Adapter时，没有使用缓存的 convertView。  
3、Bitmap对象不在使用时调用recycle()释放内存。  
4、没有及时释放对象的引用，当一个生命周期较短的对象A，被一个生命周期较长的对象B保有其引用的情况下，在A的生命周期结束时，要在B中清除掉对A的引用。  
## MVC，MVP与MVVM
### MVC
#### 架构：
View：对应于布局文件  
Model：业务逻辑和实体模型  
Controllor：对应于Activity  

![image](E:\法本\1.jpg)

View 传送指令到 Controller，View可以与Model直接交互 。   
Controller 完成业务逻辑后，要求 Model 改变状态 。   
Model 将新的数据发送到 View， 用户得到反馈。  
所有通信是单向性。
#### 缺点：
1、view与model存在耦合。  
2、Activity类作为controllor，职责不断增加，会变得庞大臃肿。  
### MVP
MVP 模式将 Controller 改名为 Presenter，同时改变了通信方向。
#### 架构：
View： 负责绘制UI元素、与用户进行交互(在Android中体现为Activity等)。     
Model： 依然是业务逻辑和实体模型。  
Presenter： 作为View与Model交互的中间纽带，处理与用户交互的业务逻辑。
View interface：需要View实现的接口，View通过View interface与Presenter进行交互，降低耦合，方便进行单元测试。  

![image](E:\法本\2.jpg)

View不直接与Model交互，而是通过与Presenter交互来与Model间接交互。  
Presenter与View的交互是通过接口来进行的。  
通常View与Presenter是一对一的，但复杂的View可能绑定多个Presenter来处理逻辑。  
#### 优点
1、在MVP中，Activity的代码不臃肿。    
2、view层与model解耦，我们可以修改视图而不影响Model 。   
3、可以更高效地使用Model，因为所有的交互都发生在一个地方——Presenter内部    
4、一个Presenter可用于多个视图，而不需要改变Presenter的逻辑。  
#### 缺点：
1、P层与V层是通过接口进行交互的，接口粒度不好控制。粒度太小，就会存在大量接口的情况，使代码太过碎版化；粒度太大，解耦效果不好。同时对于UI的输入和数据的变化，需要手动调用V层或者P层相关的接口，相对来说缺乏自动性、监听性。  
2、MVP是以UI为驱动的模型，更新UI都需要保证能获取到控件的引用，同时更新UI的时候要考虑当前是否是UI线程，也要考虑Activity的生命周期  
3、复杂的业务同时也可能会导致P层太大，代码臃肿的问题依然不能解决。  
### MVVM
MVVM 模式将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致。  
#### 架构：
View：负责绘制UI元素、与用户进行交互(在Android中体现为Activity等)。
Model：依然是业务逻辑和实体模型。        
ViewModel：将前面两者联系在一起的对象。    

![image](E:\法本\3.jpg)


一个ViewModel接口提供了两个东西：动作和数据。动作改变Model的下层（click listener，监听文字改变的listener等等），而数据则是Model的内容。  
跟MVP的区别是，它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。  
#### 优点：
1、提高可维护性。解决了MVP大量的手动View和Model同步的问题，提供双向绑定机制。提高了代码的可维护性。  
2、简化测试。因为同步逻辑是交由Binder做的，View跟着Model同时变更，所以只需要保证Model的正确性，View就正确。大大减少了对View同步更新的测试。  
#### 缺点：
1、过于简单的图形界面不适用，或说牛刀杀鸡。  
2、对于大型的图形应用程序，视图状态较多，ViewModel的构建和维护的成本都会比较高。  
3、数据绑定的声明是指令式地写在View的模版当中的，这些内容是没办法去打断点debug的。  


HFP(Hands-free Profile)耳机模式：  
让蓝牙设备可以控制电话，如接听、挂断、拒接、语音拨号等，拒接、语音拨号要视蓝牙耳机及电话是否支持。
HSP（Handset Profile）耳机模式
用于支持蓝牙耳机与移动电话之间使用  
HID() 人机接口设备配置文件  
HID 配置文件定义了 Bluetooth HID（如键盘、指向设备、游戏设备及远程监视设备）使用的协议、程序及功能

**Andorid 子线程更新UI为什么会崩溃**  
子线程更新UI能抛出CalledFromWrongThreadException异常。 View的invalidate()方法会触发ViewParent接口的invalidateChildInParent方法。 而实现类ViewRootImpl的这个invalidateChildInParent方法中会检查当前线程是否UI线程。

在onResume之前用非UI线程更新能UI，而onResume之后就不行了。这是因为onResume之前还没有创建ViewRootImpl示例。

### Android 触摸事件描述
触摸事件分发： 由根视图向子view分发。onInterceptTouchEvent 方法（ViewGroup才有）的返回值决定是否拦截触摸事件（true：拦截，false：不拦截）。如果 ViewGroup 拦截了触摸事件，那么其 onTouchEvent 就会被调用用来处理触摸事件。

触摸事件消费： onTouchEvent 方法的返回值决定是否处理完成触摸事件（true：已经处理完成，不需要给父 ViewGroup 处理，false：还没处理完成 ，需要传递给父 ViewGroup 处理）。

### Android View绘制流程
measure、layout、draw

在onMeasure方法中View会对其所有的子元素执行measure过程，此时measure过程就从父容器"传递"到了子元素中，接着子元素会递归的对其子元素进行measure过程，如此反复完成对整个View树的遍历。 onLayout与onDraw过程的执行流程与此类似。

measure过程决定了View的测量宽高，这个过程结束后，就可以通过getMeasuredHeight和getMeasuredWidth获得View的测量宽高了；

layout过程决定了View在父容器中的位置和View的最终显示宽高，getTop等方法可获取View的top等四个位置参数（View的左上角顶点的坐标为(left, top), 右下角顶点坐标为(right, bottom)）， getWidth和getHeight可获得View的最终显示宽高（width = right - left；height = bottom - top）。

draw过程决定了View最终显示出来的样子，此过程完成后，View才会在屏幕上显示出来。

### Android内存泄露有哪几种？
内存泄漏：简单说来就是内存无法被回收。我们所讨论的内存泄漏，主要讨论堆内存，他存放的就是引用指向的对象实体。

* 单例模式引起的内存泄露  
  * 由于单例模式的静态特性，它的生命周期可能和进程一样长，如果让单例无限制的持有Activity的强引用就会导致内存泄漏
* 非静态内部类引起内存泄露
  * 未移除已不需要的回调发生内存泄露
* 资源未关闭引起的内存泄露情况
  * 比如：BroadCastReceiver、Cursor、Bitmap、IO流、自定义属性attribute attr.recycle()回收。
    当不需要使用的时候，要记得及时释放资源。否则就会内存泄露。
* 无限循环动画
  * 没有在onDestroy中停止动画，否则Activity就会变成泄露对象。
    比如：轮播图效果。
* 集合引发的内存泄漏
  * 存放了太多对象
* 循环引用
  * A持有B，B持有C，C持有A，这样的设计谁都得不到释放

### apk加固  
加固原理是将JAVA代码转化为C/C++编译成.so文件，使用JNI调用。目前市场上比较成熟的加固产品有，梆梆加固、360加固保、爱加密等。

### TCP、UDP和HTTP详解
**TCP**  
面向连接的TCP  
“面向连接”就是在正式通信前必须要与对方建立起连接。比如你给别人打电话，必须等线路接通了、对方拿起话筒才能相互通话。

TCP（Transmission Control Protocol，传输控制协议）是基于连接的协议，也就是说，在正式收发数据前，必须和对方建立可靠的连接。一个TCP连接必须要经过三次“对话”才能建立起来，其中的过程非常复杂，我们这里只做简单、形象的介绍，你只要做到能够理解这个过程即可。

我们来看看这三次对话的简单过程：
* 1.主机A向主机B发出连接请求数据包：“我想给你发数据，可以吗？”，这是第一次对话；

* 2.主机B向主机A发送同意连接和要求同步（同步就是两台主机一个在发送，一个在接收，协调工作）的数据包：“可以，你什么时候发？”，这是第二次对话；

* 3.主机A再发出一个数据包确认主机B的要求同步：“我现在就发，你接着吧！”，这是第三次对话。

三次“对话”的目的是使数据包的发送和接收同步，经过三次“对话”之后，主机A才向主机B正式发送数据。

TCP协议能为应用程序提供可靠的通信连接，使一台计算机发出的字节流无差错地发往网络上的其他计算机，对可靠性要求高的数据通信系统往往使用TCP协议传输数据。  

**tcp协议3次握手,4次挥手**

1.建立连接协议,三次握手

    (1).报文1,客服端发送一个带SYN标志的TCP报文到服务器.SYN（synchronous）是TCP/IP建立连接时使用的握手信号。SYN攻击是个比较有意思的事情.

    (2).报文2,服务器端回应客服端,报文带ACK和SYN编制.

    (3).报文3,客服端在回应一个ack报文.  服务器端接受到此ack信号好才启动连接,此时连接才算成立.

    为什么需要三次握手:由于各个网路的情况不同,可能会产生报文传输延时,从而变成失效的连接请求.防止失效的连接请求报文段突然有传到了服务端,从而产生错误.解决网络中存在重复分组的问题.
    只是传输层为了防止失效的连接请求而设立的机制.    


2.连接终止协议,四次挥手

    tcp连接是全双工,因此每个方向都必须单独进行关闭.每个方向,当一方完成它的数据发送任务后就能发送一个FIN来终止这个方向的连接.

    (1).TCP客服端发送一个FIN,用来关闭客服到服务器的数据传送.

    (2).服务器端接收到FIN,响应一个ACK,确认序号,为接收到的序号加1.

    (3).服务器关闭客服端的连接,发送一个FIN给客服端.

    (4).客服端发回ACK报文确认,并将确认序号设置为序号加1.

    4次挥手,为了断开连接.

**UDP**  
“面向非连接”就是在正式通信前不必与对方先建立连接，不管对方状态就直接发送。这与现在风行的手机短信非常相似：你在发短信的时候，只需要输入对方手机号就OK了。

UDP（User Data Protocol，用户数据报协议）是与TCP相对应的协议。它是面向非连接的协议，它不与对方建立连接，而是直接就把数据包发送过去！

UDP 适用于一次只传送少量数据、对可靠性要求不高的应用环境。比如，我们经常使用“ping”命令来测试两台主机之间TCP/IP通信是否正常，其实 “ping”命令的原理就是向对方主机发送UDP数据包，然后对方主机确认收到数据包，如果数据包是否到达的消息及时反馈回来，那么网络就是通的。例如， 在默认状态下，一次“ping”操作发送4个数据包。大家可以看到，发送的数据包数量是4包，收到的也是4包（因为对方主机收到后会发回一 个确认收到的数据包）。这充分说明了UDP协议是面向非连接的协议，没有建立连接的过程。正因为UDP协议没有连接的过程，所以它的通信效果高；但也正因为如此，它的可靠性不如TCP协议高。QQ就使用UDP发消息，因此有时会出现收不到消息的情况。  

**https协议和http协议的区别:**
* 1.https协议需要到ca申请

* 2.http传输的信息是明文的,https传输的信息是经过ssl加密

* 3.http和https使用的是完全不同的连接方式,端口不一样,http端口号80,https端口为443.

* 4.http连接简单,是无状态的.

* 5.https协议是有ssl+http协议构建的可进行加密传输//身份认证的网络协议,比http协议安全.  
