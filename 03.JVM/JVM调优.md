# JVM调优

## 1. 目的

对JVM内存的系统级的调优主要的目的是减少**GC的频率**和**Full GC的次数。**



### 1.1 FUll GC

会对整个堆进行整理，包括Young、Tenured和Perm。Full GC因为需要对整个堆进行回收，所以比较慢，因此应该尽可能减少Full GC的次数。



### 1.2 导致Full GC的原因

1) **年老代（Tenured）被写满**

调优时尽量让对象在新生代GC时被回收、让对象在新生代多存活一段时间和不要创建过大的对象及数组避免直接在旧生代创建对象 。

2) **持久代Pemanet Generation空间不足**

增大Perm Gen空间，避免太多静态对象 ， 控制好新生代和旧生代的比例

3) **System.gc()被显示调用**

垃圾回收不要手动触发，尽量依靠JVM自身的机制

在对JVM调优的过程中，很大一部分工作就是对于FullGC的调节，下面详细介绍对应JVM调优的方法和步骤。



## 2. 调优参数

````
-Xms JVM初始堆大小，默认为物理内存的1/64;
-Xmx JVM可申请的堆内存最大值，默认为物理内存的1/4;
-Xmn JVM新生代大小;
	-XX:SurvivorRation 调整Eden和SuvivorSpace的大小
-Xss JVM每个线程栈的大小，默认为1M;
-XX:PermSize 方法区（元空间）初始内存大小;
-XX:MaxPermSize 方法区（元空间）最大内存大小;
````





![image-20210903164125436](http://note.youdao.com/yws/public/resource/7d81e6a39024a96dd86efacf29f4ca80/xmlnote/WEBRESOURCE67299b8562d44b4cafced6f12d3741ad/2014)
