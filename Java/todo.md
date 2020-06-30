本月计划： Java线程

线程理论/概念  使用场景， JDK实现

参考：

https://www.javadoop.com/

https://my.oschina.net/goldenshaw?tab=newest&catalogId=3277710


1、线程概念

 Java线程是什么？

 1、描述：Java的线程，是运行在JVM的程序上的基本执行单元， Java针对线程抽象出Thread对象的概念。

 2、Java线程分类：Thread分为守护线程和非守护线程，当JVM启动时，伴随一个非守护线程的运行，我们称之为主线程/main函数，当JVM所有的非守护线程都销毁时，JVM实例也会销毁；

 3、Java线程生命周期：Java线程包含6个状态：NEW，RUNNABLE，BLOCKED，WAITING，TIMED_WAITING，TERMINATED

 4、多线程优缺点：JVM支持多线程，正确使用多线程能大大提高程序的服务能力，同时也引入程序的复杂度和线程安全问题(不正确使用)。


2、线程状态：

 2-1、一个线程在指定的时刻上，只能存在一个状态；JVM的线程状态和操作系统的线程状态不是一一对应的。了解线程状态可用于分析线程问题/监控，不建议通过判断线程状态来进行逻辑处理

 2-2、NEW：尚未启动的线程处于这一状态，即尚未调用start()。

 2-3、RUNNABLE：JVM中可执行的线程处于这一状态

 2-4、BLOCKED：线程等待监视器锁时处于这一状态，如 synchronized，通俗理解：当因为获取不到锁而无法进入同步块时，线程处于 BLOCKED 状态。

 2-5、WAITINGL：正在无限期等待另一个线程执行一个特别的动作的线程处于这一状态，如 Object.wait()、Thread.join()、LockSupport.park()。特别动作分别为：notify/notifyAll、线程完结、、LockSupport.unpark()

 2-6、TIMED_WAITING：限时等待另一个线程执行一个动作的线程处于这一状态，如：Thread.sleep(long millis)、Object.wait(long millis，Object.wait(long millis)、Thread.join(long millis)、LockSupport.parkNanos(Object blocker, long nanos) 、 LockSupport.parkUntil(Object blocker, long nanos)

 2-7、TERMINATED：已经退出的线程处于这一状态



 特别说明：
 Runnable是JVM层面定义的线程状态，但不代表系统线程正在运行中，比如当线程在进行IO操作时，系统线程是阻塞状态（未设置non-blocking模式）的，但Java线程是Runnable.

 Blocked和Waiting/TimedWaiting 的区别，前者是被动，完全受系统控制，常用于竞争资源同步；后者是受业务主动控制，如线程所需条件(condition)不足，需要等条件满足后由业务唤醒/限时唤醒，常用语线程协作。当然底层机制也是不一样（Monitor），这里不做讨论

3、JMM

 1、描述：Java内存模型（Java Memory Model ,JMM）是屏蔽了各种硬件和操作系统访问内存的差异，保证了Java程序在各种平台下对内存的访问效果一致的机制及规范。它解决了 CPU多级缓存、处理器优化（指令重排）等导致的内存访问问题，保证了并发场景下的可见性、原子性和有序性。

 2、原子性：保证在同一时刻，只有一个线程可以执行某个原子区域。如果多线程并发对共享资源进行非原子操作会出现安全问题。

 3、可见性：保证一个线程的变化可见。如果一个线程的变化对其他线程不可见，会出现安全问题。

 4、有序性：如果在本线程内观察，所有的操作都是有序的(as-if-serial)；如果在一个线程中观察另一个线程，所有的操作都是无序的。如果多线程协作依赖单线程的有序性，会出现线程安全问题

 5、解决方案：可以使用 synchronized 定义原子性的边界，来解决多线程之间的原子性，也可以使用衍生API，如 Lock 接口这类API实现了 synchronized 语义效果；JMM提供的happens-before规则，能保证多线程之间的可见性和有序性。JDK提供了很多遵循 happens-before 规则的类，如CountDownLatch，Semaphore，ReentrantLock 等

4、Monitor机制

 1、描述：Monitor机制的目的主要是为了互斥进入临界区，为了做到能够阻塞无法进入临界区.

 2、Monitor Object：为了实现Monitor机制，引入Monitor object概念，有对应的entrySet、waitSet。分别存放竞争所失败的线程（被阻塞的线程）和 wait操作的线程。

 3、使用方式：synchronized 关键字修饰的方法、代码块，就是 monitor 机制的临界区。synchronized 所关联的对象，就是 Monitor object，该对象可以是实例对象，也可以是类对象，在对象头信息包含锁信息，如偏向锁，轻量级锁，重量级锁等锁信息

 为何 synchronized 性能比 ReentrantLock 低？ 阻塞 or 唤醒 一个线程，需要从用户态转换到核心态中，状态转换需要耗费很多的处理器时间。ReentrantLock 可以采用非公平锁降低阻塞几率，而且提供更多的API接口。随着JDK的发展，synchronized也在优化，引入了偏向锁，轻量级锁(短暂自旋），减少线程的状态切换。

5、锁，在Java SE 1.6之前,  synchronized被称为重量级锁.在SE 1.6之后进行了各种优化,就出现了偏向锁,轻量锁,目的是为了减少获得锁和释放锁带来的性能消耗.

 1、锁升级：

  偏向锁：在大多数情况下,锁不仅不存在多线程竞争,而且总是由同一个线程多次获得,为了让线程获得锁的代价更低而引入了偏向锁（单纯比较线程ID是否相等即可成功获取锁，减少CAS的开销）；适用于无锁竞争的场景

  轻量级锁：在关闭偏向锁 or 多个线程竞争偏向锁（升级为轻量级锁），线程会尝试后获取轻量级锁；两个线程短暂的锁竞争情况

  重量级锁：当线程竞争轻量级锁的自旋次数达到一定次数，当前锁则升级为重量级锁

 2、自旋锁：为了避免线程的频繁阻塞和唤醒，当线程获取锁失败时，会通过自旋获取锁的方式避免进入阻塞状态，同时也会增加CPU使用率；1.6以前自旋次数固定，1.6引入自适应自旋次数，智能的判定自旋的次数

 3、锁消除：通过逃逸分析，JVM检测到当前锁操作不存在竞争时，会对该锁操作进行锁消除

 4、锁粗化：正常情况下，锁区域越小越好，便于锁的快速释放；但存在同一个锁的连续 获取 - 释放， 会导致不必要的开销，针对这样的情形，JVM会将这些多个连续的加锁，解锁操作连在一起，扩展成范围更大的锁

6、应用：AQS，线程池，ThreadLocal

 AQS：

 1、描述：同步工具类，提供锁资源的获取和释放接口，如果锁资源获取失败会将当前线程包装成节点进入双向链表中（由于最终实现的形态是FIFO，所以被称为队列，采用队列式 + 自旋，也被称为CLH队列），然后挂起线程；触发锁资源释放的同时，会唤醒挂起最旧的Node（线程）；同时还支持Condition接口的核心实现。

 2、锁状态控制：AQS使用一个int成员变量 state 表示同步状态，同步状态信息通过getState，setState，compareAndSetState进行操作。

 3、模式：Exclusive，独占模式，实现例子如：ReentrantLock，只有一个线程能执行；Share，共享模式，如 Semaphore/CountDownLatch

 4、Condition支持：实现类似WaitSet机制，对应接口有 await，sign，signAll，对应wait和notify等；优点是可以支持多个条件创建。ArrayBlockingQueue、CyclicBarrier 就是使用了该技术



线程池：是一种线程的使用模式，它为了降低线程频繁的创建和销毁所带来的资源消耗与代价，同时还可以进行参数的动态配置，运行监控等

ThreadPoolExecutor

 BlockingQueue<Runnable> taskQueue; //装置任务的队列

 Set<Worker> wokers;

 RejectedExecutionHandler handler; //1、抛出拒绝异常 2、无视任务提交 3、使用当前线程执行任务 4、将任务队列的最早任务剔除，当前任务提交到线程池

 int corePoolSize;

 int maxPoolSize;

 int keepAliveTime;  //currentSize > corePoolSize or allowCoreThreadTimeOut，通过 taskQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS)



 AtomicInteger clt;  //包含线程池状态和线程数量





 FutureTask submit(Runnable tast); //将任务转化为FutureTask，期间不会抛出异常


 [0, corePoolSize) > new Worker(Runnable task, boolean core) //返回值为boolean，表示是否成功；

 [corePoolSize, maxPoolSize)，优先级如下

  1、taskQueue.offer(Runnable task)

  2、new Worker(Runnable task, boolean core)，返回false表示线程数超过了 maxPoolSize，则进入3

  3、rejectedHandler


生命周期

 1、Runnable，创建线程池时的状态

 2、Shutdown, 调用了shutdown()，正在执行的任务不会中断，任务队列中的任务依然会执行，当前状态为Shutdown，拒绝新任务。 直到任务全部执行完则进入TIDYING 阶段

 3、Stop，调用shutdownNow()，中断所有正在执行任务的Worker，清空任务队列，当前状态为Stop，然后进入 TIDYING 阶段

 4、Tidying，通过2、3阶段进入，terminated() 调用，更新状态为 Terminated



ScheduledThreadPoolExecutor extends ThreadPoolExecutor implements ScheduledExecutorService


 ScheduledFutureTask

  int heapIndex;  //快速定位任务在队列(二叉堆实现)的位置

  long time; //任务开始时间

  long period;  //周期时间，正数表示 fixedRate，负数表示 fixedDelay，0表示一次性任务

  RunnableScheduledFuture outerTask;  //任务，用于反复执行


 DelayedWorkQueue  延迟队列实现定时任务的执行



疑问：阻塞，非阻塞；同步，异步；

5、持久化

 5-1、RDB(Redis database)，将数据库的所有数据（状态）保存为RDB文件，通过SAVE（同步），BGSAVE（fork出子进程，异步）命令触发；执行BGSAVE期间，将拒绝执行新的SAVE，BGSAVE命令；

  5-1-1、通过SaveParams，设置BGSAVE的触发规则，如save 900 1表示在900s内，至少有一次修改。Redis会保存 dirty(修改次数)、lastsave(上一次save操作时间)，对比SaveParams条件，符合则触发BGSAVE

  5-1-2、加载RDB文件不需要输入命令，Redis服务启动时，如果配置没开启AOF，会自动扫描是否存在 RDB 文件，存在则加载，加载操作为同步.

  5-1-3、生成RDB文件过程中，如果数据已过期，该数据将不会进入RDB文件。加载RDB文件过程中，发现有数据已过期，则会被丢弃

 5-2、AOF(Append only file)，Redis的另一个持久化方式，通过保存Redis服务器所执行的命令来记录数据库状态。

  5-2-1、命令追加：数据的更新命令，会append到AOF的buffer尾部。

  5-2-2、AOF的写入和同步，配置appendsync：flushAppendOnlyFile()函数调用时，根据 appendsync 进行文件写入(文件Buffer) 和 同步(Buffer数据导入文件)

    always：每次都会触发AOF文件的写入，并且触发文件同步. 安全性最高，但性能最差

    no：每次都会触发AOF文件写入，但文件同步有系统决定，不受控制. 性能最好，安全性不可预估，因为不知道系统什么时候发起文件同步

    everysec(默认选项)：与上一次AOF文件写入间隔如果超过1s，则写入 + 同步. 折中安全性和性能，出现宕机最多丢失1s的数据

    注：IO流，在操作系统上都会基于buffer的方式进行数据操作，写文件可以理解为只是写入缓冲区，flush(系统底层指令为fsync/fdatasync)会将缓冲区数据刷到目标源

  5-2-3、服务器载入AOF文件，启动本地的虚拟RedisClient，一个一个的命令执行，直到执行完所有命令

  5-2-4、Rewrite：随着运行时间推移，服务器执行的命令累计，AOF文件膨胀，为了解决这个问题，Redis提供了AOF rewrite 功能，将数据库的数据通过命令写入到新的AOF文件中，也有对应的BGRwriteAOF

  5-2-5、BGRewrite过程中，主进程会有增量的数据；Redis在启动Rewrite时，新增AOFRewrite缓冲区，用来存放增量命令，rewrite完成之后，将增量数据同步到rewrite文件，之后替换原AOF文件

 5-3、RDB vs AOF

  5-3-1、优点：RDB文件紧凑，恢复数据相对AOF更快（文件更小），适合数据备份，传输；缺点：BGSAVE耗时较长，安全性比不上AOF（耗时 = 恢复数据的时间）

  5-3-2、优点：AOF数据更完整，安全性更高（取决于 appendsync），可能解决误命令的恢复； 缺点：AOF文件大于RDB，恢复较RDB慢
  
6、Maxmemory policy：Reids服务器内存随着数据增加，到达maxmemory时，会触发 Maxmemory policy 进行数据淘汰（驱逐）

 6-1、volatile-lru：从过期集中删除最久没被访问的一个key（remove the key with an expire set using an LRU algorithm）

 6-2、allkeys-lru：从所有key中，删除最久没被访问的一个key（remove any key according to the LRU algorithm）

 6-3、volatile-random：从过期集中随机删除一个key（remove a random key with an expire set）

 6-4、allkeys-random：从所有key中，随机删除一个key（remove a random key, any key）

 6-5、volatile-ttl：删除最快过期的一个key(remove the key with the nearest expire time (minimal TTL))

 6-6、noeviction：默认选项，不做任何操作，直接访问写操作失败(don't expire at all, just return an error on write operations)

 6-7、LRU，最小TTL 算法只是一个非精准的算法（Redis数据量较大）；Redis通过 maxmemory-samples 设置取样的个数，默认5，也就是说lru和ttl是基于指定个数下的数据进行淘汰，所以不是一个精确的淘汰机制
