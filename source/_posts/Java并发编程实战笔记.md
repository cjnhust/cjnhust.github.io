---
title: Java 并发编程实战笔记
categories: 
- Java
---

[TOC]

### 第一章

#### 一些概念

* 同步，异步
* 并发，并行
* 临界区:多个线程的公共资源
* 阻塞，非阻塞：
* 死锁:即A占有了B的资源，B占有了A的资源，两个线程都不能正常运行
* 饥饿:如某个高优先级线程一直抢占低优先级线程所需要的资源，使得低优先级线程不能正常运行
* 活锁：资源在两个线程之间不停跳动（两个线程都希望对方先使用资源），但没有一个线程拿到资源正常运行

#### 并发级别

1. 阻塞:一旦有一个线程占有临界区，其他线程只能等待该线程完成逻辑后释放临界区，是一种悲观的锁策略
2. 无饥饿:
3. 无障碍: 线程不会因为临界区被占据而挂起，一旦发现有资源冲突的问题则回滚相应操作
4. 无锁:一个线程可以在有限步数内完成
5. 无等待:所有线程都可以在有限步数内完成

#### JMM

1. **原子性**:一个操作是不可被中断的，即使多个线程一起运行的时候，一个操作一旦开始就不会被影响,**注意：**在32位jvm虚拟机上long和double变量的读写都不是原子性的，64位jvm虚拟机上也不一定是原子性的，需要看具体jvm虚拟机的内部实现，但是 volatile double 和volatile long的读写一定是原子性的                                             **参考https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.7** 
2. **可见性**：当一个线程对共享资源进行了修改，其他线程能够马上知道这个修改
3. **有序性**：因为存在指令重排序，而指令重排只会保证单线程条件下的语义一致，所以多线程条件下要保证指令重排不会影响原有语义
4. **指令重排的原则**（happen before原则）：

- **程序顺序原则**：单线程保证语义的串行性
- **volatile**原则：volatile变量的写一定是在读前面
- **锁规则**：解锁必定发生在之后的加锁之前
- **传递性**
- **线程start()方法在其所有操作之前**
- **线程所有操作先于线程终结之前**
- **对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生**
- **一个对象的初始化完成先行发生于他的finalize()方法的开始**

### 第二章

#### 线程的生命周期

如下图所示,**NEW**表示线程刚刚创建还没执行，当**start**()方法调用后才会执行，此时如果线程所需的资源都已经就绪就会进入**RUNNABLE**状态，如果执行的过程中遇到了**synchronized**同步块就会进入**BLOCKED**阻塞状态直到获得了锁。WAITING和TIMED_WAITING都表示线程处于等待状态，但是区别是前者是无时间限制的等待，后者是有时间限制的等待，通常因为**wait()**方法进入等待状态的线程是在等待**notify**()方法的执行，而因为**join()**方法进入等待的线程是在等待目标线程的中止，当等到了相应的事件线程会再次执行并进入**RUNNABLE**状态，当线程执行完毕后则会进入**TERMINATED**状态，表示结束。线程从**NEW**状态进入其他状态后不会再回到NEW状态，当然进入**TERMINATED**状态后也不会回到其他状态

![线程生命周期](/images/线程生命周期.png)

#### 线程的基本操作

1. **新建线程**：

```java
        Thread thread = new Thread();
        thread.start();
```

这段代码会新建一个线程并让这个线程执行run方法

如果直接调用run()方法只会在当前线程串行执行该方法中的代码

2.线程中断

```java
public void Thread.interrupt() //中断线程
public boolean Thread.isInterrupted() //判断线程是否被中断，不清除中断标记
public static boolean Thread.interrupted()//判断线程是否被中断，清楚中断标记
```

关于sleep方法

```java
    public static native void sleep(long millis) throws InterruptedException;
```

当线程处于sleep状态被中断时会抛出一个InterruptedException，此时会重置中断状态，所以如果要处理中断逻辑必须手动在异常处理部分再次调用中断方法，代码如下所示

```java
Thread thread = new Thread(() -> {
            int i=0;
            while (true){
                if (Thread.currentThread().isInterrupted()){
                    System.out.println("Interrupted");
                    break;
                }
                try {
                    if (i==0) {
                        i++;
                        Thread.sleep(2000);
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                    Thread.currentThread().interrupt();
                }
                Thread.yield();
            }
        });
```

3.挂起和继续执行

4.wait()和notify()/notifyAll()

这两个方法是属于Object类的，所以事实上wait方法是使线程进入关于某个object对象的等待队列，然后notify()方法是随机唤起一个等待队列中的线程，notifyAll()是唤起等待队列中的所有线程，示例代码如下所示

```java
 Object object = new Object();
        Runnable runnable = () -> {
           synchronized (object){
               for (int i=0;i<10;i++){
                   if (i==5) {
                       try {
                           object.wait();
                       } catch (InterruptedException e) {
                           e.printStackTrace();
                       }
                   }
                   System.out.println(Thread.currentThread().getName()+" "+i);
               }
           }
        };
        Thread thread1 = new Thread(() -> {
            synchronized (object){
                for (int i=0;i<10;i++){
                    System.out.println(Thread.currentThread().getName()+" "+i);

                }
                object.notify();
            }
        });
        Thread thread2 = new Thread(runnable);
        thread2.start();
        thread1.start();
```

wait()和sleep()方法的区别是前者会让线程释放锁而后者不会释放任何资源

5.join()和yield()

#### 线程组

#### 守护线程

#### 线程优先级

#### 线程安全(synchronized)



#### volatile和JMM

volatile事实上只是为变量提供了可见性和禁止变量相关的指令重排序（jdk1.5之后）,**synchronized**并不能完全禁止指令重排序，它只能保证跨同步块内外的指令不发生重排，但是单单同步块外和同步块内是可以的。

volatile的使用场景如下:

- 该字段不遵循其他字段的不变式。
- 对字段的写操作不依赖于当前值。
- 没有线程违反预期的语义写入非法值。
- 读取操作不依赖于其它非volatile字段的值。

### 第三章

#### JDK并发包

**重入锁(ReentrantLock)：**

**信号量(Semaphore)**

**读写锁(ReadWriteLock)**

**倒计时器(CountDownLatch)**

**循环栅栏(CyclicBarrier)**

**线程阻塞工具类(LockSupport)**

**线程池**

ForkJoinPool 

ConcurrentSkipListMap 

ConcurrentLinkedQueue<T>

AtomicStampedReference<T>

AtomicIntegerFieldUpdater<T>