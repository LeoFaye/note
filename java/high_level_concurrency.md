# Lock Objects
synchronized 依赖一种简单的可重入锁定，它虽然容易使用，但是有许多局限性；  
更复杂锁定用法参考`java.util.concurrent.locks`包；  
锁对象的工作方式和同步代码块使用的内部锁非常像，同一时刻，只能有一个线程拥有锁对象。  
锁对象通过关联`Condition`对象也支持`wait`和`notify`机制。  
相较于内部锁，对象锁最大的优点是当尝试获取锁而没有获取到时能够返回。  
`tryLock`在没有获取到锁，或者指定时间里没有获取到锁，就会返回。  
`lockInterruptibly`在请求锁并被阻塞时，如果被中断，会被唤醒，并被要求处理`InterruptedException`。

# Executors
在大型应用程序中，线程的管理和应用分开是有意义的。  
`java.util.concurrent`包定义了3个执行器接口
- Executor  
- ExecutorService  
- ScheduledExecutorService

`Executor`有方法`execute`，接收一个`Runnable`对象；  
`ExecutorService`有`submit`方法，可以接收`Runnable`或`Callable`对象，`Callable`对象的`call`方法可以返回一个值；  
`submit`方法返回`Future`对象，可以用来查找Callable对象方法的返回值，且可以管理任务的状态；  
`ExecutorService`还提供提交大量`Callable`对象的方法，以及很多关闭执行器的方法。  
`ScheduledExecutorService`使用schedule补充了`ExecutorService`，可以在指定的延时之后执行任务，此外，接口还定义了
`scheduleAtFixedRate `和`scheduleWithFixedDelay`方法，以固定的频率或固定的延迟执行任务。

# Thread Pools
执行器的大多数实现都使用线程池，由工作线程组成。  
使用工作线程可最大程度地减少线程创建所带来的开销。  
- 固定线程池`newFixedThreadPool`
- 可扩展的线程池`newCachedThreadPool`
- 单个线程的执行器`newSingleThreadExecutor`

固定线程池的一个优点是可以正常降级，因为限制了线程创建数量，不会导致线程快速增长，
以至于超出系统负载，系统奔溃，比如每一个http请求都由一个线程负责处理，如果出现大量的请求，
不限制线程数量的创建就会发生问题。  

可扩展的线程池适用于启动许多短期任务的应用。  

单个线程的执行器，一次执行一个任务。  

如果这些方法都不满足需求，可以考虑创建：
- java.util.concurrent.ThreadPoolExecutor
- java.util.concurrent.ScheduledThreadPoolExecutor  

# Concurrent Collections
这些集合容器都可以避免内存一致性错误。
>BlockingQueue  

`BlockingQueue`定义了一个先进先出的数据结构，当您试图添加到完整队列或从空队列检索时，该数据结构将被阻塞或超时。

>ConcurrentMap  

`ConcurrentMap`是`java.util.Map`的子接口，它定义了有用的原子操作。 这些操作仅在存在键时才删除或替换键值对，或者仅在键不存在时才添加键值对。

>ConcurrentNavigableMap  

`ConcurrentNavigableMap`是`ConcurrentMap`的子接口，支持近似匹配。`ConcurrentNavigableMap`的标准通用实现是`ConcurrentSkipListMap`。

# Atomic Variables
java.util.concurrent.atomic包定义了支持对单个变量进行原子操作的类。  
所有类都具有`get`和`set`方法，它们的工作方式类似于对`volatile`变量的读写，也就是可以避免内存一致性错误。  
原子的`compareAndSet`方法也可以避免内存一致性错误，适用于整数原子变量的简单方法也是如此。

# Concurrent Random Numbers
在JDK 7中，`java.util.concurrent`包含一个便利类`ThreadLocalRandom`，用于需要使用随机数的多线程应用或`ForkJoinTasks`。  
对于并发访问，使用`ThreadLocalRandom`而不是`Math.Random`可以减少争用，并最终提高性能。  
```
int r = ThreadLocalRandom.current() .nextInt(4, 77);
```
# CAS compare and swap
加锁是一种悲观的策略，它总是认为每次访问共享资源的时候，总会发生冲突，所以宁愿牺牲性能（时间）来保证数据安全。  

无锁是一种乐观的策略，它假设线程访问共享资源不会发生冲突，所以不需要加锁，因此线程将不断执行，不需要停止；一旦碰到冲突，就重试当前操作直到没有冲突为止。  

无锁的策略使用一种叫做比较交换的技术（CAS Compare And Swap）来鉴别线程冲突，一旦检测到冲突产生，就重试当前操作直到没有冲突为止。  

由于CAS操作属于乐观派，它总是认为自己能够操作成功，所以操作失败的线程将会再次发起操作，而不是被OS挂起；所以说，即使CAS操作没有使用同步锁，其它线程也能够知道对共享变量的影响。  

因为其它线程没有被挂起，并且将会再次发起修改尝试，所以无锁操作即CAS操作天生免疫死锁。  

另外一点需要知道的是，CAS是系统原语，CAS操作是一条CPU的原子指令，所以不会有线程安全问题。

> Java提供的CAS操作：原子操作类

Java提供了一个Unsafe类，其内部方法操作可以像C的指针一样直接操作内存，方法都是native的。  

为了让Java程序员能够受益于CAS等CPU指令，JDK并发包中有一个atomic包，它们是原子操作类，它们使用的是无锁的CAS操作，并且统统线程安全；atomic包下的几乎所有的类都使用了这个Unsafe类。

> 分类
- 原子更新基本类型 `AtomicInteger` `AtomicLong` `AtomicBoolean`
- 原子更新引用类型 `AtomicMarkableReference` `AtomicStampedReference` `AtomicReference`
- 原子更新数组类型 `AtomicIntegerArray` `AtomicLongArray` `AtomicReferenceArray`
- 原子更新字段类型 `AtomicIntegerFieldUpdater` `AtomicLongFieldUpdater` `AtomicReferenceFieldUpdater`

> ABA问题解决方案

原子操作类提供了一个带有时间戳的原子操作类 `AtomicStampedReference`  
当带有时间戳的原子操作类`AtomicStampedReference`对应的数值被修改时，除了更新数据本身外，还必须要更新时间戳。  
当AtomicStampedReference设置对象值时，对象值以及时间戳都必须满足期望值，写入才会成功。  
因此，即使对象值被反复读写，写回原值，只要时间戳发生变化，就能防止不恰当的写入。