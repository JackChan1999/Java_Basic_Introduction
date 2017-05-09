##1. Callable 
泛型接口，用于获取线程执行完的结果，Callable的声明如下

```java
public interface Callable<V> {
  	// 返回 V 类型的结果
    V call() throws Exception;
}
```

Callable 接口类似于Runnable，两者都是为那些其实例可能被另一个线程执行的类设计的。但是 Runnable 不会返回结果，并且无法抛出经过检查的异常，而Callable是可返回结果并且可以抛出异常的任务

```java
public class MyCallable implements Callable{
    private int number;
    public MyCallable(int number) {
        this.number = number;
    }

    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int x = 1; x <= number; x++) {
            sum += x;
        }
        return sum;
    }
}

public class CallableDemo {
    ExecutorService pool = Executors.newFixedThreadPool(2);
    Future<Integer> future = pool.submit(new SumCallable(10));
    Integer sum = future.get();
    pool.shutdown();

    class SumCallable implements Callable<Integer> {
        private int number;
        public SumCallable(int number){
            this.number = number;
        }

        @Override
        public Integer call() throws Exception {
            int sum = 0;
            for (int i=0; i<number; i++){
                sum += i;
            }
            return sum;
        }
    }
}
```

##2. Future 

Future 为线程池提供了一个可管理的任务标准，它提供了对Runnable或Callable任务的执行结果进行取消、查询是否完成、获取结果、设置结果操作

Future 接口表示异步计算的结果。它提供了检查计算是否完成的方法，以等待计算的完成，并获取计算的结果。计算完成后只能使用get()方法来获取结果，如有必要，计算完成前可以阻塞此方法

Future取得的结果类型和Callable返回的结果类型必须一致，这是通过泛型来实现的。Callable要采用ExecutorService的submit()方法提交，返回的future对象可以取消任务。Future声明如下

```java
public interface Future<V> {
    boolean cancel(boolean var1);
	// 该任务是否已经取消
    boolean isCancelled();
	// 判断是否已经完成
    boolean isDone();
	// 获取结果，该方法会阻塞
    V get() throws InterruptedException, ExecutionException;
	// 获取结果，如果还未完成那么等待，直到timeout或返回结果，该方法会阻塞
    V get(long var1, TimeUnit var3) throws InterruptedException, ExecutionException, TimeoutException;
}
```
Future的简单使用
```java
// 创建线程池对象
ExecutorService pool = Executors.newFixedThreadPool(2);
// 可以执行Runnable对象或者Callable对象代表的线程
Future<Integer> future = pool.submit(new MyCallable(100));
Integer i1 = future.get();
pool.shutdown();
```
Future常用方法

| 方法声明         | 功能描述                     |
| :----------- | :----------------------- |
| cancel()     | 取消任务                     |
| isCanceled() | 任务是否取消                   |
| isDone()     | 任务是否完成                   |
| get()        | 获取结果，get()方法会阻塞，直到任务返回结果 |
| set()        | 设置结果                     |

## 3. FutureTask

Future只是定义了一些接口规范，而FutureTask则是它的实现类

```java
public class FutureTask<V> implements RunnableFuture<V>{
	// 代码省略
}

public interface RunnableFuture<V> extends Runnable, Future<V>{
  	void run();
}
```

FutureTask会像Thread包装Runnable那样对Runnable和Callable<V>进行包装，Runnable和Callable由构造函数注入

```java
public FutureTask(Callable<V> callable) {
    if (callable == null)
        throw new NullPointerException();
    this.callable = callable;
    this.state = NEW;       // ensure visibility of callable
}
```

```java
public FutureTask(Runnable runnable, V result) {
    this.callable = Executors.callable(runnable, result);
    this.state = NEW;       // ensure visibility of callable
}
```

由于FutureTask实现了Runnable，因此，它既可以通过Thread包装来直接执行，也可以提交给ExecuteService来执行，并且还可以直接通过get()方法获取执行结果，该方法会阻塞，直到结果返回。因此，FutureTask既是Future、Runnable，又是包装了Callable，它是两者的合体

```java
public class FutureDemo {
    // 线程池
    static ExecutorService mExecutor = Executors.newSingleThreadExecutor();

    // main函数
    public static void main(String[] args) {
        try {
            futureWithRunnable();
            futureWithCallable();
            futureTask();
        } catch (Exception e) {
        }
    }

    /**
     * 其中Runnable实现的是void run()方法，无返回值；Callable实现的是 V
     * call()方法，并且可以返回执行结果。其中Runnable可以提交给Thread来包装下
     * ，直接启动一个线程来执行，而Callable则一般都是提交给ExecuteService来执行。
     */
    private static void futureWithRunnable() throws InterruptedException, ExecutionException {
        // 提交runnable则没有返回值, future没有数据
        Future<?> result = mExecutor.submit(new Runnable() {

            @Override
            public void run() {
                fibc(20);
            }
        });

        System.out.println("future result from runnable : " + result.get());
    }

    private static void futureWithCallable() throws InterruptedException, ExecutionException {
        /**
         * 提交Callable, 有返回值, future中能够获取返回值
         */
        Future<Integer> result2 = mExecutor.submit(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                return fibc(20);
            }
        });

        System.out.println("future result from callable : "
                + result2.get());
    }

    private static void futureTask() throws InterruptedException, ExecutionException {
        /**
         * FutureTask则是一个RunnableFuture<V>，即实现了Runnbale又实现了Futrue<V>这两个接口，
         * 另外它还可以包装Runnable(实际上会转换为Callable)和Callable
         * <V>，所以一般来讲是一个符合体了，它可以通过Thread包装来直接执行，也可以提交给ExecuteService来执行
         * ，并且还可以通过v get()返回执行结果，在线程体没有执行完成的时候，主线程一直阻塞等待，执行完则直接返回结果。
         */
        FutureTask<Integer> futureTask = new FutureTask<Integer>(
                new Callable<Integer>() {
                    @Override
                    public Integer call() throws Exception {
                        return fibc(20);
                    }
                });
        // 提交futureTask
        mExecutor.submit(futureTask);
        System.out.println("future result from futureTask : "
                + futureTask.get());
    }

    // 效率底下的斐波那契数列, 耗时的操作
    private static int fibc(int num) {
        if (num == 0) {
            return 0;
        }
        if (num == 1) {
            return 1;
        }
        return fibc(num - 1) + fibc(num - 2);
    }
}
```

输出结果

```
future result from runnable : null
future result from Callable : 6765
future result from futureTask : 6765
```

##4. CompletionService

CompletionService用于提交一组Callable任务，其take()方法返回已完成的一个Callable任务对应的Future对象。

示例：这里数据的获取好比同时种了好几块地的麦子，然后等待收割，秋收时，哪块先熟，先收割哪块

```java
import java.util.Random;  
import java.util.concurrent.Callable;  
import java.util.concurrent.CompletionService;  
import java.util.concurrent.ExecutionException;  
import java.util.concurrent.ExecutorCompletionService;  
import java.util.concurrent.ExecutorService;  
import java.util.concurrent.Executors;  
import java.util.concurrent.Future;  
import java.util.concurrent.TimeUnit;  
import java.util.concurrent.TimeoutException;  
  
public class CallableAndFuture {  
    public static void main(String[] args){  
  
　　//创建一个单独的线程         
　　ExecutorService threadPool = Executors.newSingleThreadExecutor();  
　　//future泛型与Callable的类型一致        
　　Future<String> future = threadPool.submit(new Callable<String>(){  
  
            @Override  
            public String call() throws Exception {  
                Thread.sleep(3000);  
                return "hello";  
            }  
              
        });  
        System.out.println("等待結果……");  
          
        //在指定时timeout内等待，未等到抛出TimeoutException  
        //System.out.println("拿到结果：" + future.get(long timeout,TimeUnit unit));  
          
        try {  
            System.out.println("拿到结果：" + future.get()); //获取线程执行后的结果  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        } catch (ExecutionException e) {  
            e.printStackTrace();  
        }  
                   
        //CompletionService用于提交一组Callable任务，  
        //其take方法返回已完成的一个Callable任务对应的Future对象。  
        ExecutorService threadPool2 = Executors.newFixedThreadPool(10);  
        CompletionService<Integer> completionService = new ExecutorCompletionService<Integer>(threadPool2);  
          
　　            //创建10任务  
        for(int i=1;i<=10;i++){  
            final int seq = i;  
  
　　            //将任务提交给线程池  
            completionService.submit(new Callable<Integer>(){  
                @Override  
                public Integer call() throws Exception {  
                    Thread.sleep(new Random().nextInt(5000));  
                    return seq;               
                }  
            });  
        }  
        //获取结果，哪个结果先返回就先获得  
        for(int i=0;i<10;i++){  
            try {  
                System.out.println(completionService.take().get());  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            } catch (ExecutionException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
}  
```
输出结果

```
等待結果……
拿到结果：hello
3
6
10
2
4
8
5
1
7
9
```