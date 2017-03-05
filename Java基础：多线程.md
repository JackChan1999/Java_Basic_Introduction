# 第一讲 多线程概述

## 1.多线程引入

由上图中程序的调用流程可知，这个程序只有一个执行流程，所以这样的程序就是单线程程序。假如一个程序有多条执行流程，那么，该程序就是多线程程序。

## 2.多线程概述

### 什么是进程？
进程就是正在运行的程序，是系统进行资源分配和调用的独立单位。每一个进程都有它自己的内存空间和系统资源。

### 多进程有什么意义呢?

单进程的计算机只能做一件事情，而我们现在的计算机都可以做多件事情。举例：一边玩游戏(游戏进程)，一边听音乐(音乐进程)。也就是说现在的计算机都是支持多进程的，可以在一个时间段内执行多个任务。并且呢，可以提高CPU的使用率，解决了多部分代码同时运行的问题。
其实，多个应用程序同时执行都是CPU在做着快速的切换完成的。这个切换是随机的。CPU的切换是需要花费时间的，从而导致了效率的降低。

### 什么是线程?

线程是程序的执行单元，执行路径；是进程中的单个顺序控制流，是一条执行路径；一个进程如果只有一条执行路径，则称为单线程程序。一个进程如果有多条执行路径，则称为多线程程序。

### 多线程有什么意义呢?

多线程的存在，不是提高程序的执行速度。其实是为了提高应用程序的使用率。程序的执行其实都是在抢CPU的资源，CPU的执行权。多个进程是在抢这个资源，而其中的某一个进程如果执行路径比较多，就会有更高的几率抢到CPU的执行权。我们是不敢保证哪一个线程能够在哪个时刻抢到，所以线程的执行有随机性。

### 什么是并行、并发呢？

前者是逻辑上同时发生，指在某一个时间内同时运行多个程序；后者是物理上同时发生，指在某一个时间点同时运行多个程序。那么，我们能不能实现真正意义上的并发呢？答案是可以的，多个CPU就可以实现，不过你得知道如何调度和控制它们。

PS：

一个进程中可以有多个执行路径，称之为多线程。
一个进程中至少要有一个线程。
开启多个线程是为了同时运行多部分代码，每一个线程都有自己运行的内容，这个内容可以称为线程要执行的任务。

## 3.Java程序运行原理

java 命令会启动 java 虚拟机，启动 JVM，等于启动了一个应用程序，也就是启动了一个进程。该进程会自动启动一个 “主线程” ，然后主线程去调用某个类的 main 方法。所以 main方法运行在主线程中。在此之前的所有程序都是单线程的。

思考：JVM虚拟机的启动是单线程的还是多线程的？

答案：JVM启动时启动了多条线程，至少有两个线程可以分析的出来

- 执行main函数的线程，该线程的任务代码都定义在main函数中。

- 负责垃圾回收的线程。System类的gc方法告诉垃圾回收器调用finalize方法，但不一定立即执行。

# 第二讲 多线程的实现方案

由于线程是依赖进程而存在的，所以我们应该先创建一个进程出来。而进程是由系统创建的，所以我们应该去调用系统功能创建一个进程。Java是不能直接调用系统功能的，所以，我们没有办法直接实现多线程程序。但是呢?Java可以去调用C/C++写好的程序来实现多线程程序。由C/C++去调用系统功能创建进程，然后由Java去调用这样的东西，然后提供一些类供我们使用。我们就可以实现多线程程序了。

## 1、多线程的实现方案一：继承Thread类，重写run()方法

- 定义一个类继承Thread类。
- 覆盖Thread类中的run方法。
- 直接创建Thread的子类对象创建线程。
- 调用start方法开启线程并调用线程的任务run方法执行。

```java
package cn.itcast;

//多线程的实现方案一：继承Thread类，重写run()方法
//1、定义一个类继承Thread类。
class MyThread extends Thread {
    private String name;

    MyThread(String name) {
        this.name = name;
    }

    // 2、覆盖Thread类中的run方法。
    public void run() {
        for (int x = 0; x < 5; x++) {
            System.out.println(name + "...x=" + x + "...ThreadName="
                    + Thread.currentThread().getName());
        }
    }
}

class ThreadTest {
    public static void main(String[] args) {
        // 3、直接创建Thread的子类对象创建线程。
        MyThread d1 = new MyThread("黑马程序员");
        MyThread d2 = new MyThread("中关村在线");
        // 4、调用start方法开启线程并调用线程的任务run方法执行。
        d1.start();
        d2.start();
        for (int x = 0; x < 5; x++) {
            System.out.println("x = " + x + "...over..."
                    + Thread.currentThread().getName());
        }
    }
}
```

运行结果：

### 为什么要重写run()方法？

Thread类用于描述线程，线程是需要任务的。所以Thread类也有对任务的描述。这个任务就是通过Thread类中的run方法来体现。也就是说，run方法就是封装自定义线程运行任务的函数，run方法中定义的就是线程要运行的任务代码。所以只有继承Thread类，并复写run方法，将运行的代码定义在run方法中即可。

### 启动线程使用的是那个方法

启动线程调用的是start()方法，不是run()方法，run()方法只是封装了被线程执行的代码，调用run()只是普通方法的调用，无法启动线程。

### 线程能不能多次启动

不能，会出现IllegalThreadStateException非法的线程状态异常。

### run()和start()方法的区别：

run()：仅仅是封装了被线程执行的代码，直接调用就是普通方法。
start()：首先是启动了线程，然后再由jvm去调用了该线程的run()方法

### Thread类的基本获取和设置方法

- public final String getName()：获取线程的名称
- public final void setName(String name)：设置线程的名称
- Thread(String name) ：通过构造方法给线程起名字

思考：如何获取main方法所在的线程名称呢?

```java
public static Thread currentThread() // 获取任意方法所在的线程名称
```

## 2、多线程的实现方案二：实现Runnable接口

- 定义类实现Runnable接口。
- 覆盖接口中的run方法，将线程的任务代码封装到run方法中。
- 通过Thread类创建线程对象，并将Runnable接口的子类对象作为Thread类的构造函数的参数进行传递。为什么？因为线程的任务都封装在Runnable接口子类对象的run方法中。所以要在线程对象创建时就必须明确要运行的任务。
- 调用线程对象的start方法开启线程。

```java
package cn.itcast;

//多线程的实现方案二：实现Runnable接口
//1、定义类实现Runnable接口。
class MyThread implements Runnable {
    // 2、覆盖接口中的run方法，将线程的任务代码封装到run方法中。
    public void run() {
        show();
    }

    public void show() {
        for (int x = 0; x < 5; x++) {
            System.out.println(Thread.currentThread().getName() + "..." + x);
        }
    }
}

class ThreadTest {
    public static void main(String[] args) {
        MyThread d = new MyThread();
        // 3、通过Thread类创建线程对象，并将Runnable接口的子类对象作为Thread类的构造函数的参数进行传递。
        Thread t1 = new Thread(d);
        Thread t2 = new Thread(d);
        // 4、调用线程对象的start方法开启线程。
        t1.start();
        t2.start();
    }
}
```
运行结果：

- 如何获取线程名称：Thread.currentThread().getName()
- 如何给线程设置名称：setName()、Thread(Runnable target, String name)
- 实现接口方式的好处：
  - 可以避免由于Java单继承带来的局限性，所以，创建线程的第二种方式较为常用。
  - 适合多个相同程序的代码去处理同一个资源的情况，把线程同程序的代码，数据有效分离，较好的体现了面向对象的设计思想。

## 3、多线程程序实现方案三：实现Callable接口

- 创建一个线程池对象，控制要创建几个线程对象。

```java
public static ExecutorService newFixedThreadPool(int nThreads)
```

- 这种线程池的线程可以执行：
可以执行Runnable对象或者Callable对象代表的线程。
- 调用如下方法即可
    - Future<?> submit(Runnable task)：
      提交一个 Runnable 任务用于执行，并返回一个表示该任务的 Future。
    - <T> Future<T> submit(Callable<T> task)：    
      提交一个返回值的任务用于执行，返回一个表示任务的未决结果的 Future
- 结束线程：shutdown()：关闭线程

```java
package cn.itcast;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import java.util.concurrent.Callable;

//Callable:是带泛型的接口。
//这里指定的泛型其实是call()方法的返回值类型。
class MyCallable implements Callable {

    public Object call() throws Exception {
        for (int x = 0; x < 100; x++) {
            System.out.println(Thread.currentThread().getName() + ":" + x);
        }
        return null;
    }

}

/*
 * 多线程实现的方式3： A:创建一个线程池对象，控制要创建几个线程对象。 public static ExecutorService
 * newFixedThreadPool(int nThreads) B:这种线程池的线程可以执行：
 * 可以执行Runnable对象或者Callable对象代表的线程 做一个类实现Runnable接口。 C:调用如下方法即可 Future<?>
 * submit(Runnable task) <T> Future<T> submit(Callable<T> task) D:我就要结束，可以吗? 可以。
 */
public class CallableDemo {
    public static void main(String[] args) {
        // 创建线程池对象
        ExecutorService pool = Executors.newFixedThreadPool(2);

        // 可以执行Runnable对象或者Callable对象代表的线程
        pool.submit(new MyCallable());
        pool.submit(new MyCallable());

        // 结束
        pool.shutdown();
    }
}
```

运行结果：


实现Callable的优缺点
  
- 好处：可以有返回值；可以抛出异常。
- 弊端：代码比较复杂，所以一般不用

## 4、匿名内部类方式使用多线程

```java
new Thread(){代码…}.start(); //新创建一个线程并启动
new Thread(new Runnable(){代码…}).start(); //新创建一个线程并启动
```

# 第三讲 线程调度和线程控制

## 1、线程调度

假如我们的计算机只有一个 CPU，那么 CPU 在某一个时刻只能执行一条指令，线程只有得到 CPU时间片，也就是使用权，才可以执行指令。那么Java是如何对线程进行调用的呢？

### 线程有两种调度模型：
分时调度模型：所有线程轮流使用 CPU 的使用权，平均分配每个线程占用 CPU 的时间片
抢占式调度模型：优先让优先级高的线程使用 CPU，如果线程的优先级相同，那么会随机选择一个，优先级高的线程获取的 CPU 时间片相对多一些。
Java使用的是抢占式调度模型。

### 如何设置和获取线程优先级

```java
public final intgetPriority(); //获取线程的优先级
public final voidsetPriority(int newPriority); //设置线程的优先级
```

注意：线程默认的优先级是5；线程优先级的范围是：1-10；线程优先级高仅仅表示线程获取CPU的时间片的几率高，但是要在次数比较多，或者多次运行的时候才能卡到比较好的结果。

## 2、线程控制


# 第四讲 线程的生命周期

## 1、线程的状态


##　2、线程的生命周期图


#　第五讲 线程安全问题

## 1、判断一个程序是否会有线程安全问题的标准：

- 是否是多线程环境
- 是否有共享数据
- 是否有多条语句操作共享数据

## 2、如何解决多线程安全问题呢?

基本思想：让程序没有安全问题的环境。
解决办法：同步机制：同步代码块、同步方法。把多个语句操作共享数据的代码给锁起来，让任意时刻只能有一个线程执行即可。

### 解决线程安全问题实现1：同步代码块，格式如下

```java
synchronized(对象){
  需要同步的代码;
}
```

```java
package cn.itcast;

//卖票程序的同步代码块实现示例 
class Ticket implements Runnable {
    private int num = 10;
    Object obj = new Object();

    public void run() {
        while (true) {
            // 给可能出现问题的代码加锁
            synchronized (obj) {
                if (num > 0) {
                    // 显示线程名及余票数
                    System.out.println(Thread.currentThread().getName()
                            + "...sale..." + num--);
                }
            }
        }
    }
}

class TicketDemo {
    public static void main(String[] args) {
        // 通过Thread类创建线程对象，并将Runnable接口的子类对象作为Thread类的构造函数的参数进行传递。
        Ticket t = new Ticket();
        Thread t1 = new Thread(t);
        Thread t2 = new Thread(t);
        Thread t3 = new Thread(t);
        Thread t4 = new Thread(t);
        // 调用线程对象的start方法开启线程。
        t1.start();
        t2.start();
        t3.start();
        t4.start();
    }
}
```

运行结果：

- 同步可以解决安全问题的根本原因就在那个对象上。该对象如同锁的功能。
- 同步代码块的对象可以是哪些呢? 可以是任意对象，但每个线程都必须是同一对象。
- 同步的前提：多个线程；多个线程使用的是同一个锁对象
- 同步的好处：同步的出现解决了多线程的安全问题。
- 同步的弊端：当线程相当多时，因为每个线程都会去判断同步上的锁，这是很耗费资源的，无形中会降低程序的运行效率。

## 解决线程安全问题实现2：同步方法   

同步方法：就是把同步关键字加到方法上

```java
package cn.itcast;

//卖票程序的同步代码块实现示例 
class Ticket implements Runnable {
    // 定义100张票
    private static int tickets = 10;

    Object obj = new Object();

    public void run() {
        while (true) {
            sellTicket();
        }
    }

    private synchronized void sellTicket() {
        if (tickets > 0) {
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "正在出售第"
                    + (tickets--) + "张票 ");
        }
    }
}

class TicketDemo {
    public static void main(String[] args) {
        // 通过Thread类创建线程对象，并将Runnable接口的子类对象作为Thread类的构造函数的参数进行传递。
        Ticket t = new Ticket();
        Thread t1 = new Thread(t);
        Thread t2 = new Thread(t);
        Thread t3 = new Thread(t);
        Thread t4 = new Thread(t);
        // 调用线程对象的start方法开启线程。
        t1.start();
        t2.start();
        t3.start();
        t4.start();
    }
}
```

运行结果：

PS：

- 同步方法的锁对象是什么呢?this对象
- 如果是静态方法，同步方法的锁对象又是什么呢?该类的字节码文件（类的class文件）
- 那么，我们到底使用同步方法还是同步代码块？

如果锁对象是this，就可以考虑使用同步方法。否则能使用同步代码块的尽量使用同步代码块。

### 线程安全与非线程安全的类

某些线程安全的类：StringBuffer、Vector、HashTable，虽然线程安全，但是效率较低，我们一般不用，而用Collections工具类解决线程安全的问题。
List<String> list = Colletions.syncrinizedList(new ArrayList<String>())：获取线程安全的List集合

### JDK5中Lock锁的使用
虽然我们可以理解同步代码块和同步方法的锁对象问题，但是我们并没有直接看到在哪里加上了锁，在哪里释放了锁，为了更清晰的表达如何加锁和释放锁，JDK5以后提供了一个新的锁对象Lock
Lock接口：Lock 实现提供了比使用 synchronized 方法和语句可获得的更广泛的锁定操作，此实现允许更灵活的结构。

- void lock()：获取锁

- void unlock()：释放锁

- ReentrantLock：Lock的实现类

```java
package cn.itcast;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class SellTicket implements Runnable {

    // 定义票
    private int tickets = 100;

    // 定义锁对象
    private Lock lock = new ReentrantLock();

    public void run() {
        while (true) {
            try {
                // 加锁
                lock.lock();
                if (tickets > 0) {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName()
                            + "正在出售第" + (tickets--) + "张票");
                }
            } finally {
                // 释放锁
                lock.unlock();
            }
        }
    }

}

/*
 * 虽然我们可以理解同步代码块和同步方法的锁对象问题，但是我们并没有直接看到在哪里加上了锁，在哪里释放了锁，
 * 为了更清晰的表达如何加锁和释放锁,JDK5以后提供了一个新的锁对象Lock。
 * 
 * Lock： void lock()： 获取锁。 void unlock():释放锁。 ReentrantLock是Lock的实现类.
 */
public class SellTicketDemo {
    public static void main(String[] args) {
        // 创建资源对象
        SellTicket st = new SellTicket();

        // 创建三个窗口
        Thread t1 = new Thread(st, "窗口1");
        Thread t2 = new Thread(st, "窗口2");
        Thread t3 = new Thread(st, "窗口3");

        // 启动线程
        t1.start();
        t2.start();
        t3.start();
    }
}
```

运行结果：

# 六、死锁

同步弊端：效率低，如果出现了同步嵌套，就容易产生死锁问题。
死锁问题：是指两个或者两个以上的线程在执行的过程中，因争夺资源产生的一种互相等待现象

```java
package cn.itcast;

class Ticket implements Runnable {
    private static int num = 100;
    Object obj = new Object();
    boolean flag = true;

    public void run() {
        if (flag) {
            while (true) {
                synchronized (obj) {
                    show();
                }
            }
        } else
            while (true)
                show();
    }

    public synchronized void show() {
        synchronized (obj) {
            if (num > 0) {
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName()
                        + "...function..." + num--);
            }
        }
    }
}

class DeadLockDemo {
    public static void main(String[] args) {
        Ticket t = new Ticket();
        Thread t1 = new Thread(t);
        Thread t2 = new Thread(t);

        t1.start();
        try {
            Thread.sleep(10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        t.flag = false;
        t2.start();
    }
}
```

运行结果：

原因分析：

由上图可以看到程序已经被锁死，无法向下执行。run方法中的同步代码块需要获取obj对象锁，才能执行代码块中的show方法。而执行show方法则必须获取this对象锁，然后才能执行其中的同步代码块。当线程t1获取到obj对象锁执行同步代码块，线程t2获取到this对象锁执行show方法。同步代码块中的show方法因无法获取到this对象锁无法执行，show方法中的同步代码块因无法获取到obj对象锁无法执行，就会产生死锁。

# 七、线程间通信

多个线程在处理统一资源，但是任务却不同，这时候就需要线程间通信。为了实现线程间的通信Java提供了等待唤醒机制。
等待/唤醒机制涉及的方法：

- wait()：让线程处于冻结状态，被wait的线程会被存储到线程池中。

- notify()：唤醒线程池中的一个线程（任何一个都有可能）。

- notifyAll()：唤醒线程池中的所有线程。

PS：

- 这些方法都必须定义在同步中，因为这些方法是用于操作线程状态的方法。
- 必须要明确到底操作的是哪个锁上的线程！
- wait和sleep区别？
            

4、为什么操作线程的方法wait、notify、notifyAll定义在了object类中，因为这些方法是监视器的方法，监视器其实就是锁。锁可以是任意的对象，任意的对象调用的方式一定在object类中。
生产者-消费者问题：

```java
package cn.itcast;

class Student {
    String name;
    int age;
    boolean flag;
}

class SetThread implements Runnable {

    private Student s;
    private int x = 0;

    public SetThread(Student s) {
        this.s = s;
    }

    public void run() {
        while (true) {
            synchronized (s) {
                // 判断有没有
                if (s.flag) {
                    try {
                        s.wait(); // t1等着，释放锁
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

                if (x % 2 == 0) {
                    s.name = "张三";
                    s.age = 15;
                } else {
                    s.name = "李四";
                    s.age = 16;
                }
                x++; // x=1

                // 修改标记
                s.flag = true;
                // 唤醒线程
                s.notify(); // 唤醒t2,唤醒并不表示你立马可以执行，必须还得抢CPU的执行权。
            }
            // t1有，或者t2有
        }
    }
}

class GetThread implements Runnable {
    private Student s;

    public GetThread(Student s) {
        this.s = s;
    }

    public void run() {
        while (true) {
            synchronized (s) {
                if (!s.flag) {
                    try {
                        s.wait(); // t2就等待了。立即释放锁。将来醒过来的时候，是从这里醒过来的时候
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

                System.out.println(s.name + "---" + s.age);
                // 林青霞---27
                // 刘意---30

                // 修改标记
                s.flag = false;
                // 唤醒线程
                s.notify(); // 唤醒t1
            }
        }
    }
}

public class StudentDemo {
    public static void main(String[] args) {
        // 创建资源
        Student s = new Student();

        // 设置和获取的类
        SetThread st = new SetThread(s);
        GetThread gt = new GetThread(s);

        // 线程类
        Thread t1 = new Thread(st);
        Thread t2 = new Thread(gt);

        // 启动线程
        t1.start();
        t2.start();
    }
}
```

运行结果：

## 1、线程组

  Java中使用ThreadGroup来表示线程组，它可以对一批线程进行分类管理，Java允许程序直接对线程组进行控制。默认情况下，所有的线程都属于主线程组。
  public final ThreadGroup getThreadGroup()：返回该线程所属的线程组。
  Thread(ThreadGroup group,Runnable target, String name)：给线程设置分组
##　2、线程池

程序启动一个新线程成本是比较高的，因为它涉及到要与操作系统进行交互。而使用线程池可以很好的提高性能，尤其是当程序中要创建大量生存期很短的线程时，更应该考虑使用线程池。

- 线程池里的每一个线程代码结束后，并不会死亡，而是再次回到线程池中成为空闲状态，等待下一个对象来使用。
- 在JDK5之前，我们必须手动实现自己的线程池，从JDK5开始，Java内置支持线程池。JDK5新增了一个Executors工厂类来产生线程池，有如下几个方法
    - public static ExecutorService newCachedThreadPool()：
    创建一个线程池对象
    - public static ExecutorService newFixedThreadPool(int nThreads)：
    创建一个线程池对象，控制要创建几个线程
    - public static ExecutorService newSingleThreadExecutor()：
    创建一个使用单个 worker 线程的 Executor
    这些方法的返回值是ExecutorService对象，该对象表示一个线程池，可以执行Runnable对象或者Callable对象代表的线程。它提供了如下方法：
    - Future<?>submit(Runnable task)
    提交一个 Runnable 任务用于执行，并返回一个表示该任务的 Future
    - <T>Future<T>  submit(Callable<T>task)
    提交一个返回值的任务用于执行，返回一个表示任务的未决结果的 Future。

#　八、定时器的使用

定时器是一个应用十分广泛的线程工具，可用于调度多个定时任务以后台线程的方式执行。在Java中，可以通过Timer和TimerTask类来实现定义调度的功能
Timer：一种工具，线程用其安排以后在后台线程中执行的任务。可安排任务执行一次，或者定期重复执行。
 
实际开发中使用的是Quartz：一个完全由java编写的开源调度框架。

#　九、多线程总结

# 多线程

## 三种多选实现方案

1. 继承Thread类，重写run()方法。
2. 实现Runnable接口，new Thread(new Runnable(){…}){…};
3. 实现Callable接口。和线程池结合。

## 实现Runnable好处 

1. 将线程的任务从线程的子类中分离出来，进行了单独的封装，实现数据和程序分离，

按照面向对象的思想将任务封装成对象。

2. 避免了Java单继承的局限性。所以，创建线程的第二种方式较为常用。
 
## 线程间的通信
- 多个线程在处理同一资源，但是任务却不同，这时候就需要线程间通信。
- 等待/唤醒机制涉及的方法
  - wait()：让线程处于冻结状态，被wait的线程会被存储到线程池中。
  - notify()：唤醒线程池中的一个线程（任何一个都有可能）。
  - notifyAll()：唤醒线程池中的所有线程。

## wait、sleep区别

 1. wait可以指定时间也可以不指定。sleep必须指定时间。
 2. 在同步中时，对CPU的执行权和锁的处理不同。
   wait：释放CPU执行权，释放锁。Object中的方法。
   sleep：释放CPU执行权，不释放锁。Thread中的方法。
 3. sleep必需捕获异常，wait，notify，notifyAll不需要捕获异常
 
## 常用方法

- 1. String getName()：获取线程的名称
- 2. void setName(String name)：设置线程的名称
- 3. static Thread currentThread()：获取当前正在执行的线程
- 4. int getPriority()：获取线程优先级
- 5. void setPriority(int newPriority)：设置线程优先级（1-10）
- 6. static void sleep(long millis)：线程休眠
- 7. void join()：线程加入，该线程执行完毕其他线程才可以执行
- 8. static void yield()：线程礼让
- 9. setDaemon(boolean on)：后台线程/守护线程
- 10.void stop( )：已过时，不建议使用；interrupt( )：中断线程

## 线程的生命周期
  新建，就绪，运行，阻塞（同步阻塞，等待阻塞，其他阻塞），死亡