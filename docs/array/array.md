#### 1.用过 ConcurrentHashMap，讲一下他和 HashTable 的不同之处？
+ HashTable 就是实现了 HashMap 加上了 synchronized，而 ConcurrentHashMap 底层
采用分段的数组+链表实现，线程安全
+ ConcurrentHashMap 通过把整个 Map 分为 N 个 Segment，可以提供相同的线程安
全，但是效率提升 N 倍，默认提升 16 倍。并且读操作不加锁，由于 HashEntry 的 value 变量是 volatile 的，也能保证读取到最新
的值。
+ Hashtable 的 synchronized 是针对整张 Hash 表的，即每次锁住整张表让线程独占，
ConcurrentHashMap 允许多个修改操作并发进行，其关键在于使用了锁分离技术
扩容：段内扩容（段内元素超过该段对应 Entry 数组长度的 75%触发扩容，不会对整个
Map 进行扩容），插入前检测需不需要扩容，有效避免无效扩容
#### 2.ArrayList 和 LinkedList 的区别在哪里?  
+ 数据结构实现：ArrayList ：基于数组，便于按 index 访问，超过数组需要扩容，扩容成
本较高。LinkedList：使用链表实现，无需扩容。
+ 随机访问效率：ArrayList 比 LinkedList 在随机访问的时候效率要高，因为 LinkedList 是
线性的数据存储方式，所以需要移动指针从前往后依次查找。
增加和删除效率：在非首尾的增删操作，LinkedList 要比 ArrayList 效率要高，因为
+ ArrayList 增删操作要影响数组内的其他数据的下标。
内存空间占用：LinkedList 比 ArrayList 更占内存，因为 LinkedList 的节点除了存储数
据，还存储了两个引用，一个指向前一个元素，一个指向后一个元素。
+ 线程安全：ArrayList 和 LinkList 都是不同步的，不保证线程安全。
+ 综合来说，需要频繁读取集合中的元素时，更推荐使用 Arrayist，而在增删操作较多时，
更推荐使用 LinkedList。
