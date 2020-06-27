# 线程

## 概念
1. Java线程是什么  
> Java的线程，是运行在JVM的程序上的基本执行单元， Java针对线程抽象出Thread对象的概念。

2. Java线程分类
> Thread分为守护线程和非守护线程，当JVM启动时，伴随一个非守护线程的运行，我们称之为主线程/main函数，当JVM所有的非守护线程都销毁时，JVM实例也会销毁；

3. Java线程生命周期
> Java线程包含6个状态：NEW，RUNNABLE，BLOCKED，WAITING，TIMED_WAITING，TERMINATED

4. 多线程优缺点
> JVM支持多线程，正确使用多线程能大大提高程序的服务能力，同时也引入程序的复杂度和线程安全问题(不正确使用)。

## 状态
> 一个线程在指定的时刻上，只能存在一个状态；JVM的线程状态和操作系统的线程状态不是一一对应的。了解线程状态可用于分析线程问题/监控，不建议通过判断线程状态来进行逻辑处理

* `NEW` (新建)       
> 一个尚未启动的线程处于这一状态。(A thread that has not yet started is in this state.)
```Java
Theard t = new Theard();
```

* `RUNNABLE` (可运行)       
> 一个正在 Java 虚拟机中执行的线程处于这一状态。(A thread executing in the Java virtual machine is in this state.)

* `BLOCKED` (阻塞)       
> 一个正在阻塞等待一个监视器锁的线程处于这一状态。(A thread that is blocked waiting for a monitor lock is in this state.)

* `WAITING` (等待)       
> 一个正在无限期等待另一个线程执行一个特别的动作的线程处于这一状态。(A thread that is waiting indefinitely for another thread to perform a particular action is in this state.)

* `TIMED_WAITING` (计时等待)       
> 一个正在限时等待另一个线程执行一个动作的线程处于这一状态。(A thread that is waiting for another thread to perform an action for up to a specified waiting time is in this state.)

* `TERMINATED` (终止)       
> 一个已经退出的线程处于这一状态。(A thread that has exited is in this state.)
