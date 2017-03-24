Java 语言通过 synchronized 关键字来保证原子性，这是因为每一个 Object 都有一个隐含的锁，这个也称作监视器对象。在进入 synchronized 之前自动获取此内部锁，而一旦离开此方式，无论是完成或者中断都会自动释放锁。显然这是一个独占锁，每个锁请求之间是互斥的。相对于众多高级锁 (Lock/ReadWriteLock 等)，synchronized 的代价都比后者要高。但是 synchronzied 的语法比较简单，而且也比较容易使用和理解。Lock 一旦调用了 lock() 方法获取到锁而未正确释放的话很有可能造成死锁，所以 Lock 的释放操作总是跟在 finally 代码块里面，这在代码结构上也是一次调整和冗余。Lock 的实现已经将硬件资源用到了极致，所以未来可优化的空间不大，除非硬件有了更高的性能，但是 synchronized 只是规范的一种实现，这在不同的平台不同的硬件还有很高的提升空间，未来 Java 锁上的优化也会主要在这上面。既然 synchronzied 都不可能避免死锁产生，那么死锁情况会是经常容易出现的错误，下面具体描述死锁发生的原因及解决方法。

## **死锁描述**

死锁是操作系统层面的一个错误，是进程死锁的简称，最早在 1965 年由 Dijkstra 在研究银行家算法时提出的，它是计算机操作系统乃至整个并发程序设计领域最难处理的问题之一。

事实上，计算机世界有很多事情需要多线程方式去解决，因为这样才能最大程度上利用资源，才能体现出计算的高效。但是，实际上来说，计算机系统中有很多一次只能由一个进程使用的资源的情况，例如打印机，同时只能有一个进程控制它。在多通道程序设计环境中，若干进程往往要共享这类资源，而且一个进程所需要的资源还很有可能不止一个。因此，就会出现若干进程竞争有限资源，又推进顺序不当，从而构成无限期循环等待的局面。我们称这种状态为死锁。简单一点描述，死锁是指多个进程循环等待它方占有的资源而无限期地僵持下去的局面。很显然，如果没有外力的作用，那么死锁涉及到的各个进程都将永远处于封锁状态。

系统发生死锁现象不仅浪费大量的系统资源，甚至导致整个系统崩溃，带来灾难性后果。所以，对于死锁问题在理论上和技术上都必须予以高度重视。

## **银行家算法**

一个银行家如何将一定数目的资金安全地借给若干个客户，使这些客户既能借到钱完成要干的事，同时银行家又能收回全部资金而不至于破产。银行家就像一个操作系统，客户就像运行的进程，银行家的资金就是系统的资源。

银行家算法需要确保以下四点：

- 当一个顾客对资金的最大需求量不超过银行家现有的资金时就可接纳该顾客
- 顾客可以分期贷款, 但贷款的总数不能超过最大需求量
- 当银行家现有的资金不能满足顾客尚需的贷款数额时，对顾客的贷款可- 推迟支付，但总能使顾客在有限的时间里得到贷款
- 当顾客得到所需的全部资金后，一定能在有限的时间里归还所有的资金

清单 1. 银行家算法实现

```java
/*一共有５个进程需要请求资源，有３类资源*/
public class BankDemo {
	/* 每个进程所需要的最大资源数 */
	public static int MAX[][] = { { 7, 5, 3 }, { 3, 2, 2 }, { 9, 0, 2 },
				      { 2, 2, 2 }, { 4, 3, 3 } };
	/* 系统拥有的初始资源数 */
	public static int AVAILABLE[] = { 10, 5, 7 };
	/* 系统已给每个进程分配的资源数 */
	public static int ALLOCATION[][] = { { 0, 0, 0 }, { 0, 0, 0 }, { 0, 0, 0 },
					     { 0, 0, 0 }, { 0, 0, 0 } };
	/* 每个进程还需要的资源数 */
	public static int NEED[][] = { { 7, 5, 3 }, { 3, 2, 2 }, { 9, 0, 2 },
				       { 2, 2, 2 }, { 4, 3, 3 } };
	/* 每次申请的资源数 */
	public static int Request[] = { 0, 0, 0 };
	/* 进程数与资源数 */
	public static int	M	= 5, N = 3;
	int			FALSE	= 0;
	int			TRUE	= 1;
	public void showdata()
	{
		int i, j;
		System.out.print( "系统可用的资源数为:/n" );
		for ( j = 0; j < N; j++ )
		{
			System.out.print( "资源" + j + ":" + AVAILABLE[j] + " " );
		}
		System.out.println();
		System.out.println( "各进程还需要的资源量:" );
		for ( i = 0; i < M; i++ )
		{
			System.out.print( "进程" + i + ":" );
			for ( j = 0; j < N; j++ )
			{
				System.out.print( "资源" + j + ":" + NEED[i][j] + " " );
			}
			System.out.print( "/n" );
		}
		System.out.print( "各进程已经得到的资源量: /n" );
		for ( i = 0; i < M; i++ )
		{
			System.out.print( "进程" );
			System.out.print( i );
			for ( j = 0; j < N; j++ )
			{
				System.out.print( "资源" + j + ":" + ALLOCATION[i][j] + " " );
			}
			System.out.print( "/n" );
		}
	}


	/* 分配资源，并重新更新各种状态 */
	public void changdata( int k )
	{
		int j;
		for ( j = 0; j < N; j++ )
		{
			AVAILABLE[j]		= AVAILABLE[j] - Request[j];
			ALLOCATION[k][j]	= ALLOCATION[k][j] + Request[j];
			NEED[k][j]		= NEED[k][j] - Request[j];
		}
	};
	/* 回收资源，并重新更新各种状态 */
	public void rstordata( int k )
	{
		int j;
		for ( j = 0; j < N; j++ )
		{
			AVAILABLE[j]		= AVAILABLE[j] + Request[j];
			ALLOCATION[k][j]	= ALLOCATION[k][j] - Request[j];
			NEED[k][j]		= NEED[k][j] + Request[j];
		}
	};
	/* 释放资源 */
	public void free( int k )
	{
		for ( int j = 0; j < N; j++ )
		{
			AVAILABLE[j] = AVAILABLE[j] + ALLOCATION[k][j];
			System.out.print( "释放" + k + "号进程的" + j + "资源!/n" );
		}
	}


	public int check0( int k )
	{
		int j, n = 0;
		for ( j = 0; j < N; j++ )
		{
			if ( NEED[k][j] == 0 )
				n++;
		}
		if ( n == 3 )
			return(1);
		else
			return(0);
	}


	/*
	 * 检查安全性函数
	 * 所以银行家算法其核心是：保证银行家系统的资源数至少不小于一个客户的所需要的资源数。在安全性检查函数 chkerr() 上由这个方法来实现
	 * 这个循环来进行核心判断，从而完成了银行家算法的安全性检查工作。
	 */
	public int chkerr( int s )
	{
		int	WORK;
		int	FINISH[] = new int[M], temp[] = new int[M];     /* 保存临时的安全进程序列 */
		int	i, j, k = 0;
		for ( i = 0; i < M; i++ )
			FINISH[i] = FALSE;
		for ( j = 0; j < N; j++ )
		{
			WORK	= AVAILABLE[j];                         /* 第 j 个资源可用数 */
			i	= s;
			/* 判断第 i 个进程是否满足条件 */
			while ( i < M )
			{
				if ( FINISH[i] == FALSE && NEED[i][j] <= WORK )
				{
					WORK		= WORK + ALLOCATION[i][j];
					FINISH[i]	= TRUE;
					temp[k]		= i;
					k++;
					i = 0;
				} else {
					i++;
				}
			}
			for ( i = 0; i < M; i++ )
				if ( FINISH[i] == FALSE )
				{
					System.out.print( "/n 系统不安全!!! 本次资源申请不成功!/n" );
					return(1);
				}
		}
		System.out.print( "/n 经安全性检查，系统安全，本次分配成功。/n" );
		System.out.print( "本次安全序列：" );
		for ( i = 0; i < M - 1; i++ )
		{
			System.out.print( "进程" + temp[i] + "->" );
		}
		System.out.print( "进程" + temp[M - 1] );
		System.out.println( "/n" );
		return(0);
	}
}
```

## **死锁示例**
死锁问题是多线程特有的问题，它可以被认为是线程间切换消耗系统性能的一种极端情况。在死锁时，线程间相互等待资源，而又不释放自身的资源，导致无穷无尽的等待，其结果是系统任务永远无法执行完成。死锁问题是在多线程开发中应该坚决避免和杜绝的问题。

一般来说，要出现死锁问题需要满足以下条件：

1. 互斥条件：一个资源每次只能被一个线程使用。
2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件：进程已获得的资源，在未使用完之前，不能强行剥夺。
4. 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。

只要破坏死锁 4 个必要条件之一中的任何一个，死锁问题就能被解决。

我们先来看一个示例，前面说过，死锁是两个甚至多个线程被永久阻塞时的一种运行局面，这种局面的生成伴随着至少两个线程和两个或者多个资源。代码清单 2 所示的示例中，我们编写了一个简单的程序，它将会引起死锁发生，然后我们就会明白如何分析它。

清单 2. 死锁示例

```java
public class ThreadDeadlock {
    public static void main(String[] args) throws InterruptedException {
        Object obj1 = new Object();
        Object obj2 = new Object();
        Object obj3 = new Object();

        Thread t1 = new Thread(new SyncThread(obj1, obj2), "t1");
        Thread t2 = new Thread(new SyncThread(obj2, obj3), "t2");
        Thread t3 = new Thread(new SyncThread(obj3, obj1), "t3");

        t1.start();
        Thread.sleep(5000);
        t2.start();
        Thread.sleep(5000);
        t3.start();
    }
}
```

```java
class SyncThread implements Runnable {
    private Object obj1;
    private Object obj2;

    public SyncThread(Object o1, Object o2) {
        this.obj1 = o1;
        this.obj2 = o2;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        System.out.println(name + " acquiring lock on " + obj1);

        synchronized (obj1) {
            System.out.println(name + " acquired lock on " + obj1);
            work();
            System.out.println(name + " acquiring lock on " + obj2);

            synchronized (obj2) {
                System.out.println(name + " acquired lock on " + obj2);
                work();
            }

            System.out.println(name + " released lock on " + obj2);
        }

        System.out.println(name + " released lock on " + obj1);
        System.out.println(name + " finished execution.");
    }

    private void work() {
        try {
            Thread.sleep(30000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

在上面的程序中同步线程正完成 Runnable 的接口，它工作的是两个对象，这两个对象向对方寻求死锁而且都在使用同步阻塞。在主函数中，我使用了三个为同步线程运行的线程，而且在其中每个线程中都有一个可共享的资源。这些线程以向第一个对象获取封锁这种方式运行。但是当它试着向第二个对象获取封锁时，它就会进入等待状态，因为它已经被另一个线程封锁住了。这样，在线程引起死锁的过程中，就形成了一个依赖于资源的循环。当我执行上面的程序时，就产生了输出，但是程序却因为死锁无法停止。输出如清单 3 所示。

清单 3. 清单 2 运行输出

```
t1 acquiring lock on java.lang.Object@1dd3812
t1 acquired lock on java.lang.Object@1dd3812
t2 acquiring lock on java.lang.Object@c791b9
t2 acquired lock on java.lang.Object@c791b9
t3 acquiring lock on java.lang.Object@1aa9f99
t3 acquired lock on java.lang.Object@1aa9f99
t1 acquiring lock on java.lang.Object@c791b9
t2 acquiring lock on java.lang.Object@1aa9f99
```

在此我们可以清楚地在输出结果中辨认出死锁局面，但是在我们实际所用的应用中，发现死锁并将它排除是非常难的。
死锁情况诊断

JVM 提供了一些工具可以来帮助诊断死锁的发生，如下面程序清单 4 所示，我们实现了一个死锁，然后尝试通过 jstack 命令追踪、分析死锁发生。
清单 4. 死锁代码

```java
import java.util.concurrent.locks.ReentrantLock;

//下面演示一个简单的死锁，两个线程分别占用 south 锁和 north 锁，并同时请求对方占用的锁，导致死锁
public class DeadLock extends Thread {
    static ReentrantLock south = new ReentrantLock();
    static ReentrantLock north = new ReentrantLock();
    protected Object myDirect;

    public DeadLock(Object obj) {
        this.myDirect = obj;

        if (myDirect == south) {
            this.setName("south");
        } else {
            this.setName("north");
        }
    }

    @Override
    public void run() {
        if (myDirect == south) {
            try {
                north.lockInterruptibly(); //?? north

                try {
                    Thread.sleep(500);
                } catch (Exception ex) {
                    ex.printStackTrace();
                }

                south.lockInterruptibly();
                System.out.println("car to south has passed");
            } catch (InterruptedException ex) {
                System.out.println("car to south is killed");
                ex.printStackTrace();
            } finally {
                if (north.isHeldByCurrentThread()) {
                    north.unlock();
                }

                if (south.isHeldByCurrentThread()) {
                    south.unlock();
                }
            }
        }

        if (myDirect == north) {
            try {
                south.lockInterruptibly(); //?? south

                try {
                    Thread.sleep(500);
                } catch (Exception ex) {
                    ex.printStackTrace();
                }

                north.lockInterruptibly();
                System.out.println("car to north has passed");
            } catch (InterruptedException ex) {
                System.out.println("car to north is killed");
                ex.printStackTrace();
            } finally {
                if (north.isHeldByCurrentThread()) {
                    north.unlock();
                }

                if (south.isHeldByCurrentThread()) {
                    south.unlock();
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        DeadLock car2south = new DeadLock(south);
        DeadLock car2north = new DeadLock(north);
        car2south.start();
        car2north.start();
    }
}
```

jstack 可用于导出 Java 应用程序的线程堆栈，-l 选项用于打印锁的附加信息。我们运行 jstack 命令，输出入清单 5 和 6 所示，其中清单 5 里面可以看到线程处于运行状态，代码中调用了拥有锁投票、定时锁等候和可中断锁等候等特性的 ReentrantLock 锁机制。清单 6 直接打印出出现死锁情况，报告 north 和 sourth 两个线程互相等待资源，出现了死锁。

清单 5. jstack 运行输出 1

```
[root@facenode4 ~]# jstack -l 31274
2015-01-29 12:40:27
Full thread dump Java HotSpot(TM) 64-Bit Server VM (20.45-b01 mixed mode):

"Attach Listener" daemon prio=10 tid=0x00007f6d3c001000 nid=
            0x7a87 waiting on condition [0x0000000000000000]
 java.lang.Thread.State: RUNNABLE

 Locked ownable synchronizers:
 - None

"DestroyJavaVM" prio=10 tid=0x00007f6da4006800 nid=
            0x7a2b waiting on condition [0x0000000000000000]
 java.lang.Thread.State: RUNNABLE

 Locked ownable synchronizers:
 - None

"north" prio=10 tid=0x00007f6da4101800 nid=
            0x7a47 waiting on condition [0x00007f6d8963b000]
 java.lang.Thread.State: WAITING (parking)
 at sun.misc.Unsafe.park(Native Method)
 - parking to wait for <0x000000075903c7c8> (
                            a java.util.concurrent.locks.ReentrantLock$NonfairSync)
 at java.util.concurrent.locks.LockSupport.park(LockSupport.java:156)
 at java.util.concurrent.locks.AbstractQueuedSynchronizer.
                            parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:811)
 at java.util.concurrent.locks.AbstractQueuedSynchronizer.
                            doAcquireInterruptibly(AbstractQueuedSynchronizer.java:867)
 at java.util.concurrent.locks.AbstractQueuedSynchronizer.
                            acquireInterruptibly(AbstractQueuedSynchronizer.java:1201)
 at java.util.concurrent.locks.ReentrantLock.lockInterruptibly(ReentrantLock.java:312)
 at DeadLock.run(DeadLock.java:50)

 Locked ownable synchronizers:
 - <0x000000075903c798> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)

"south" prio=10 tid=0x00007f6da4100000 nid=
                        0x7a46 waiting on condition [0x00007f6d8973c000]
 java.lang.Thread.State: WAITING (parking)
 at sun.misc.Unsafe.park(Native Method)
 - parking to wait for <0x000000075903c798> (
                                        a java.util.concurrent.locks.ReentrantLock$NonfairSync)
 at java.util.concurrent.locks.LockSupport.park(LockSupport.java:156)
 at java.util.concurrent.locks.AbstractQueuedSynchronizer.
                                    parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:811)
 at java.util.concurrent.locks.AbstractQueuedSynchronizer.
                                    doAcquireInterruptibly(AbstractQueuedSynchronizer.java:867)
 at java.util.concurrent.locks.AbstractQueuedSynchronizer.
                                     acquireInterruptibly(AbstractQueuedSynchronizer.java:1201)
 at java.util.concurrent.locks.ReentrantLock.lockInterruptibly(ReentrantLock.java:312)
 at DeadLock.run(DeadLock.java:28)

 Locked ownable synchronizers:
 - <0x000000075903c7c8> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)

"Low Memory Detector" daemon prio=10 tid=0x00007f6da40d2800 nid=
                                    0x7a44 runnable [0x0000000000000000]
 java.lang.Thread.State: RUNNABLE

 Locked ownable synchronizers:
 - None

"C2 CompilerThread1" daemon prio=10 tid=0x00007f6da40d0000 nid=
                                    0x7a43 waiting on condition [0x0000000000000000]
 java.lang.Thread.State: RUNNABLE

 Locked ownable synchronizers:
 - None

"C2 CompilerThread0" daemon prio=10 tid=0x00007f6da40cd000 nid=
                                    0x7a42 waiting on condition [0x0000000000000000]
 java.lang.Thread.State: RUNNABLE

 Locked ownable synchronizers:
 - None

"Signal Dispatcher" daemon prio=10 tid=0x00007f6da40cb000 nid=
                                    0x7a41 runnable [0x0000000000000000]
 java.lang.Thread.State: RUNNABLE

 Locked ownable synchronizers:
 - None

"Finalizer" daemon prio=10 tid=0x00007f6da40af000 nid=
                                      0x7a40 in Object.wait() [0x00007f6d89d44000]
 java.lang.Thread.State: WAITING (on object monitor)
 at java.lang.Object.wait(Native Method)
 - waiting on <0x0000000759001300> (a java.lang.ref.ReferenceQueue$Lock)
 at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:118)
 - locked <0x0000000759001300> (a java.lang.ref.ReferenceQueue$Lock)
 at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:134)
 at java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:171)

 Locked ownable synchronizers:
 - None

"Reference Handler" daemon prio=10 tid=0x00007f6da40ad000 nid=
                                       0x7a3f in Object.wait() [0x00007f6d89e45000]
 java.lang.Thread.State: WAITING (on object monitor)
 at java.lang.Object.wait(Native Method)
 - waiting on <0x00000007590011d8> (a java.lang.ref.Reference$Lock)
 at java.lang.Object.wait(Object.java:485)
 at java.lang.ref.Reference$ReferenceHandler.run(Reference.java:116)
 - locked <0x00000007590011d8> (a java.lang.ref.Reference$Lock)

 Locked ownable synchronizers:
 - None

"VM Thread" prio=10 tid=0x00007f6da40a6000 nid=0x7a3e runnable 

"GC task thread#0 (ParallelGC)" prio=10 tid=0x00007f6da4019800 nid=0x7a2c runnable 

"GC task thread#1 (ParallelGC)" prio=10 tid=0x00007f6da401b000 nid=0x7a2d runnable 

"GC task thread#2 (ParallelGC)" prio=10 tid=0x00007f6da401d000 nid=0x7a2e runnable 

"GC task thread#3 (ParallelGC)" prio=10 tid=0x00007f6da401f000 nid=0x7a2f runnable 

"GC task thread#4 (ParallelGC)" prio=10 tid=0x00007f6da4020800 nid=0x7a30 runnable 

"GC task thread#5 (ParallelGC)" prio=10 tid=0x00007f6da4022800 nid=0x7a31 runnable 

"GC task thread#6 (ParallelGC)" prio=10 tid=0x00007f6da4024800 nid=0x7a32 runnable 

"GC task thread#7 (ParallelGC)" prio=10 tid=0x00007f6da4026000 nid=0x7a33 runnable 

"GC task thread#8 (ParallelGC)" prio=10 tid=0x00007f6da4028000 nid=0x7a34 runnable 

"GC task thread#9 (ParallelGC)" prio=10 tid=0x00007f6da402a000 nid=0x7a35 runnable 

"GC task thread#10 (ParallelGC)" prio=10 tid=0x00007f6da402b800 nid=0x7a36 runnable 

"GC task thread#11 (ParallelGC)" prio=10 tid=0x00007f6da402d800 nid=0x7a37 runnable 

"GC task thread#12 (ParallelGC)" prio=10 tid=0x00007f6da402f800 nid=0x7a38 runnable 

"GC task thread#13 (ParallelGC)" prio=10 tid=0x00007f6da4031000 nid=0x7a39 runnable 

"GC task thread#14 (ParallelGC)" prio=10 tid=0x00007f6da4033000 nid=0x7a3a runnable 

"GC task thread#15 (ParallelGC)" prio=10 tid=0x00007f6da4035000 nid=0x7a3b runnable 

"GC task thread#16 (ParallelGC)" prio=10 tid=0x00007f6da4036800 nid=0x7a3c runnable 

"GC task thread#17 (ParallelGC)" prio=10 tid=0x00007f6da4038800 nid=0x7a3d runnable 

"VM Periodic Task Thread" prio=10 tid=0x00007f6da40dd000 nid=0x7a45 waiting on condition 

JNI global references: 886

清单 6. jstack 运行输出片段 2
WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 0 page no 843102 n bits 600 index `KEY_TSKTASK_MONTIME2` of table
        `dcnet_db/TSK_TASK` trx id 0 677833454 lock_mode X locks rec but not gap waiting
Record lock, heap no 395 PHYSICAL RECORD: n_fields 3; compact format; info bits 0
0: len 8; hex 8000000000000425; asc %;; 1: len 8; hex 800012412c66d29c; 
                    asc A,f ;; 2: len 8; hex 800000000097629c; asc b ;;

*** WE ROLL BACK TRANSACTION (1)
```

我们假设涉事的数据表上面有一个索引，这次的死锁就是由于两条记录同时访问到了相同的索引造成的。

我们首先来看看 InnoDB 类型的数据表，只要能够解决索引问题，就可以解决死锁问题。MySQL 的 InnoDB 引擎是行级锁，需要注意的是，这不是对记录进行锁定，而是对索引进行锁定。在 UPDATE、DELETE 操作时，MySQL 不仅锁定 WHERE 条件扫描过的所有索引记录，而且会锁定相邻的键值，即所谓的 next-key locking；

如语句 UPDATE TSK_TASK SET UPDATE_TIME = NOW() WHERE ID > 10000 会锁定所有主键大于等于 1000 的所有记录，在该语句完成之前，你就不能对主键等于 10000 的记录进行操作；当非簇索引 (non-cluster index) 记录被锁定时，相关的簇索引 (cluster index) 记录也需要被锁定才能完成相应的操作。

再分析一下发生问题的两条 SQL 语句：
当“update TSK_TASK set STATUS_ID=1064,UPDATE_TIME=now () where STATUS_ID=1061 and MON_TIME&lt;date_sub(now(), INTERVAL 30 minute)”执行时，MySQL 会使用 KEY_TSKTASK_MONTIME2 索引，因此首先锁定相关的索引记录，因为 KEY_TSKTASK_MONTIME2 是非簇索引，为执行该语句，MySQL 还会锁定簇索引（主键索引）。

假设“update TSK_TASK set STATUS_ID=1067,UPDATE_TIME=now () where ID in (9921180)”几乎同时执行时，本语句首先锁定簇索引 (主键)，由于需要更新 STATUS_ID 的值，所以还需要锁定 KEY_TSKTASK_MONTIME2 的某些索引记录。

这样第一条语句锁定了 KEY_TSKTASK_MONTIME2 的记录，等待主键索引，而第二条语句则锁定了主键索引记录，而等待 KEY_TSKTASK_MONTIME2 的记录，这样死锁就产生了。

我们通过拆分第一条语句解决了死锁问题：即先查出符合条件的 ID：

```
select ID from TSK_TASK where STATUS_ID=1061 and MON_TIME < date_sub(now(), INTERVAL 30 minute)；
```
然后再更新状态：
```
update TSK_TASK set STATUS_ID=1064 where ID in (….);
```

##**结束语**

我们发现，死锁虽然是较早就被发现的问题，但是很多情况下我们设计的程序里还是经常发生死锁情况。我们不能只是分析如何解决死锁这类问题，还需要具体找出预防死锁的方法，这样才能从根本上解决问题。总的来说，还是需要系统架构师、程序员不断积累经验，从业务逻辑设计层面彻底消除死锁发生的可能性。