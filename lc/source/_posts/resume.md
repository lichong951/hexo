---
title: 面试总结记录
date: 2017-04-27 20:10:01
tags: android 面试
---
## 1、 Android性能分析工具
	
	1.1 TraceView:
		最简单的方式就是直接打开DDMS，选择一个进程，然后按上面的“Start Method Profiling”按钮，
		第2种方式就是使用android.os.Debug.startMethodTracing();和android.os.Debug.stopMethodTracing();方法
		重要指标 Calls+Recur Calls／Total（方法执行的次数）；Cpu Time／Call（方法耗时）
	1.2 Eclipse Memory Analyzer Tool 内存使用
	
	1.3 Dump UI Hierarchy for UI Atomator 分析UI层级
	
	1.4 systrace
		Systrace是Android4.1中新增的性能数据采样和分析工具。它可帮助开发者收集Android关键子系统（如surfaceflinger、WindowManagerService等Framework部分关键模块、服务）的运行信息，从而帮助开发者更直观的分析系统瓶颈，改进性能。
		Systrace的功能包括跟踪系统的I/O操作、内核工作队列、CPU负载以及Android各个子系统的运行状况等。在Android平台中，它主要由3部分组成：
		内核部分：Systrace利用了Linux Kernel中的ftrace功能。所以，如果要使用Systrace的话，必须开启kernel中和ftrace相关的模块。
		数据采集部分：Android定义了一个Trace类。应用程序可利用该类把统计信息输出给ftrace。同时，Android还有一个atrace程序，它可以从ftrace中读取统计信息然后交给数据分析工具来处理。
		数据分析工具：Android提供一个systrace.py（python脚本文件，位于Android SDK目录/tools/systrace中，其内部将调用atrace程序）用来配置数据采集的方式（如采集数据的标签、输出文件名等）和收集ftrace统计数据并生成一个结果网页文件供用户查看。
	
	1.5 Oprofile：性能数据采集和分析工具
	
	1.6 Hierarchy Viewer（层级查看器）
	
参考：*http://www.jianshu.com/p/da2a4bfcba68*

	1.7 Battery Historian 
		Google出品, 通过Android系统的bugreport文件来做电量使用分析的工具
		https://github.com/google/battery-historian
	1.8 NetEase/Emmagee
		针对Android App的CPU, 内存, 网络, 电量等多项综合的测试分析.
		https://github.com/NetEase/Emmagee
		
	1.9 Square
		Square出品, 必属精品.
		类似与App探针的内存泄露监测工具.
		https://github.com/square/leakcanary
		
	1.10 AndroidDevMetrics
		一个library, 用来检测Activity生命周期执行性能, Dagger2注入性能以及帧率性能的工具
		https://github.com/frogermcs/AndroidDevMetrics

2、 Android内存优化方案

	2.1 Android应用String/StringBuilder/StringBuffer优化建议
	字符串操作在Android应用开发中是十分常见的操作，也就是这个最简单的字符串操作却也暗藏很多潜在的性能问题，下面我们实例来说说。
	先看下面这个关于String和StringBuffer的对比例子：
	//性能差的实现
	String str1 = "Name:";
	String str2 = "GJRS";
	String Str = str1 + str2;
	//性能好的实现
	String str1 = "Name:";
	String str2 = "GJRS";
	StringBuffer str = new StringBuilder().append(str1).append(str2);
	
	通过这个例子可以看出来，String对象（记得是对象，不是常量）和StringBuffer对象的主要性能区别在于String对象是不可变的，所以每次对String对象做改变操作（譬如“+”操作）时其实都生成了新的String对象实例，所以会导致内存消耗性能问题；而StringBuffer对象做改变操作每次都会对自己进行操作，所以不需要消耗额外的内存空间。
	
	我们再看一个关于String和StringBuffer的对比例子：
	//性能差的实现
	StringBuffer str = new StringBuilder().append("Name:").append("GJRS");
	//性能好的实现
	String Str = "Name:" + "GJRS";
	在这种情况下你会发现StringBuffer的性能反而没有String的好，原因是在JVM解释时认为 
	String Str = "Name:" + "GJRS";就是String Str = "Name:GJRS";，所以自然比StringBuffer快了。
	可以发现，如果我们拼接的是字符串常量则String效率比StringBuffer高，如果拼接的是字符串对象，则StringBuffer比String效率高，我们在开发中要酌情选择。当然，除过注意StringBuffer和String的效率问题，我们还应该注意另一个问题，那就是StringBuffer和StringBuilder的区别，其实StringBuffer和StringBuilder都继承自同一个父类，只是StringBuffer是线程安全的，也就是说在不考虑多线程情况下StringBuilder的性能又比StringBuffer高。
	PS：如果想追究清楚他们之间具体细节差异，麻烦自己查看实现源码即可

	2.2 Android应用HashMap与ArrayMap及SparseArray优化建议
	在Android开发中涉及到数据逻辑部分大部分用的都是Java的API（譬如HashMap），但是对于Android设备来说有些Java的API并不适合，可能会导致系统性能下降，好在Google团队已经意识到这些问题，所以他们针对Android设备对Java的一些API进行了优化，优化最多就是使用了ArrayMap及SparseArray替代HashMap来获得性能提升。
	
	HashMap：
	HashMap内部使用一个默认容量为16的数组来存储数据，数组中每一个元素存放一个链表的头结点，其实整个HashMap内部结构就是一个哈希表的拉链结构。HashMap默认实现的扩容是以2倍增加，且获取一个节点采用了遍历法，所以相对来说无论从内存消耗还是节点查找上都是十分昂贵的。
	
	SparseArray：
	SparseArray比HashMap省内存是因为它避免了对Key进行自动装箱（int转Integer），它内部是用两个数组来进行数据存储的（一个存Key，一个存Value），它内部对数据采用了压缩方式来表示稀疏数组数据，从而节约内存空间，而且其查找节点的实现采用了二分法，很明显可以看见性能的提升。
	
	ArrayMap：
	ArrayMap内部使用两个数组进行数据存储，一个记录Key的Hash值，一个记录Value值，它和SparseArray类似，也会在查找时对Key采用二分法。
	有了上面的基本了解我们可以得出结论供开发时参考，当数据量不大（千位级内）且Key为int类型时使用SparseArray替换HashMap效率高；当数据量不大（千位级内）且数据类型为Map类型时使用ArrayMap替换HashMap效率高；其他情况下HashMap效率相对高于二者
	
	2.3 Android应用ContentProviderOperation优化建议
	ContentProvider是Android应用开发的核心组件之一，有时候在开发中需要使用ContentProvider对多行数据进行操作，我们的做法一般是多次调运相关操作方法，殊不知这种实现方式是非常低性能的，取而代之的做法应该是使用批量操作，具体为了使批量更新、插入、删除数据操作更加方便官方提供了ContentProviderOperation工具类。所以在我们开发中遇到类似情景时请务必使用批量操作，具体的优势如下：
	所有的操作都在一个事务中执行，可以保证数据的完整性。
	批量操作在一个事务中执行，所以只用打开、关闭一个事务。
	减轻应用程序与ContentProvider间的多次频繁交互，提升性能。
	可以看见，这对于数据库操作来说是一个非常有用的优化措施，烦请务必重视（我们项目优化过，的确有很大提升）

	2.4 其他逻辑优化
	避免在Android中使用Java的枚举类型，因为编译后不但占空间，加载也费时，完全没有static final的变量好用、高效。

	Handler发送消息时尽量使用obtain去获取已经存在的Message对象进行复用，而不是新new Message对象，这样可以减轻内存压力。
	
	在使用后台Service时尽量将能够替换为IntentService的地方替换为此，这样可以减轻系统压力、省电、省内存、省CPU占用率。
	
	在当前类内部尽量不要通过自己的getXXX、setXXX对自己内部成员进行操作，而是直接使用，这样可以提高代码执行效率。
	
	不要一味的为了设计模式而过分的抽象代码，因为代码抽象系数与代码加载执行时间成正比。
	
	尽量减少锁个数、减小锁范围，避免造成性能问题。
	
	合理的选择使用for循环与增强型for循环，譬如不要在ArrayList上使用增强型for循环等。
3、 Android UI性能优化方案

4、 设计模式

5、 Android网络访问框架选型和优化
	
	5.1 volley
	5.2 retrofit
	5.3 andrid-async-http
	
	

6、 Bug解决系统：Bugly；BugTags；GT；iTest；Emmagee；

7、 Android应用移动设备电池耗电性能分析

有了UI性能优化、内存性能优化、代码编写优化之后我们在来说说应用开发中很重要的一个优化模块—–电量优化。

7.1 Android应用耗电量概念

在盒子等开发时可能电量优化不是特别重视（视盒子待机真假待机模式而定），但是在移动设备开发中耗电量是一个非常重要的指标，如果用户一旦发现我们的应用非常耗电，不好意思，他们大多会选择卸载来解决此类问题，所以耗电量是一个十分重要的问题。

关于我们应用的耗电量情况我们可以进行定长时间测试，至于具体的耗电量统计等请参考此文，同时我们还可以直接通过Battery Historian Tool来查看详细的应用电量消耗情况。最简单常用办法是通过命令直接查看，如下：

adb shell dumpsys batterystats

其实我们一款应用耗电量最大的部分不是UI绘制显示等，常见耗电量最大原因基本都是因为网络数据交互、GPS定位、大量内存性能问题、冗余的后台线程和Service等造成。

7.2 Android应用耗电量优化建议

优化电量使用情况我们不仅可以使用系统提供的一些API去处理，还可以在平时编写代码时就养成好的习惯。具体的一些建议如下：

在需要网络的应用中，执行某些操作前尽量先进行网络状态判断。

在网络应用传输中使用高效率的数据格式和解析方法，譬如JSON等。

在传输用户反馈或者下载OTA升级包等不是十分紧急的操作时尽量采用压缩数据进行传输且延迟到设备充电和WIFI状态时进行。

在有必要的情况下尽量通过PowerManager.WakeLock和JobScheduler来控制一些逻辑操作达到省电优化。

对定位要求不太高的场景尽量使用网络定位，而不是GPS定位。

对于定时任务尽量使用AlarmManager，而不是sleep或者Timer进行管理。

尽可能的减少网络请求次数和减小网络请求时间间隔。

后台任务要尽可能少的唤醒CPU，譬如IM通信的长连接心跳时间间隔、一些应用的后台定时唤醒时间间隔等要设计合理。

特殊耗电业务情况可以进行弹窗等友好的交互设计提醒用户该操作会耗用过多电量。

可以看见，上面只是一些常见的电量消耗优化建议。总之，作为应用开发者的我们要意识到电量损耗对于用户来说是非常敏感的，只有我们做到合理的电量优化才能赢得用户的芳心。