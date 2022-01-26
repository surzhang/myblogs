# java基础问题
#### 1.java8有哪些新特性？
+ Lambda 表达式，也可称为闭包，它是推动 Java 8 发布的最重要新特性。Lambda 允许把函数作为一个方法的参数（函数作为参数传递进方法中）。使用Lambda 表达式可以使代码变的更加简洁紧凑。 
+ 方法引用通过方法的名字来指向一个方法。方法引用可以使语言的构造更紧凑简洁，减少冗余代码。方法引用使用一对冒号 :: 。
+ 函数式接口(FunctionalInterface)就是一个有且仅有一个抽象方法，但是可以有多个非抽象方法的接口。函数式接口可以被隐式转换为lambda表达式。函数式接口可以现有的函数友好地支持 lambda。
+ Java 8 新增了接口的默认方法。简单说，默认方法就是接口可以有实现方法，而且不需要实现类去实现其方法。我们只需在方法名前面加个default关键字即可实现默认方法。
+ Java 8 API添加了一个新的抽象称为流Stream，可以让你以一种声明的方式处理数据。Stream使用一种类似用SQL语句从数据库查询数据的直观方式来提供一种对Java集合运算和表达的高阶抽象。Stream API可以极大提高Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。
+ Optional 类是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true，调用get()方法会返回该对象。Optional 是个容器：它可以保存类型T的值，或者仅仅保存null。Optional提供很多有用的方法，这样我们就不用显式进行空值检测。Optional 类的引入很好的解决空指针异常。
+ jjs是个基于Nashorn引擎的命令行工具。它接受一些JavaScript源代码为参数，并且执行这些源代码。
+ Java 8通过发布新的Date-Time API (JSR 310)来进一步加强对日期与时间的处理。
+ 在Java8中，Base64编码已经成为Java类库的标准。Java 8 内置了 Base64 编码的编码器和解码器。  
  
#### 2.==和equals的区别？  
== : 它的作用是判断两个对象的地址是不是相等。即，判断两个对象是不是同⼀个对象(基本数据
类型==比较的是值，引用数据类型==比较的是内存地址)。  
equals() : 它的作用也是判断两个对象是否相等。但它一般有两种使用情况：  
+ 情况 1：类没有覆盖 equals() 方法。则通过 equals() 比较该类的两个对象时，等价于通过
“==”比较这两个对象。  
+ 情况 2：类覆盖了 equals() 方法。一般，我们都覆盖 equals() 方法来比较两个对象的内容是
否相等；若它们的内容相等，则返回 true (即，认为这两个对象相等)。  

```java
public class test1 {
    public static void main(String[] args) {
        String a = new String("ab"); // a 为一个引用
        String b = new String("ab"); // b为另一个引用,对象的内容一样
        String aa = "ab"; // 放在常量池中
        String bb = "ab"; // 从常量池中查找
        if (aa == bb) // true
            System.out.println("aa==bb");
        if (a == b) // false，非同一对象
            System.out.println("a==b");
        if (a.equals(b)) // true
            System.out.println("aEQb");
        if (42 == 42.0) { // true
            System.out.println("true");
        }
    }
}
```

+ String 中的 equals 方法是被重写过的，因为 object 的 equals 方法是比较的对象的内存地
址，而 String 的 equals 方法比较的是对象的值。
+ 当创建 String 类型的对象时，虚拟机会在常量池中查找有没有已经存在的值和要创建的值相
同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个 String 对象。  

#### 3.String、StringBuilder、StringBuffer的区别？ 
String 类中使用 final 关键字修饰字符数组来保存字符串， private final char
value[] ，所以 String 对象是不可变的。  
在 Java 9 之后，String 类的实现改用 byte 数组存储字符串
private final byte[] value  
- 而 StringBuilder 与 StringBuffer 都继承自 AbstractStringBuilder 类，在 AbstractStringBuilder 中
也是使用字符数组保存字符串 char[]value 但是没有用 final 关键字修饰，所以这两种对象都是可
变的。  

```java
public abstract class AbstractStringBuilder implements Appendable, CharSequence {
/**
* The value is used for character storage.
*/
char[] value;
/**
* The count is the number of characters used.
*/
int count;
AbstractStringBuilder(int capacity) {
value = new char[capacity];
}
```


- String 中的对象是不可变的，也就可以理解为常量，线程安全。AbstractStringBuilder 是
StringBuilder 与 StringBuffer 的公共父类，定义了一些字符串的基本操作，expandCapacity、
append、insert、indexOf 等公共方法。StringBuffer 对方法加了`同步锁`或者对调用的方法加了同 步锁，所以是线程安全的。StringBuilder 并没有对方法进行加同步锁，所以是非线程安全的。  

- 每次对 String 类型进行改变的时候，都会生成一个新的 String 对象，然后将指针指向新的 String
  对象。StringBuffer 每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象并改变对象
  引用。相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提
  升，但却要冒多线程不安全的⻛险。  
  

**对于三者使用的总结：**
1. 操作少量的数据: 适用 String
2. 单线程操作字符串缓冲区下操作大量数据: 适用 StringBuilder
3. 多线程操作字符串缓冲区下操作大量数据: 适用 StringBuffer

#### 4.final关键字的作用？  
 ① 可以用来修饰一个类  
 ```java
public final class 类名称 {
		...
	}
 ```
 作用：使当前这个类不能有任何子类。（“太监类”）
 注意：一个类如果是final的，那么其中所有的成员方法都无法进行覆盖重写  
 ② 可以用来修饰一个方法  
 ```java
	修饰符 final 返回值类型 方法名称(参数列表) {
		方法体
	}
 ```
 作用：当final关键字用来修饰一个方法的时候，这个方法就是最终方法，不能够被覆盖重写
 注意：对于类、方法来说，abstract关键字和final关键字不能同时使用，因为作用相互矛盾  
 ③ 可以用来修饰一个局部变量  
 ```java
	// ① 第一种基本数据类型情况下的格式
	final 基本数据类型 数据名称 = 值;
	// ② 引用数据类型情况下的格式
	final 类型 对象名 = new 类名();
	//例如：final Student stu = new Student();
 ```
 作用：当final关键字用于修饰局部变量的时候，这个局部变量就不能更改，“一次赋值，终生不变”
 注意：对于 基本类型 来说，不可改变指的是变量当中的数据不可改变，但是对于 引用类型 来说，不可改变的指的是变量当中的地址值不可改变  
 ④ 可以用来修饰一个成员变量  
 对于成员变量来说，如果使用了final关键字修饰，那么这个变量也照样是不可变的  
 + 由于成员变量具有默认值，所以用了final之后必须手动赋值，不会再给默认值了  
 + 对于final的成员变量，要么使用直接赋值，要么通过构造方法赋值，只能二选一  
 + 必须保证类当中所有重载的构造方法，都最终会对final的成员变量进行赋值    

#### 5.hashmap和hashtable的区别？ 
1. 线程是否安全： HashMap 是非线程安全的， HashTable 是线程安全的,因为 HashTable 内
部的方法基本都经过 synchronized 修饰。（如果你要保证线程安全的话就使用
ConcurrentHashMap 吧！）；
2. 效率： 因为线程安全的问题， HashMap 要比 HashTable 效率高一点。另外， HashTable
基本被淘汰，不要在代码中使用它；
3. 对 Null key 和 Null value 的支持： HashMap 可以存储 null 的 key 和 value，但 null 作为
键只能有一个，null 作为值可以有多个；HashTable 不允许有 null 键和 null 值，否则会抛出
NullPointerException 。
4. 初始容量大小和每次扩充容量大小的不同 ： ① 创建时如果不指定容量初始值， Hashtable
默认的初始大小为 11，之后每次扩充，容量变为原来的 2n+1。 HashMap 默认的初始化大
小为 16。之后每次扩充，容量变为原来的 2 倍。② 创建时如果给定了容量初始值，那么
Hashtable 会直接使用你给定的大小，而 HashMap 会将其扩充为 2 的幂次方大小
（ HashMap 中的 tableSizeFor() 方法保证，下面给出了源代码）。也就是说 HashMap 总
是使用 2 的幂作为哈希表的大小,后面会介绍到为什么是 2 的幂次方。
5. 底层数据结构： JDK1.8 以后的 HashMap 在解决哈希冲突时有了较大的变化，当链表⻓度
大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的⻓度小于 64，那么
会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时
间。Hashtable 没有这样的机制。  

#### 6.ConcurrentHashMap 和 Hashtable 的区别?
`ConcurrentHashMap` 和 `Hashtable` 的区别主要体现在实现线程安全的方式上不同。
+ 底层数据结构： JDK1.7 的 ConcurrentHashMap 底层采用 分段的数组+链表 实现，JDK1.8
采用的数据结构跟 HashMap1.8 的结构一样，数组+链表/红黑二叉树。 Hashtable 和
JDK1.8 之前的 HashMap 的底层数据结构类似都是采用 数组+链表 的形式，数组是
HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；
+ 实现线程安全的方式（重要）： ① 在 JDK1.7 的时候， ConcurrentHashMap （分段锁）
对整个桶数组进行了分割分段( Segment )，每一把锁只锁容器其中一部分数据，多线程访问
容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。 到了 JDK1.8 的时候已经
摒弃了 Segment 的概念，而是直接用 Node 数组+链表+红黑树的数据结构来实现，并发
控制使用 synchronized 和 CAS 来操作。（JDK1.6 以后 对 synchronized 锁做了很多优
化） 整个看起来就像是优化过且线程安全的 HashMap ，虽然在 JDK1.8 中还能看到
Segment 的数据结构，但是已经简化了属性，只是为了兼容旧版本；② Hashtable (同一把
锁) :使用 synchronized 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其
他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不
能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。  

#### 7.map和flatmap的区别？
map把数组流中的每一个值，使用所提供的函数执行一遍，一一对应。得到元素个数相同的数组流。  
flat是扁平的意思。它把数组流中的每一个值，使用所提供的函数执行一遍，一一对应。得到元素相同的数组流。只不过，里面的元素也是一个子数组流。把这些子数组合并成一个数组以后，元素个数大概率会和原数组流的个数不同。  
*举例说明：*  
有二箱鸡蛋，每箱5个，现在要把鸡蛋加工成煎蛋，然后分给学生。  
map做的事情：把二箱鸡蛋分别加工成煎蛋，还是放成原来的两箱，分给2组学生；  
flatMap做的事情：把二箱鸡蛋分别加工成煎蛋，然后放到一起【10个煎蛋】，分给10个学生；  ‘
#### 8.Java中实现定时任务的方式？  
>java基本的定时任务，有三种方式：  

+ 创建一个thread，然后让它在while循环里一直运行，通过sleep方法来达到定时任务的效果。
```java
    public void test1() {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                while (true) {
                    System.out.println("hello");
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        };
        Thread thread=new Thread(runnable);
        thread.start();
    }
```
+ 用Timer和TimerTask与第一种方法相比有如下好处：
    1. 当启动和去取消任务时可以控制
    2. 第一次执行任务时可以指定你想要的delay时间  
```java
 public void test2() {
        TimerTask task =  new TimerTask() {
            @Override
            public void run() {
                System.out.println("hello");
            }
        };

       Timer timer = new Timer();
       long delay = 0;
       long intervalPeriod  = 1* 1000;
       timer.scheduleAtFixedRate(task,delay,intervalPeriod);
    }
```
+  用ScheduledExecutorService是从的java.util.concurrent里，做为并发工具类被引进的，这是最理想的定时任务实现方式，相比于上两个方法，它有以下好处：  
    1. 相比于Timer的单线程，它是通过线程池的方式来执行任务的  
    2. 可以很灵活的去设定第一次执行任务delay时间  
    3. 提供了良好的约定，以便设定执行的时间间隔  
```java
 public void test3() {
        Runnable runnable=new Runnable() {
            @Override
            public void run() {
                System.out.println("hello");
            }
        };
        /**
         * command：执行线程
         * initialDelay：初始化延时
         * period：两次开始执行最小间隔时间
         * unit：计时单位
         */
        ScheduledExecutorService scheduledExecutorService= Executors.newSingleThreadScheduledExecutor();
        scheduledExecutorService.scheduleAtFixedRate(runnable,0,1, TimeUnit.SECONDS);
    }
```
>spring的定时任务也有三种  

[spring定时任务](https://blog.csdn.net/lchq1995/article/details/78222528)  
[springboot定时任务](https://www.cnblogs.com/lenve/p/10728897.html)  
在 SpringBoot 中使用定时任务主要有两种不同的方式，一个就是使用 Spring 中的
@Scheduled 注解，另一个则是使用第三方框架 Quartz。
使用 Spring 中的 @Scheduled 的方式主要通过 @Scheduled 注解来实现。
使用 Quartz ，则按照 Quartz 的方式，定义 Job 和 Trigger 即可。
#### 9.如何保证线程安全？
#### 10.常用的设计模式？
#### 11.进程和线程？
操作系统中可以拥有多个进程，一个进程里可以拥有多个线程，线程在进程内执行  
进程和线程的区别  
+ 容易创建新线程。创建新进程需要重复父进程
+ 线程可以控制同一进程的其他线程。进程无法控制兄弟进程，只能控制其子进程
+ 进程拥有自己的内存空间。线程使用进程的内存空间，且要和该进程的其他线程共享这个空间；而不是在进程中给每个线程单独划分一点空间。
+ （同一进程中的）线程在共享内存空间中运行，而进程在不同的内存空间中运行
+ 线程可以使用 wait（），notify（），notifyAll（）等方法直接与其他线程（同一进程）
通信；而，进程需要使用“进程间通信”（IPC）来与操作系统中的其他进程通信。
#### 12.抽象类和接口的区别？
#### 13，AQS和CAS？