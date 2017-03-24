# 第一题

现有的程序代码模拟产生了16个日志对象，并且需要运行16秒才能打印完这些日志，请在程序中增加4个线程去调用parseLog()方法来分头打印这16个日志对象，程序只需要运行4秒即可打印完这些日志对象。

```java
public class Test {  
          
        public static void main(String[] args){  
              
            System.out.println("begin:"+(System.currentTimeMillis()/1000));  
            /*模拟处理16行日志，下面的代码产生了16个日志对象，当前代码需要运行16秒才能打印完这些日志。 
            修改程序代码，开四个线程让这16个对象在4秒钟打完。 
            */  
            for(int i=0;i<16;i++){  //这行代码不能改动  
                final String log = ""+(i+1);   //这行代码不能改动  
                {  
                        Test.parseLog(log);  
                }  
            }  
        }  
          
        //parseLog方法内部的代码不能改动  
        public static void parseLog(String log){  
            System.out.println(log+":"+(System.currentTimeMillis()/1000));  
              
            try {  
                Thread.sleep(1000);  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            }         
        }     
    }
```

实现：通过阻塞队列实现线程间的通信

```java
import java.util.concurrent.ArrayBlockingQueue;  
import java.util.concurrent.BlockingQueue;  
  
//BlockingQueue  
public class Test {  
      
    public static void main(String[] args){  
          
        //创建一个空间大小为16的阻塞队列，空间大小可以任意，因为每次打印都要1秒，在此期间，  
        //4个线程足以不断去从队列中取数据，然后打印,即在1秒内打印4条日志信息  
        final BlockingQueue<String> queue = new ArrayBlockingQueue<String>(16);  
          
        //开启4个线程打印  
        for(int i=0;i<4;i++){  
            new Thread(new Runnable(){  
                @Override  
                public void run() {  
                    while(true){  
                        try {  
                            //开始没有数据，阻塞，一旦有其中一个线程就去取数据，即不再阻塞，就开始打印
                            String log = queue.take();    
                            parseLog(log);  
                        } catch (InterruptedException e) {  
                            e.printStackTrace();  
                        }  
                    }  
                }  
                  
            }).start();  
        }  
          
        //打印秒数  
        System.out.println("begin:"+(System.currentTimeMillis()/1000));  
          
          
        for(int i=0;i<16;i++){  // 这行代码不能改动  
            final String log = ""+(i+1); // 这行代码不能改动  
            {  
                    try {  
                        queue.put(log);  // 向队列中存储数据  
                    } catch (InterruptedException e) {  
                        e.printStackTrace();  
                    }  
                    //Test.parseLog(log);  
            }  
        }  
    }  
      
    //parseLog方法内部的代码不能改动  
    public static void parseLog(String log){  
        System.out.println(log+":"+(System.currentTimeMillis()/1000));  
          
        try {  
            Thread.sleep(1000);     // 模拟每条日志打印需要1秒  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }         
    }   
}  
```


# 第二题

现成程序中的Test类中的代码在不断地产生数据，然后交给TestDo.doSome()方法去处理，就好像生产者在不断地产生数据，消费者在不断消费数据。

请将程序改造成有10个线程来消费生成者产生的数据，这些消费者都调用TestDo.doSome()方法去进行处理，故每个消费者都需要一秒才能处理完，程序应保证这些消费者线程依次有序地消费数据，只有上一个消费者消费完后，下一个消费者才能消费数据，下一个消费者是谁都可以，但要保证这些消费者线程拿到的数据是有顺序的。


```java
public class Test {  
      
        public static void main(String[] args) {  
              
            System.out.println("begin:"+(System.currentTimeMillis()/1000));  
            for(int i=0;i<10;i++){  //这行不能改动  
                String input = i+"";  //这行不能改动  
                String output = TestDo.doSome(input);  
                System.out.println(Thread.currentThread().getName()+ ":" + output);  
            }  
        }  
    }  
      
    //不能改动此TestDo类  
    class TestDo {  
        public static String doSome(String input){  
              
            try {  
                Thread.sleep(1000);  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            }  
            String output = input + ":"+ (System.currentTimeMillis() / 1000);  
            return output;  
        }  
    }  
```


在实现之前先介绍一个阻塞队列：SynchronousQuene，一种阻塞队列，其中每个插入操作必须等待另一个线程的对应移除操作 ，反之亦然。同步队列没有任何内部容量，甚至连一个队列的容量都没有。除非另一个线程试图移除某个元素，否则也不能（使用任何方法）插入元素；也不能迭代队列，因为其中没有元素可用于迭代。

应用：它非常适合于传递性设计，在这种设计中，在一个线程中运行的对象要将某些信息、事件或任务传递给在另一个线程中运行的对象，它就必须与该对象同步。 


```java
import java.util.concurrent.SynchronousQueue;  
  
/*Semaphore与SynchronousQueue的混合使用。 
    由于Semaphore只有1个许可权，所以谁先拿到谁执行，然后释放，保证依次执行, 
    用锁也行，只要保证一个线程执行即可 
    SynchronousQueue是必须有其他线程取的动作，这样一一对应 
*/  
public class Test {  
  
    public static void main(String[] args) {  
          
        //定义一个许可权为1的信号灯  
        final Semaphore semaphore = new Semaphore(1);  
          
        //产生的结果无序  
        final SynchronousQueue<String> queue = new SynchronousQueue<String>();  
          
        //产生10个线程  
        for(int i=0;i<10;i++){  
            new Thread(new Runnable(){  
                @Override  
                public void run() {   
                    try {  
                        semaphore.acquire();    //获取许可  
                        String input = queue.take();    //获取并移除此队列的头  
                        String output = TestDo.doSome(input);  
                        System.out.println(Thread.currentThread().getName()+ ":" + output);  
                        semaphore.release();    //释放许可  
                    } catch (InterruptedException e) {  
                        // TODO Auto-generated catch block  
                        e.printStackTrace();  
                    }     
                }  
            }).start();  
        }  
          
        System.out.println("begin:"+(System.currentTimeMillis()/1000));  
        for(int i=0;i<10;i++){  //这行不能改动  
            String input = i+"";  //这行不能改动  
            try {  
                queue.put(input); //将指定元素添加到此队列  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
}  
  
//不能改动此TestDo类  
class TestDo {  
    public static String doSome(String input){  
          
        try {  
            Thread.sleep(1000);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        String output = input + ":"+ (System.currentTimeMillis() / 1000);  
        return output;  
    }  
}  
```

#  第三题

现有程序同时启动了4个线程去调用TestDo.doSome(key, value)方法，由于TestDo.doSome(key, value)方法内的代码是先暂停1秒，然后再输出以秒为单位的当前时间值，所以，会打印出4个相同的时间值，如下所示：

```
4:4:1258199615
1:1:1258199615
3:3:1258199615
1:2:1258199615
```

请修改代码，如果有几个线程调用TestDo.doSome(key, value)方法时，传递进去的key相等（equals比较为true），则这几个线程应互斥排队输出结果，即当有两个线程的key都是"1"时，它们中的一个要比另外其他线程晚1秒输出结果，如下所示：

```
4:4:1258199615
1:1:1258199615
3:3:1258199615
1:2:1258199616
```

总之，当每个线程中指定的key相等时，这些相等key的线程应每隔一秒依次输出时间值（要用互斥），如果key不同，则并行执行（相互之间不互斥）。


```java
    //不能改动此Test类      
    public class Test extends Thread{  
          
        private TestDo testDo;  
        private String key;  
        private String value;  
          
        public Test(String key,String key2,String value){  
            this.testDo = TestDo.getInstance();  
            /*常量"1"和"1"是同一个对象，下面这行代码就是要用"1"+""的方式产生新的对象， 
            以实现内容没有改变，仍然相等（都还为"1"），但对象却不再是同一个的效果*/  
            this.key = key+key2;   
            this.value = value;  
        }  
  
  
        public static void main(String[] args) throws InterruptedException{  
            Test a = new Test("1","","1");  
            Test b = new Test("1","","2");  
            Test c = new Test("3","","3");  
            Test d = new Test("4","","4");  
            System.out.println("begin:"+(System.currentTimeMillis()/1000));  
            a.start();  
            b.start();  
            c.start();  
            d.start();  
  
        }  
          
        public void run(){  
            testDo.doSome(key, value);  
        }  
    }  
  
    class TestDo {  
  
        private TestDo() {}  
        private static TestDo _instance = new TestDo();   
        public static TestDo getInstance() {  
            return _instance;  
        }  
  
        public void doSome(Object key, String value) {  
      
            // 以大括号内的是需要局部同步的代码，不能改动!  
            {  
                try {  
                    Thread.sleep(1000);  
                    System.out.println(key+":"+value + ":"  
                            + (System.currentTimeMillis() / 1000));  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        }  
    }  
```

对于源代码中关于实现值相同而对象不同的效果进行解释：
对于：
```java 
 a = "1"+"";
 b = "1"+"";
```
编译器自动优化，所以a和b是同一个对象

而对于：key = key+key2;  由于是变量，编译器无法识别，这时a和b把“1”和“”赋值给key和key2时会得到两个不同的对象

思想：将集合中的对象作为同步代码块的锁，即this锁，每次将对象存入集合中的时候，就判断是否原集合中已经存在一个与将要存入集合的对象值相同的对象，即用equals比较，如果有，那么就获取原来的这个对象，把这个对象作为将要存入对象的锁，这样它们持有的就是同一把锁，即可实现互斥，这样就可以实现值相同的对象在不同的时刻打印的效果

代码中出现的问题：在遍历ArrayList集合查找与要存入值相同元素的时候，进行了添加的动作，所以会出现并发修改异常，因此使用并发的CopyOnWriteArrayList


```java
import java.util.ArrayList;  
import java.util.Iterator;  
import java.util.concurrent.CopyOnWriteArrayList;  
  
  
//不能改动此Test类      
public class Test extends Thread{  
      
    private TestDo testDo;  
    private String key;  
    private String value;  
      
    public Test(String key,String key2,String value){  
        this.testDo = TestDo.getInstance();  
        /*常量"1"和"1"是同一个对象，下面这行代码就是要用"1"+""的方式产生新的对象， 
        以实现内容没有改变，仍然相等（都还为"1"），但对象却不再是同一个的效果*/  
          
        this.key = key+key2;    //这里是变量，所以不会优化  
/*      a = "1"+""; 
        b = "1"+""   
        编译器自动优化，所以a和b是同一个对象 
*/  
        this.value = value;  
    }  
  
  
    public static void main(String[] args) throws InterruptedException{  
        Test a = new Test("1","","1");  
        Test b = new Test("1","","2");  
        Test c = new Test("3","","3");  
        Test d = new Test("4","","4");  
        System.out.println("begin:"+(System.currentTimeMillis()/1000));  
        a.start();  
        b.start();  
        c.start();  
        d.start();  
  
    }  
      
    public void run(){  
        testDo.doSome(key, value);  
    }  
}  
  
class TestDo {  
  
    private TestDo() {}  
    private static TestDo _instance = new TestDo();   
    public static TestDo getInstance() {  
        return _instance;  
    }  
  
    //private ArrayList keys = new ArrayList();  
    //迭代的时候不能修改数据，所以使用同步的ArrayList  
    private CopyOnWriteArrayList keys = new CopyOnWriteArrayList();  
    public void doSome(Object key, String value) {  
        Object o = key;  
        if(!keys.contains(o)){  //比较是否已经存入了一个相同值的对象  
            keys.add(o);  
        }else{  
              
            //迭代，找出原集合里和传进来的值相同的对象  
            for(Iterator iter=keys.iterator();iter.hasNext();){  
                try {  
                    Thread.sleep(20);   // 迭代的时候休息一会，在ArrayList下演示并发修改异常  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
                Object oo = iter.next();  
                if(oo.equals(o)){   //如果两个对象的值相同  
                    //就让原集合中的那个相等值的对象作为锁对象，由于原对象之前做的就是锁
                    //这样两个锁就相同了，就可以实现互斥
                  	o = oo;    
                    break;  
                }  
            }  
        }  
        synchronized(o)  
        // 以大括号内的是需要局部同步的代码，不能改动!  
        {  
            try {  
                Thread.sleep(1000);  
                System.out.println(key+":"+value + ":"  
                        + (System.currentTimeMillis() / 1000));  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
}  
```