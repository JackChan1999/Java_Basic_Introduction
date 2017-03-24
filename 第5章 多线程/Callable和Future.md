##**1. Callable** 
用于获取线程执行完的结果

Callable 接口类似于Runnable，两者都是为那些其实例可能被另一个线程执行的类设计的。但是 Runnable 不会返回结果，并且无法抛出经过检查的异常，而Callable返回结果并且可能抛出异常的任务

##**2. Future** 
Future 接口表示异步计算的结果。它提供了检查计算是否完成的方法，以等待计算的完成，并获取计算的结果。计算完成后只能使用get()方法来获取结果，如有必要，计算完成前可以阻塞此方法

Future取得的结果类型和Callable返回的结果类型必须一致，这是通过泛型来实现的。Callable要采用ExecutorService的submit()方法提交，返回的future对象可以取消任务

##**3. CompletionService**
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