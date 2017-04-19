---
title: 网络图片加载工具
date: 2017-04-12 19:58:32
tags: 开源框架 内存策略 缓存策略 android
---
## 《Android-Universal-Image-Loader》
##### 1、 数据结构分析


	/** Stores not strong references to objects */
	private final Map<String, Reference<Bitmap>> softMap = Collections.synchronizedMap(new HashMap<String, Reference<Bitmap>>());
	
	private final List<Bitmap> hardCache = Collections.synchronizedList(new LinkedList<Bitmap>());
	
LinkedHashMap中的get()方法不仅返回所匹配的值，并且在返回前还会将所匹配的key对应的entry调整在列表中的顺序（LinkedHashMap使用双链表来保存数据），让它处于列表的最后。当然，这种情况必须是在LinkedHashMap中accessOrder==true的情况下才生效的，反之就是get()方法不会改变被匹配的key对应的entry在列表中的位置

LinkedHashMap:
	
	根据链表中元素的顺序可以分为：按插入顺序的链表，和按访问顺序(调用get方法)的链表。  
	
	默认是按插入顺序排序，如果指定按访问顺序排序，那么调用get方法后，会将这次访问的元素移至链表尾部，不断访问可以形成按访问顺序排序的链表。  可以重写removeEldestEntry方法返回true值指定插入元素时移除最老的元素。 



##### 2、 缓存策略分析
UIL中的内存缓存策略

	1. 只使用的是强引用缓存 
	
	LruMemoryCache（这个类就是这个开源框架默认的内存缓存类，缓存的是bitmap的强引用，下面我会从源码上面分析这个类）
	 2.使用强引用和弱引用相结合的缓存有
	
	 UsingFreqLimitedMemoryCache（如果缓存的图片总量超过限定值，先删除使用频率最小的bitmap）
	
	LRULimitedMemoryCache（这个也是使用的lru算法，和LruMemoryCache不同的是，他缓存的是bitmap的弱引用）
	FIFOLimitedMemoryCache（先进先出的缓存策略，当超过设定值，先删除最先加入缓存的bitmap）
	LargestLimitedMemoryCache(当超过缓存限定值，先删除最大的bitmap对象)
	LimitedAgeMemoryCache（当 bitmap加入缓存中的时间超过我们设定的值，将其删除）
	 3.只使用弱引用缓存
	
	 WeakMemoryCache（这个类缓存bitmap的总大小没有限制，唯一不足的地方就是不稳定，缓存的图片容易被回收掉）

 

我们直接选择UIL中的默认配置缓存策略进行分析。

ImageLoaderConfiguration config = ImageLoaderConfiguration.createDefault(context);
ImageLoaderConfiguration.createDefault(…)这个方法最后是调用Builder.build()方法创建默认的配置参数的。默认的内存缓存实现是LruMemoryCache，磁盘缓存是UnlimitedDiscCache。

业务如下

	① UI：请求数据，使用唯一的Key值索引Memory Cache中的Bitmap。
	
	② 内存缓存：缓存搜索，如果能找到Key值对应的Bitmap，则返回数据。否则执行第三步。
	
	③ 硬盘存储：使用唯一Key值对应的文件名，检索SDCard上的文件。
	
	④ 如果有对应文件，使用BitmapFactory.decode*方法，解码Bitmap并返回数据，同时将数据写入缓存。如果没有对应文件，执行第五步。
	
	⑤ 下载图片：启动异步线程，从数据源下载数据(Web)。
	
	⑥ 若下载成功，将数据同时写入硬盘和缓存，并将Bitmap显示在UI中。


##### 3、 设计模式应用分析
1. 单例模式的应用在
2. builder模式


##### 4、 线程池
线程池能够带来三个好处：
	
	第一：降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗
	第二：提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行
	第三：提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控
	

**创建一个ThreadPoolExecutor需要的参数：**

	corePoolSize（线程池的基本大小）：当提交一个任务到线程池时，线程池会创建一个线程来执行任务，即使其他空闲的基本线程能够执行新任务也会创建线程，等到需要执行的任务数大于线程池基本大小时就不再创建。如果调用了线程池的prestartAllCoreThreads方法，线程池会提前创建并启动所有基本线程。
	
	runnableTaskQueue（任务队列）：用于保存等待执行的任务的阻塞队列。 可以选择以下几个阻塞队列。
	
	ArrayBlockingQueue：是一个基于数组结构的有界阻塞队列，此队列按 FIFO（先进先出）原则对元素进行排序。
	
	LinkedBlockingQueue：一个基于链表结构的阻塞队列，此队列按FIFO （先进先出） 排序元素，吞吐量通常要高于ArrayBlockingQueue。静态工厂方法Executors.newFixedThreadPool()使用了这个队列。
	
	SynchronousQueue：一个不存储元素的阻塞队列。每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQueue，静态工厂方法Executors.newCachedThreadPool使用了这个队列。
	
	PriorityBlockingQueue：一个具有优先级的无限阻塞队列。
	
	maximumPoolSize（线程池最大大小）：线程池允许创建的最大线程数。如果队列满了，并且已创建的线程数小于最大线程数，则线程池会再创建新的线程执行任务。值得注意的是如果使用了无界的任务队列这个参数就没什么效果。
	
	ThreadFactory：用于设置创建线程的工厂，可以通过线程工厂给每个创建出来的线程设置更有意义的名字。
	
	RejectedExecutionHandler（饱和策略）：当队列和线程池都满了，说明线程池处于饱和状态，那么必须采取一种策略处理提交的新任务。这个策略默认情况下是AbortPolicy，表示无法处理新任务时抛出异常。以下是JDK1.5提供的四种策略。
		# AbortPolicy：直接抛出异常。
		# CallerRunsPolicy：只用调用者所在线程来运行任务。
		# DiscardOldestPolicy：丢弃队列里最近的一个任务，并执行当前任务。
		# DiscardPolicy：不处理，丢弃掉。
	当然也可以根据应用场景需要来实现RejectedExecutionHandler接口自定义策略。如记录日志或持久化不能处理的任务。
	
	keepAliveTime（线程活动保持时间）：线程池的工作线程空闲后，保持存活的时间。所以如果任务很多，并且每个任务执行的时间比较短，可以调大这个时间，提高线程的利用率。
	
	TimeUnit（线程活动保持时间的单位）：可选的单位有天（DAYS），小时（HOURS），分钟（MINUTES），毫秒(MILLISECONDS)，微秒(MICROSECONDS, 千分之一毫秒)和毫微秒(NANOSECONDS, 千分之一微秒)。
	
**用Executors静态工厂方法创建的线程池类型：**
	    
    a) newFixedThreadPool：创建一个定长的线程池。达到最大线程数后，线程数不再增长。如果一个线程由于非预期Exception而结束，线程池会补充一个新的线程。
    
    b) newCachedThreadPool：创建一个可缓存的线程池。当池长度超过处理需求时，可以回收空闲的线程。
    
    c) newSingleThreadPool：创建一个单线程executor。
    
    d) newScheduledThreadPool：创建一个定长的线程池，而且支持定时的以及周期性的任务执行。类似于Timer。但是，Timer是基于绝对时间，对系统时钟的改变是敏感的，而ScheduledThreadPoolExecutor只支持相对时间。
	     1) Timer是创建唯一的线程来执行所有的timer任务。如果一个任务超时了，会导致其他的TimerTask时间准确性出问题。
	     2）如果TimerTask抛出uncheck 异常，Timer将会产生无法预料的行为。因此，ScheduledThreadPoolExecutor可以完全代替Timer。
	     
**合理的配置线程池**
	
	要想合理的配置线程池，就必须首先分析任务特性，可以从以下几个角度来进行分析：
	
	任务的性质：CPU密集型任务，IO密集型任务和混合型任务。
	任务的优先级：高，中和低。
	任务的执行时间：长，中和短。
	任务的依赖性：是否依赖其他系统资源，如数据库连接。
	
	任务性质不同的任务可以用不同规模的线程池分开处理。CPU密集型任务配置尽可能小的线程，如配置Ncpu+1个线程的线程池。IO密集型任务则由于线程并不是一直在执行任务，则配置尽可能多的线程，如2*Ncpu。混合型的任务，如果可以拆分，则将其拆分成一个CPU密集型任务和一个IO密集型任务，只要这两个任务执行的时间相差不是太大，那么分解后执行的吞吐率要高于串行执行的吞吐率，如果这两个任务执行时间相差太大，则没必要进行分解。我们可以通过Runtime.getRuntime().availableProcessors()方法获得当前设备的CPU个数。
	
	优先级不同的任务可以使用优先级队列PriorityBlockingQueue来处理。它可以让优先级高的任务先得到执行，需要注意的是如果一直有优先级高的任务提交到队列里，那么优先级低的任务可能永远不能执行。
	
	执行时间不同的任务可以交给不同规模的线程池来处理，或者也可以使用优先级队列，让执行时间短的任务先执行。
	
	依赖数据库连接池的任务，因为线程提交SQL后需要等待数据库返回结果，如果等待的时间越长CPU空闲时间就越长，那么线程数应该设置越大，这样才能更好的利用CPU。
	
	建议使用有界队列，有界队列能增加系统的稳定性和预警能力，可以根据需要设大一点，比如几千。有一次我们组使用的后台任务线程池的队列和线程池全满了，不断的抛出抛弃任务的异常，通过排查发现是数据库出现了问题，导致执行SQL变得非常缓慢，因为后台任务线程池里的任务全是需要向数据库查询和插入数据的，所以导致线程池里的工作线程全部阻塞住，任务积压在线程池里。如果当时我们设置成无界队列，线程池的队列就会越来越多，有可能会撑满内存，导致整个系统不可用，而不只是后台任务出现问题。当然我们的系统所有的任务是用的单独的服务器部署的，而我们使用不同规模的线程池跑不同类型的任务，但是出现这样问题时也会影响到其他任务。