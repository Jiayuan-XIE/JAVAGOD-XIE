## 1. 由来

因为HashTable的方法，用了大量的synchronized关键字修饰方法，并发性极差，理论上在某一时刻只有一个线程能访问该哈希表，已经不推荐使用了。

引入了ConcurrentHashMap



## 2.发展

1.7的ConcurrentHashMap 采用分段锁的形式，来提高并发量，默认一个哈希表有16个segment，也就是说

ConcurrentHashMap 默认支持最多 16 个线程并发。



1.8之后的ConcurrentHashMap 是**Node 数组 + 链表 / 红黑树**，和HashMap结构类似了。





## 3. 实现

初始化是通过**自旋和 CAS** 操作完成的

put操作也会在插入链表的逻辑上，用synchronized代码块来保证线程安全。

由于在JDK1.6之后，synchronized做了优化，所以put操作的效率还是可以保证的。

