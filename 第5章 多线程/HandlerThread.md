# 1. HandlerThread的使用

继承自Thread，在run()方法中，执行了Looper.prepare()和Looper.loop()，和handler结合使用，实现后台轮询线程功能

- start()	
- quit()
- getLooper()

```java
public class HandlerThreadActivity extends AppCompatActivity {
    private TextView      mTvServiceInfo;
    private HandlerThread mCheckMsgThread;
    private Handler       mCheckMsgHandler;
    private boolean       isUpdateInfo;
    private static final int MSG_UPDATE_INFO = 0x110;

    //与UI线程相关的handler
    private Handler mHandler = new Handler();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_thread_handler);
        initBackThread();//创建后台线程
        mTvServiceInfo = (TextView) findViewById(R.id.id_textview);
    }

    @Override
    protected void onResume() {
        super.onResume();
        isUpdateInfo = true;//开始查询
        mCheckMsgHandler.sendEmptyMessage(MSG_UPDATE_INFO);
    }

    @Override
    protected void onPause() {
        super.onPause();
        isUpdateInfo = false;//停止查询
        mCheckMsgHandler.removeMessages(MSG_UPDATE_INFO);
    }

    private void initBackThread() {
        mCheckMsgThread = new HandlerThread("check-message-coming");
        mCheckMsgThread.start();
        mCheckMsgHandler = new Handler(mCheckMsgThread.getLooper()) {
            @Override
            public void handleMessage(Message msg) {
                checkForUpdate();
                if (isUpdateInfo) {
                    mCheckMsgHandler.sendEmptyMessageDelayed(
                            MSG_UPDATE_INFO, 1000);
                }
            }
        };
    }

    //模拟从服务器解析数据
    private void checkForUpdate() {
        Thread.sleep(1000);//模拟耗时
        mHandler.post(new Runnable() {
            @Override
            public void run() {
                String result = "msg";
                mTvServiceInfo.setText(result);
            }
        });
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mCheckMsgThread.quit();//释放资源
    }
}
```

# 2. HandlerThread源码

Handler的构造，其实就是在Handler中持有一个指向该Looper.mQueue对象，当handler调用sendMessage方法时，其实就是往该mQueue中去插入一个message，然后Looper.loop()就会取出执行

```java
public class HandlerThread extends Thread {
    int mPriority;
    int mTid = -1;
    Looper mLooper;

    public HandlerThread(String name) {
        super(name);
        mPriority = Process.THREAD_PRIORITY_DEFAULT;
    }
    
    public HandlerThread(String name, int priority) {
        super(name);
        mPriority = priority;
    }
    
    protected void onLooperPrepared() {
    }

    @Override
    public void run() {
        mTid = Process.myTid();
        Looper.prepare();
        synchronized (this) {
            mLooper = Looper.myLooper();
            notifyAll();
        }
        Process.setThreadPriority(mPriority);
        onLooperPrepared();
        Looper.loop();
        mTid = -1;
    }
    
    public Looper getLooper() {
        if (!isAlive()) {
            return null;
        }
        
        // If the thread has been started, wait until the looper has been created.
        synchronized (this) {
            while (isAlive() && mLooper == null) {
                try {
                    wait();
                } catch (InterruptedException e) {
                }
            }
        }
        return mLooper;
    }

    public boolean quit() {
        Looper looper = getLooper();
        if (looper != null) {
            looper.quit();
            return true;
        }
        return false;
    }

    public boolean quitSafely() {
        Looper looper = getLooper();
        if (looper != null) {
            looper.quitSafely();
            return true;
        }
        return false;
    }

    public int getThreadId() {
        return mTid;
    }
}
```
我们要在子线程中调用Looper.prepare() 为一个线程开启一个消息循环，默认情况下Android中新诞生的线程是没有开启消息循环的。（主线程除外，主线程系统会自动为其创建Looper对象，开启消息循环。） Looper对象通过MessageQueue来存放消息和事件。一个线程只能有一个Looper，对应一个MessageQueue。 然后通过Looper.loop() 让Looper开始工作，从消息队列里取消息，处理消息。

注意：写在Looper.loop()之后的代码不会被执行，这个函数内部应该是一个循环，当调用mHandler.getLooper().quit()后，loop才会中止，其后的代码才能得以运行。

# 3. HandlerThread的特点

- HandlerThread将loop转到子线程中处理，说白了就是将分担MainLooper的工作量，降低了主线程的压力，使主界面更流畅。

- 开启一个线程起到多个线程的作用。处理任务是串行执行，按消息发送顺序进行处理。HandlerThread本质是一个线程，在线程内部，代码是串行处理的。

- 但是由于每一个任务都将以队列的方式逐个被执行到，一旦队列中有某个任务执行时间过长，那么就会导致后续的任务都会被延迟处理。

- HandlerThread拥有自己的消息队列，它不会干扰或阻塞UI线程。

- 对于网络IO操作，HandlerThread并不适合，因为它只有一个线程，还得排队一个一个等着。

# 4. 参考

[详解 Android 中的 HandlerThread](http://droidyue.com/blog/2015/11/08/make-use-of-handlerthread/?utm_source=tuicool&utm_medium=referral)
