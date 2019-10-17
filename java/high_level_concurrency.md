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
