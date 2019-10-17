# 进程和线程
> 进程

进程有自己独立的执行环境，有自己私有的运行时资源，特别是有自己的内存空间。  
进程通常被看作是程序或者是应用，但是用户看到的应用实际上可能有很多协作的进程。  
大多数操作系统都支持进程间通信的资源，比如pipes和sockets，进程间通信不只用于同一个系统，也用于不同的系统。  
大多数java虚拟机都是单进程运行，java应用程序可以通过使用`ProcessBuilder`对象创建额外的进程。

> 线程

线程有时被称为轻量级的进程，创建一个线程比创建一个进程需要的资源更少。  
线程存在于进程之中，一个进程至少有一个线程，线程共享进程的资源，包括内存和打开的文件，这样可以高效的通信，但也会带来问题。  

# 使用线程创建并发应用程序的两种策略
- 直接控制线程的创建和管理，需要执行异步任务时创建线程。
- 抽象出线程的创建和管理过程，使用执行器，当有异步任务时交给执行器执行。 

# java线程的6种状态
- new （还没有启动的线程）
- runnable （在java虚拟机里执行的线程，或者正等着操作系统分配资源的线程）
- blocked （在等待锁的线程）
- waiting （等待另一线程（不指定等待时间）执行特定动作的线程，`Object#wait()，join()，LockSupport#park()`）
- timed_waiting （同waiting（但指定了等待时间），`Object#wait(long)，join(long)，LockSupport#parkNanos，#parkUntil`）
- terminated （已经执行完毕的线程） 

# sleep方法
调用sleep使处理器时间对其他线程可用是一种有用的方法，但是sleep的时间不能保证精确，因为这受限于底层操作系统。  

# interrupt
中断表示线程应该停止正在做的任务而去执行其他任务，一个线程怎么具体的响应中断取决于程序员，但通常来说是终止线程。  
一个线程通过在另一个线程对象上调用`interrupt`方法来给这个线程发送中断。  
为了使中断机制正常工作，被中断的线程必须支持自己的中断。  
线程支持中断的方式取决于它在做什么，如果经常调用抛出`InterruptedException`异常的方法，就在`catch`之后简单返回。  
如果线程长时间运行但没有调用抛出`InterruptedException`异常的方法怎么办？
可以定期调用`Thread.interrupted`方法，如果返回true，就直接返回。  
```
for (int i = 0; i < inputs.length; i++) {
    heavyCrunch(inputs[i]);
    if (Thread.interrupted()) {
        // We've been interrupted: no more crunching.
        return;
        // 这里简单演示，在复杂的程序中这里抛出中断异常更有意义，这样便于把处理中断的代码统一放到catch中
        throw new InterruptedException();
    }
}
```

#中断状态标志
- 中断机制是通过使用被称为中断状态的内部标识实现的。  
- 当一个线程调用静态方法`Thread.interrupted`检查中断时，就会清除中断状态。  
- 一个线程通过调用另一个线程的`isInterrupted`方法，可以查询线程的中断状态，而且不会改变中断状态。  
- 通过抛出`InterruptedException`退出的方法都会清除中断状态，但是可能其他线程调用`interrupt`方法再次设置中断状态。

# join方法
- 在指定线程上调用`join`方法可以让当前线程暂停执行，直到指定的线程执行完毕。  
- `join`方法也可以指定暂停执行的时间，但是和`sleep`方法一样，这个时间依赖于底层操作系统，并不精确。  
- 和`sleep`一样，`join`方法也通过抛出`InterruptedException`响应中断。

# 例子概念汇总
主线程启动新的线程执行`MessageLoop`，如果长时间未完成就中断。
```
public class SimpleThreads {

    // Display a message, preceded by
    // the name of the current thread
    static void threadMessage(String message) {
        String threadName =
            Thread.currentThread().getName();
        System.out.format("%s: %s%n",
                          threadName,
                          message);
    }

    private static class MessageLoop
        implements Runnable {
        public void run() {
            String importantInfo[] = {
                "Mares eat oats",
                "Does eat oats",
                "Little lambs eat ivy",
                "A kid will eat ivy too"
            };
            try {
                for (int i = 0;
                     i < importantInfo.length;
                     i++) {
                    // Pause for 4 seconds
                    Thread.sleep(4000);
                    // Print a message
                    threadMessage(importantInfo[i]);
                }
            } catch (InterruptedException e) {
                threadMessage("I wasn't done!");
            }
        }
    }

    public static void main(String args[])
        throws InterruptedException {

        // Delay, in milliseconds before
        // we interrupt MessageLoop
        // thread (default one hour).
        long patience = 1000 * 60 * 60;

        // If command line argument
        // present, gives patience
        // in seconds.
        if (args.length > 0) {
            try {
                patience = Long.parseLong(args[0]) * 1000;
            } catch (NumberFormatException e) {
                System.err.println("Argument must be an integer.");
                System.exit(1);
            }
        }

        threadMessage("Starting MessageLoop thread");
        long startTime = System.currentTimeMillis();
        Thread t = new Thread(new MessageLoop());
        t.start();

        threadMessage("Waiting for MessageLoop thread to finish");
        // loop until MessageLoop
        // thread exits
        while (t.isAlive()) {
            threadMessage("Still waiting...");
            // Wait maximum of 1 second
            // for MessageLoop thread
            // to finish.
            t.join(1000);
            if (((System.currentTimeMillis() - startTime) > patience)
                  && t.isAlive()) {
                threadMessage("Tired of waiting!");
                t.interrupt();
                // Shouldn't be long now
                // -- wait indefinitely
                t.join();
            }
        }
        threadMessage("Finally!");
    }
}

```

# Synchronization
线程间通过共享字段的访问来通信，这种方式很高效，但是可能会引起两种错误：  
1.线程干扰（thread interference）  
2.内存一致性错误（memory consistency errors）  
需要使用同步（Synchronization）来避免这些错误，但是同步会引起线程争用（thread contention）， 
当多个线程同时访问相同的资源时会发生这种争用，这回引起java执行线程更慢，甚至暂停执行。  
饥饿和活锁的发生（starvation and livelock）是线程争用的表现。  
总结：线程通信（导致） -> 线程干扰和内存一致性错误（为避免此错误） -> （使用）同步 -> （这引起）线程争用 -> （导致）饥饿和活锁问题

# 线程干扰（thread interference）
线程干扰描述了当多个线程访问共享数据时如何引入错误。

# 内存一致性错误（memory consistency errors）
内存一致性错误描述了由于共享内存视图不一致而导致的错误。  
不同的线程对于相同的数据有不同的视图时，就会发生此错误。  
避免内存一致性错误的关键是理解 happens-before 关系，这种关系保证了一个对内存的写操作语句对于其他语句是可见的。

# happens-before
> 构建happens-before关系，可以保证避免内存一致性错误。  

- 对于单线程来说，语句之间构成happens-before关系。  
- synchronized 的方法或同步块代码也构成happens-before关系。  
- Thread.start() 和 Thread.join() 方法可以形成 happens-before关系。  
- `java.util.concurrent`包及子包的方法都保证了happens-before关系。

# synchronized methods
同步方法描述了一个简单的习惯用法，可以有效的防止线程干扰和内存一致性错误。  
（注意：构造方法不能声明为同步方法）  
警告：当构造的对象会被多个线程访问时，不要过早的暴露对象的引用，例如：
```
// 构造方法里有这句话，使构造的对象都保存在集合里，但是当对象没有构造完毕的时候，其他线程
// 是可以通过instances访问这个对象的引用的！！
instances.add(this);
```
synchronized methods 使用了一个简单的策略避免线程干扰和内存一致性错误：  
如果一个对象对多个线程可见，所有对对象的变量读或者写的方法都通过synchronized methods，但是这会带来liveness问题。  
（例外是final类型的字段，初始化之后就不能修改，所以可以通过非同步的方法读取）

# Intrinsic Locks and Synchronization
同步是基于内部锁构建的，内部锁有两个作用：  
1.强制对对象的状态进行互斥访问；  
2.构建happens-before关系；  
（synchronized的方法即使是因为异常返回，也会释放锁）
(静态的synchronized方法锁定的对象是与这个class关联的class对象)  
同步语句对于使用细粒度的同步提高程序的并发性是很有用的，比如下面的例子中，c1和c2永远不会一起使用，所以没有理由在更新其中一个字段时，不能更新另一个字段，
可以使用细粒度的同步：
```
public class MsLunch {
    private long c1 = 0;
    private long c2 = 0;
    private Object lock1 = new Object();
    private Object lock2 = new Object();

    public void inc1() {
        synchronized(lock1) {
            c1++;
        }
    }

    public void inc2() {
        synchronized(lock2) {
            c2++;
        }
    }
}
```
# Reentrant Synchronization
可重入的同步，线程可以多次获取自己已经拥有的锁。

# Atomic Access
使用简单的原子变量访问可以防止线程干扰，但是还是可能发生内存一致性错误。   
对引用变量和大多数的原始变量的读写操作是原子的（除了long和double）。  
对所有用volatile修饰的变量的读写操作是原子的（包括long和double）。 

# liveness
> 并发应用程序及时执行的能力被称为liveness。
- deadlock
- starvation(一个线程长时间占用资源，导致其他线程阻塞)
- livelock(线程之间忙于互相响应不能正常执行，这和死锁不同，因为没有阻塞)  

# Guarded Blocks 保护快
保护块首先轮询一个条件，条件为真才能进行操作：
```
// 程序一直循环直到另一个线程把设置好joy，才能执行
public void guardedJoy() {
    // Simple loop guard. Wastes
    // processor time. Don't do this!
    while(!joy) {}
    System.out.println("Joy has been achieved!");
}
```
上面的方法一直循环，太浪费了，高效率的保护块可以使用`Object.wait`方法暂停当前线程：  
```
public synchronized void guardedJoy() {
    // This guard only loops once for each special event, which may not
    // be the event we're waiting for.
    while(!joy) {
        try {
            wait();
        } catch (InterruptedException e) {}
    }
    System.out.println("Joy and efficiency have been achieved!");
}
```
上面的例子加synchronized是因为调用wait方法的线程必须有这个对象的内部锁。  
当wait方法被调用时，线程会释放锁并暂停执行，在未来某个时刻，另一个线程会获取相同的锁并调用notifyAll方法通知该锁上等待的所有线程：  
```
public synchronized notifyJoy() {
    joy = true;
    notifyAll();
}
```
注意：相似的方法notify：  
notify只能唤醒单个线程，因为notify不允许指定唤醒的线程，所以它仅在大规模并行应用程序中有用，即具有大量线程的程序，它们都执行类似的任务，在这样的应用程序中，不必担心哪个线程被唤醒。

# 使用保护块创建生产者消费者程序

# Immutable Objects 不可变对象
如果对象的状态在构造后无法更改，则认为该对象是不可变的。  
对不可变对象的最大依赖已被广泛认为是创建简单，可靠代码的合理策略。  
不可变对象在并发应用程序中特别有用。  
由于它们不能更改状态，因此它们不能被线程干扰破坏或在不一致的状态下观察到。  
构造不可变对象的注意点
- 不提供setter方法
- 字段声明为final private
- 不让子类重写方法，所以可以把类声明为final（一种更复杂的方法是使构造函数私有，并在工厂方法中构造实例）