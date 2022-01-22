# 多线程  
#### 1. 线程安全问题了解吗？怎么解决的？  
[线程安全问题](https://zhuanlan.zhihu.com/p/65105641)  

#### 2. 什么情况下需要注意线程安全？能举几个例子吗?    
>访问共享变量或资源  

典型的场景有访问共享对象的属性，访问 static 静态变量，访问共享的缓存等等。因为这些信息不仅会被一个线程访问到，还有可能被多个线程同时访问，那么就有可能在并发读写的情况下发生线程安全问题  
```java
/**
 * 描述：共享的变量或资源带来的线程安全问题
 */
public class ThreadNotSafe1 {
 
    static int i;
 
    public static void main(String[] args) throws InterruptedException {
        Runnable r = new Runnable() {
            @Override
            public void run() {
                for (int j = 0; j < 4000; j++) {
                    i++;
                }
            }
        };
        Thread thread1 = new Thread(r);
        Thread thread2 = new Thread(r);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();
        System.out.println(i);
    }
}
```
如代码所示，两个线程同时对 i 进行 i++ 操作，最后的输出可能是 4589 等小于8000的数，而不是我们期待的8000，这便是非常典型的共享变量带来的线程安全问题  
> 依赖时序的操作

如果我们操作的正确性是依赖时序的，而在多线程的情况下又不能保障执行的顺序和我们预想的一致，这个时候就会发生线程安全问题，如下面的代码所示  
```java
if (map.containsKey(key)) {
    map.remove(obj)
}
``` 
代码中首先检查 map 中有没有 key 对应的元素，如果有则继续执行 remove 操作。此时，这个组合操作就是危险的，因为它是先检查后操作，而执行过程中可能会被打断。如果此时有两个线程同时进入 if() 语句，然后它们都检查到存在 key 对应的元素，于是都希望执行下面的 remove 操作，随后一个线程率先把 obj 给删除了，而另外一个线程它刚已经检查过存在 key 对应的元素，if 条件成立，所以它也会继续执行删除 obj 的操作，但实际上，集合中的 obj 已经被前面的线程删除了，这种情况下就可能导致线程安全问题  

类似的情况还有很多，比如我们先检查 x=1，如果 x=1 就修改 x 的值，代码如下所示  
```java
if (x == 1) {
    x = 7 * x;
}
```

这样类似的场景都是同样的道理，“检查与执行”并非原子性操作，在中间可能被打断，而检查之后的结果也可能在执行时已经过期、无效，换句话说，获得正确结果取决于幸运的时序。这种情况下，我们就需要对它进行加锁等保护措施来保障操作的原子性  
>不同数据之间存在绑定关系

注意的线程安全场景是不同数据之间存在相互绑定关系的情况。有时候，我们的不同数据之间是成组出现的，存在着相互对应或绑定的关系，最典型的就是 IP 和端口号。有时候我们更换了 IP，往往需要同时更换端口号，如果没有把这两个操作绑定在一起，就有可能出现单独更换了 IP 或端口号的情况，而此时信息如果已经对外发布，信息获取方就有可能获取一个错误的 IP 与端口绑定情况，这时就发生了线程安全问题。在这种情况下，我们也同样需要保障操作的原子性
> 对方没有声明自己是线程安全的

在我们使用其他类时，如果对方没有声明自己是线程安全的，那么这种情况下对其他类进行多线程的并发操作，就有可能会发生线程安全问题。举个例子，比如说我们定义了 ArrayList，它本身并不是线程安全的，如果此时多个线程同时对 ArrayList 进行并发读/写，那么就有可能会产生线程安全问题，造成数据出错，而这个责任并不在 ArrayList，因为它本身并不是并发安全的.

#### 3. 线程池的常用参数?   
ThreadPoolExecutor 类中提供的四个构造方法。我们来看最⻓的那个，其余三个都是在这个构造
方法的基础上产生（其他几个构造方法说白点都是给定某些默认参数的构造方法比如默认制定拒
绝策略是什么），这里就不贴代码讲了，比较简单。  
```java
 /**
     * 用给定的初始参数创建一个新的ThreadPoolExecutor。
     */
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
                maximumPoolSize <= 0 ||
                maximumPoolSize < corePoolSize ||
                keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```
**ThreadPoolExecutor 3 个最重要的参数：**
corePoolSize : 核心线程数线程数定义了最小可以同时运行的线程数量。  
maximumPoolSize : 当队列中存放的任务达到队列容量的时候，当前可以同时运行的线程数
量变为最大线程数。  
workQueue : 当新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达
到的话，新任务就会被存放在队列中。  
ThreadPoolExecutor 其他常⻅参数:  
1. keepAliveTime :当线程池中的线程数量大于 corePoolSize 的时候，如果这时没有新的任务
提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了
keepAliveTime 才会被回收销毁；
2. unit : keepAliveTime 参数的时间单位。
3. threadFactory :executor 创建新线程的时候会用到。
4. handler :饱和策略。关于饱和策略下面单独介绍一下。
#### 4. ThreadPoolExecutor 饱和策略?   
如果当前同时运行的线程数量达到最大线程数量并且队列也已经被放满了任
时， ThreadPoolTaskExecutor 定义一些策略:  
+ ThreadPoolExecutor.AbortPolicy ：抛出 RejectedExecutionException 来拒绝新任务的处理。
+ ThreadPoolExecutor.CallerRunsPolicy ：调用执行自己的线程运行任务。您不会任务请求。
但是这种策略会降低对于新任务提交速度，影响程序的整体性能。另外，这个策略喜欢增加
队列容量。如果您的应用程序可以承受此延迟并且你不能任务丢弃任何一个任务请求的话，
你可以选择这个策略。
+ ThreadPoolExecutor.DiscardPolicy ： 不处理新任务，直接丢弃掉。
+ ThreadPoolExecutor.DiscardOldestPolicy ： 此策略将丢弃最早的未处理的任务请求。  
 
举个例子： Spring 通过 ThreadPoolTaskExecutor 或者我们直接通过 ThreadPoolExecutor 的构造
函数创建线程池的时候，当我们不指定 RejectedExecutionHandler 饱和策略的话来配置线程池的
时候默认使用的是 ThreadPoolExecutor.AbortPolicy 。在默认情况下， ThreadPoolExecutor 将抛出
RejectedExecutionException 来拒绝新来的任务 ，这代表你将丢失对这个任务的处理。 对于可伸
缩的应用程序，建议使用 ThreadPoolExecutor.CallerRunsPolicy 。当最大池被填满时，此策略为我们提供可伸缩队列。  
#### 5.volatile关键字？
#### 6.守护线程和非守护线程说一说？
java 中的线程分为两种：守护线程（Daemon）和用户线程（User）。任何线程都可以设置为守护线程和用户线程，通过方法
Thread.setDaemon(boolon) ； true 则 把 该 线程 设 置 为 守 护 线 程 ， 反 之 则 为 用 户 线 程 。
Thread.setDaemon()必须在 Thread.start()之前调用，否则运行时会抛出异常。  
两者的区别：  
唯一的区别是判断虚拟机(JVM)何时离开，Daemon 是为其他线程提供服务，如果全部的
User Thread 已经撤离，Daemon 没有可服务的线程，JVM 撤离。也可以理解为守护线程是
JVM 自动创建的线程（但不一定），用户线程是程序创建的线程；比如 JVM 的垃圾回收线程
是一个守护线程，当所有线程已经撤离， 不再产生垃圾，守护线程自然就没事可干了，当垃
圾回收线程是 Java 虚拟机上仅剩的线程时，Java 虚拟机会自动离开。
扩展：Thread Dump 打印出来的线程信息，含有 daemon 字样的线程即为守护进程，可能
会有：服务守护进程、编译守护进程、windows 下的监听 Ctrl+break 的守护进程、
Finalizer 守护进程、引用处理守护进程、GC 守护进程。
#### 7.mesi协议？