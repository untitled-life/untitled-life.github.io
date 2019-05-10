---
layout: post
title: "Java虚拟机常用参数"
date: 2019-05-10 12:34:21 +0800
comments: true
categories: JVM Java
---
> 孩子，我要求你读书用功，不是因为我要你跟别人比成绩，而是因为，我希望你将来会拥有选择的权利，选择有意义、有时间的工作，而不是被迫谋生。当你的工作在你心中有意义，你就有成就感。当你的工作给你时间，不剥夺你的生活，你就有尊严。成就感和尊严，给你快乐。 --龙应台 《亲爱的安德烈》

<!-- more -->
虽然JVM帮我们管理着内存(内存分配和垃圾回收)，但是JVM不是那么完美，很多时候还是会出现内存泄露和溢出等问题,当这些情况发生时，首先是排查定位问题，然后进行解决。在解决问题时，经常会使用JVM参数来设置JVM的属性，下面我们列举了常用的JVM参数，方便大家理解和使用。

**-Xms20M**

设置JVM启动内存的最小值为20M。

**-Xmx20M**

设置JVM启动内存的最大值为20M，如果将-Xmx和-Xms的值设置为一样，可以避免JVM内存自动扩展。

**-verbose:gc**

输出虚拟机中GC的详细情况

**-Xss128k**

设置虚拟机栈的大小为128k

**-Xoss128k**

设置本地方法栈的大小为128k。不过HotSpot并不区分虚拟机栈和本地方法栈，因此对于HotSpot来说这个参数是无效的

**-XX:PermSize=10M**

设置JVM初始分配的永久代的容量，仅限Java7及之前版本

**-XX:MaxPermSize=10M**

设置JVM允许分配的永久代的最大容量，仅限Java7及之前版本

**-Xnoclassgc**

关闭JVM对类的垃圾回收

**-XX:+TraceClassLoading**

查看类的加载信息

**-XX:+TraceClassUnLoading**

查看类的卸载信息

**-XX:NewRatio=4**

设置年轻代：老年代的大小比值为1：4，这意味着年轻代占整个堆的1/5

**-XX:SurvivorRatio=8**

设置2个Survivor区：1个Eden区的大小比值为2:8，这意味着Survivor区占整个年轻代的1/5，这个参数默认为8

**-Xmn20M**

设置年轻代的大小为20M

**-XX:+HeapDumpOnOutOfMemoryError**

表示可以让虚拟机在出现内存溢出异常时Dump出当前的堆内存转储快照

**-XX:+UseG1GC**

设置JVM使用G1垃圾收集器

**-XX:+PrintGCDetails**

表示在控制台上打印出GC具体细节

**-XX:+PrintGC**

表示在控制台上打印出GC信息

**-XX:PretenureSizeThreshold=3145728**

表示对象大于3145728（3M）时直接进入老年代分配，这里只能以字节作为单位

**-XX:MaxTenuringThreshold=1**

表示对象年龄大于1，自动进入老年代,默认值是15

**-XX:CompileThreshold=1000**

表示一个方法被调用1000次之后，会被认为是热点代码，并触发即时编译

**-XX:+PrintHeapAtGC**

表示可以看到每次GC前后堆内存布局

**-XX:+PrintTLAB**

表示可以看到TLAB的使用情况

**-XX:+UseSpining**

开启自旋锁

**-XX:PreBlockSpin**

更改自旋锁的自旋次数，使用这个参数必须先开启自旋锁
