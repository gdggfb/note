# 述
> 一个已经开发好的集合类总是能给开发过程中的某些需求的实现带来很多便利，但在很多时候在开发中我总是发现没有集合能满足当时的需求，在15年的代码中，我封装过一个类似于ArrayBlockingQueue的数据结构，给自己的线程提供一个弹性异步任务流，然而18年初，我才发现jdk已经提供了ArrayBlockingQueue，感觉创造java的先贤们可能把所有可能出现的场景的数据结构都已经设计好了，只是有很多都被雪藏在rt里，不被人发掘，所以我觉得：是时候对java的集合类做个总结了。

# HashMap
    最常用的数据结构，很完美，自动扩容让人又爱又怕，每次存不可控数量的数据都感觉自己在给系统埋了个炸弹。
# ArrayList
    最常用的数据结构，自动扩容应该是内存块拷贝，性能要比HashMap好。
# TreeMap
    单线程的红黑树结构，查找效率稳定，性能稳定，感觉以后可以多用TreeMap，少用HashMap。
# Hashtable
    多线程的HashMap，全局锁，我为什么不用ConcurrentHashMap。
# EnumMap
    key是枚举类型的，很有创意。枚举类型的元素个数是固定的，EnumMap内部直接用数组存储了数据，效率很高。
# IdentityHashMap
    key的值可以相同，但必须不是同一个对象，IdentityHashMap会提取key的系统hashcode来比较key是否相同，而且IdentityHashMap的结构还是很奇葩的，内部是一个objec数组，把一组key和value存在一个连续的位置上。
# LinkedHashMap
    继承至HashMap，新增了head和tail属性，为每个node新增了before和after属性，重写了afterNodeAccess和afterNodeRemoval方法，将每个节点串成逻辑双向链表，保证了节点插入的顺序。
# WeakHashMap
    弱键Map，感觉在某种场景中，该类可以发挥奇效，结构和HashMap差不多，key是存的是弱键
# EnumSet
    一个枚举的集合，和枚举类相比，EnumSet等于是多提供了一个boolean属性来判断是否存在，内部实现是使用位来判断是否存在。
# LinkedList
    单线程的双向链表，查找效率比较低，不用扩容，性能稳定，在不需要查找数据的情况下，LinkedList是不二的选择。
# Vector
    一个多线程的ArrayList，容量扩展比ArrayList快，然而用的是全局锁！
# Stack
    后进先出，多线程，全局锁，扩容数组。
# ArrayDeque
    一个很陌生的类，以前从未见过。单线程，数组，扩容，pop，posh，一个很自由的堆栈结构。
# BitSet
    如果把我之前的C项目改成java的，那这个类将被用来存储位图。操作一段字节里位值的类，单线程的。（我发现有很多在C里面很普通的东西，在java里面会被吹嘘成神一样。）
# Arrays
    一个操作数组的工具类，提供了对数组的查找，排序，赋值操作。
    https://www.cnblogs.com/HeDante/p/7464874.html
# Collections
    void reverse(List list)：反转
    void shuffle(List list),随机排序
    void sort(List list),按自然排序的升序排序
    void sort(List list, Comparator c);定制排序，由Comparator控制排序逻辑
    void swap(List list, int i , int j),交换两个索引位置的元素
    void rotate(List list, int distance),旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面。
    int binarySearch(List list, Object key), 对List进行二分查找，返回索引，注意List必须是有序的
    int max(Collection coll),根据元素的自然顺序，返回最大的元素。 类比int min(Collection coll)
    int max(Collection coll, Comparator c)，根据定制排序，返回最大元素，排序规则由Comparatator类控制。类比int min(Collection coll, Comparator c)
    void fill(List list, Object obj),用元素obj填充list中所有元素
    int frequency(Collection c, Object o)，统计元素出现次数
    int indexOfSubList(List list, List target), 统计targe在list中第一次出现的索引，找不到则返回-1，类比int lastIndexOfSubList(List source, list target).
    boolean replaceAll(List list, Object oldVal, Object newVal), 用新元素替换旧元素。
https://www.cnblogs.com/fysola/p/6021134.html

# StringTokenizer
Observable
Local
ListResourceBundle
Currency
DoubleSummaryStatistics
IntSummaryStatistics
GregorianCalendar
Locale
Optional
PriorityQueue
ResourceBundle
Scanner
ServiceLoader


ArrayBlockingQueue
CompletableFuture
ConcurrentHashMap
ConcurrentLinkedDeque
ConcurrentLinkedQueue
ConcurrentSkipListMap
ConcurrentSkipListSet
CopyOnWriteArrayList
CopyOnWriteArraySet
CountDownLatch
CountedCompleter
CyclicBarrier
DelayQueue
ExecutorCompletionService
ForkJoinPool
ForkJoinTask
ForkJoinWorkerThread
LinkedBlockingDeque
LinkedBlockingQueue
LinkedTransferQueue
PriorityBlockingQueue
RecursiveAction
RecursiveTask
SynchronousQueue
ThreadLocalRandom
ThreadPoolExecutor

