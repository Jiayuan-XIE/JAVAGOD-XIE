# 一、三大重点

## 1.基本结构

位桶+链表+红黑树



## 2. 分布策略

````
2.1  将哈希值的**高位参与运算**，减少碰撞

2.2 数组的长度始终保持为**2的次幂**

2.3 通过位与操作来等价取模操作
````



## 3.动态扩容

#### 扩容时机：

````
1. table容量达到阈值
2. 桶中链表的长度达到8，但是table 的长度未满64
````



#### 退化时机：

````
1. removeTreeNode()  //在红黑树的root节点为空 或者root的右节点、root的左节点、root左节点的左节点为空时 说明树都比较小了
2. resize()		   	 //扩容时，分离的两个树的节点个数小于等于6时。
````



#### 为啥是8再进行树化

因为节点分布在桶中的概率是符合**泊松分布**的，按照泊松分布的计算公式，计算出了桶中节点个数的概率值

一个桶中的节点个数达到8的概率不到**千万分之1，是亿分之6，**概率极小，所以选择8为树化阈值

红黑树虽然查询效率比链表高，但是结点占用的空间大（空间换时间），只有达到一定的数目才有树化的意义，这是**基于时间和空间的平衡考虑**。



#### 红黑树退化为链表的阈值为啥是6

主要是为了做一个过渡，避免频繁的树化和退化，频繁的转换必然会降低性能。





# 二、Map解析

## 1. 不同版本的实现结构

#### JDK1.8之后

- HashMap采用**位桶+链表+红黑树实现**(之前没有红黑树)，当链表长度达到阈值（8）时，将链表转换为红黑树，这样大大减少了查找时间。

  （将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）

- **先插入结点**，后判断是否树化（之前是先判断，后插入）

- **尾插法**插入新的结点（之前是头插法）

## 2.原理图

![img](https://img-blog.csdn.net/20160605101246837?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 加载因子（默认0.75）

加载因子（默认0.75）：为什么需要使用加载因子，为什么需要扩容呢？

因为如果填充比很大，说明利用的空间很多，如果一直不进行扩容的话，链表就会越来越长，这样查找的效率很低，因为链表的长度很大（当然最新版本使用了红黑树后会改进很多）

扩容之后，将原来链表数组的每一个链表分成奇偶两个子链表分别挂在新链表数组的散列位置，这样就减少了每个链表的长度，增加查找效率



## 3.各个参数

````java
public class HashMap<k,v> extends AbstractMap<k,v> implements Map<k,v>, Cloneable, Serializable {
    private static final long serialVersionUID = 362498820763181265L;
    
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; //*** 初始的容量16，每次扩容*2 ***
    
    static final int MAXIMUM_CAPACITY = 1 << 30;//最大容量
    
    static final float DEFAULT_LOAD_FACTOR = 0.75f;//填充比	
    //当put一个元素到某个位桶，其链表长度达到8时将链表转换为红黑树
    static final int TREEIFY_THRESHOLD = 8;
    //扩容resize时，分离的两个树的节点个数小于等于6时。
    static final int UNTREEIFY_THRESHOLD = 6;
    static final int MIN_TREEIFY_CAPACITY = 64;
    
    transient Node<k,v>[] table;//存储元素的数组***
    
    transient Set<map.entry<k,v>> entrySet;
    transient int size;//存放元素的个数
    transient int modCount;//被修改的次数fast-fail机制
    
    int threshold;//临界值 当实际大小(容量*填充比)超过临界值时，会进行扩容 ，随着容量一样的增加，是容量的0.75（默认）
    
    final float loadFactor;//填充比（......后面略）
````



## 4. map.put()步骤

````
过程：
1. 判断是否第一次添加元素，进行第一次扩容resize()。
2. 如果哈希没有碰撞，就直接搞个新节点放在table数组里
3. 如果碰撞了，判断是红黑树还是链表，分别去搜索相同key的节点
4. 处理完成后，看需不需要扩容
````

1. new HashMap();

   各项初始化

   注意：里面的table数组（用来存放链表头的数组）是空，当添加第一个元素的时候才会分配空间。

2. 开始put

   - 调用putVal()，并同时计算hash(key)	==>` (h = key.hashCode()) ^ (h >>> 16) `，这是一个扰乱函数，可以减少hash冲突

   ````java
   final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                      boolean evict) {
           Node<K,V>[] tab; Node<K,V> p; int n, i;
       	//第一次添加元素，进行第一次扩容
           if ((tab = table) == null || (n = tab.length) == 0)
               n = (tab = resize()).length;
       	//如果哈希没有碰撞，就直接搞个新节点放在table数组里，注意这里的碰撞hash是和table的长度有关
           if ((p = tab[i = (n - 1) & hash]) == null)
               tab[i] = newNode(hash, key, value, null);
           else {
             //否则开始哈希碰撞
               Node<K,V> e; K k;
               //直接相等，待定
               if (p.hash == hash &&
                   ((k = p.key) == key || (key != null && key.equals(k))))
                   e = p;
               else if (p instanceof TreeNode)	//看看此链表是不是红黑树结构
                   e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
               else {
                   //如果是普通的链表，就开始遍历链表
                   for (int binCount = 0; ; ++binCount) {
                       //找到链表尾巴了，都没找到，搞个新的节点挂后边
                       if ((e = p.next) == null) {
                           p.next = newNode(hash, key, value, null);
                           //一旦达到了8个，就开始红黑树化（其实treeifyBin方法里还要判断一下table的大小是否达到了64）
                           if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                               treeifyBin(tab, hash);
                           break;
                       }
                       //中途找到了
                       if (e.hash == hash &&
                           ((k = e.key) == key || (key != null && key.equals(k))))
                           break;
                       p = e;
                   }
               }
               //找到了同一个key了，替换value;
               if (e != null) { // existing mapping for key
                   V oldValue = e.value;
                   if (!onlyIfAbsent || oldValue == null)
                       e.value = value;
                   afterNodeAccess(e);
                   return oldValue;
               }
           }
           ++modCount;
       	//size达到阈值了，扩容
           if (++size > threshold)
               resize();
           afterNodeInsertion(evict);
           return null;
       }
   ````

   

   #### 扩容

   ````
   步骤：
   1. 设置新的容量值和阈值
   2. 创建 一个新的table，并更新table
   3. 遍历老table的元素，把原来的元素重新hash碰撞，并把链表分为奇偶两条链表，分别插入到新的table中（中间隔了oldCap
   ````

   ````java
   /**
   两种情况扩容：
   - 1. table里的元素个数达到阈值（容量 * 加载因子）
   - 2. 某个链表超过了8个，且table 的size不超过64, （超过64就直接进行红黑树化）
   */
   
   final Node<K,V>[] resize() {
           Node<K,V>[] oldTab = table;
           int oldCap = (oldTab == null) ? 0 : oldTab.length;
           int oldThr = threshold;
           int newCap, newThr = 0;
       	//设置newCap，newThr
           if (oldCap > 0) {
               if (oldCap >= MAXIMUM_CAPACITY) {
                   threshold = Integer.MAX_VALUE;
                   return oldTab;
               }
               else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                        oldCap >= DEFAULT_INITIAL_CAPACITY)
                   newThr = oldThr << 1; // double threshold
           }
           else if (oldThr > 0) // initial capacity was placed in threshold
               newCap = oldThr;
           else {               // zero initial threshold signifies using defaults
               newCap = DEFAULT_INITIAL_CAPACITY;
               newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
           }
           if (newThr == 0) {
               float ft = (float)newCap * loadFactor;
               newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                         (int)ft : Integer.MAX_VALUE);
           }
           threshold = newThr;
           @SuppressWarnings({"rawtypes","unchecked"})
               Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
           table = newTab;
       	//处理扩容后table
           if (oldTab != null) {
               for (int j = 0; j < oldCap; ++j) {
                   Node<K,V> e;
                   if ((e = oldTab[j]) != null) {
                       oldTab[j] = null;
                       if (e.next == null)
                           //就一个，直接重新hash放进去
                           newTab[e.hash & (newCap - 1)] = e;
                       else if (e instanceof TreeNode)
                           //红黑树的处理法
                           ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                       else { // preserve order
                           //链表，且不止一个节点，开始遍历上链
                           //把单链表分成两条链，挂在扩容后的table上，要么是老位置，要不是老位置+oldCap的位置
                           Node<K,V> loHead = null, loTail = null;
                           Node<K,V> hiHead = null, hiTail = null;
                           Node<K,V> next;
                           do {
                               next = e.next;
                               //把结点放在合适的位置
                               if ((e.hash & oldCap) == 0) {	
                                   //就是判断高位有没有影响，没有影响（==0）就和原来的算法一样，放在老的地方，如果有影响，就差了一个oldCap的大小
                                   //实际上与直接做  (e.hash & (newCap - 1)) == j  的效果一样
                                   if (loTail == null)
                                       loHead = e;
                                   else
                                       loTail.next = e;
                                   loTail = e;
                               }
                               else {
                                   if (hiTail == null)
                                       hiHead = e;
                                   else
                                       hiTail.next = e;
                                   hiTail = e;
                               }
                           } while ((e = next) != null);
                           if (loTail != null) {
                               loTail.next = null;
                               newTab[j] = loHead;
                           }
                           if (hiTail != null) {
                               hiTail.next = null;
                               newTab[j + oldCap] = hiHead;
                           }
                       }
                   }
               }
           }
           return newTab;
       }
   ````

   

   #### 注意

   `(e.hash & oldCap)`操作很巧妙的把链表分为两类

   实际上分为两类的结果，与直接做`(e.hash & (newCap - 1)) == j`的效果一样

   

# 三、几点思考

## 1.transient的作用？

防止该属性被序列化，序列化再反序列化后，该属性会消失,

1. 效率问题

   因为table表有很多空桶，没有必要存储，所以只存有元素的桶里的数据就行了，在序列化的时候会调用writeObject()，这里面将集合中的元素一个个的写入到文件中。

2. 跨平台问题

   不同平台的HashCode可能不太一样，因为hashcode()是native方法，而我们的下标又是通过hashcode来获取的，所以用transient 来修饰不希望序列化的属性，比如size。



## 2.hash() 扰乱函数

为了让hashcode 的高十六位也参与运算，使得信息混合，能够减少碰撞，从而使元素分配的更加均匀。



## 3.为啥扩容总是2的幂次方？

主要是为了优化获取元素在table中下标的操作

一般获取下标方法，是取模运算，但是这个运算是**十分的笨重的**，它需要先转换成十进制，再进行运算。

而我们table的长度是2的幂次方，我们就可以用**位与运算**，来**代替取模运算**，从而提高效率。



## 4.rehash()的特点

由于我们扩容后的容量，总是旧容量的2倍，也就是说，新的容量的二进制表示，会在高位多出1bit。

那旧的node，要么就在老位置，要么就在加上oldCap长度的位置。



