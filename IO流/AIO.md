# 1. JDK7 AIO初体验

JDK7已经release一段时间了，有个重要的新特性是AIO。今天趁闲暇，简单体验了下，简单分享如下：

# 2. 关于AIO的概念理解
关于AIO的概念，仅谈谈个人的一点理解。可能不到位，请大家指出。
Io的两个重要步骤：发起IO请求，和实际的IO操作。在unix网络编程的定义里异步和非异步概念的区别就是实际的IO操作是否阻塞。如果不是就是异步，如果是就是同步。

而阻塞和非阻塞的区别在于发起IO请求的时候是否会阻塞，如果会就是阻塞，不会就是非阻塞。

本人理解能力有限，想了个例子来辅助自己理解：
小明想要买一本<深入java虚拟机>的书，以下几个场景可以来理解这几种io模式：

1.    如果小明每天都去书店问售货员说有没有这本书，如果没有就回去继续等待，等下次再过来文。(阻塞)
2.    如果小明告诉售货员想买一本<深入java虚拟机>的书，那么就在家里等着做其他事情去了，如果书到了售货员就通知小明，小明再自己过去取。
3.    如果小明告售货员想买一本<深入java虚拟机>的书，然后告诉售货员到了帮他送到某某地方去，就做其他事情去了。小明就不管了，等书到了，售货员就帮他送到那个地方了。
      售货员可以认为是操作系统的一个服务，而小明是一个用户进程。不知道是否有误，如果有误请大家拍砖指出，谢谢。
      可以看出2,3的效率明显要比1高。但是1最简单，而2,3需要一些协作。充分证明了团队合作的力量。

# 3. JDK7 AIO初体验
AsynchronousChannel：支持异步通道，包括服务端AsynchronousServerSocketChannel和普通AsynchronousSocketChannel等实现。

CompletionHandler：用户处理器。定义了一个用户处理就绪事件的接口，由用户自己实现，异步io的数据就绪后回调该处理器消费或处理数据。

AsynchronousChannelGroup：一个用于资源共享的异步通道集合。处理IO事件和分配给CompletionHandler。(具体这块还没细看代码，后续再分析这块)

以一个简单监听服务端为例，基本过程是：

1.    启动一个服务端通道
2.    定义一个事件处理器，用户事件完成的时候处理，如消费数据。
3.    向系统注册一个感兴趣的事件，如接受数据，并把事件完成的处理器传递给系统。
4.    都已经交待完毕，可以只管继续做自己的事情了，操作系统在完成事件后通过其他的线程会自动调用处理器完成事件处理

以下用一个例子来简单实现，一个服务端和客户端。服务端监听客户端的消息，并打印出来

AIOServer.java

```java
package io.aio;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousServerSocketChannel;
import java.nio.channels.AsynchronousSocketChannel;
import java.nio.channels.CompletionHandler;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

/**
 * 
 * @author noname
 */
public class AIOServer {
	public final static int PORT = 9888;
	private AsynchronousServerSocketChannel server;

	public AIOServer() throws IOException {
		server = AsynchronousServerSocketChannel.open().bind(
				new InetSocketAddress(PORT));
	}

	public void startWithFuture() throws InterruptedException,
			ExecutionException, TimeoutException {
		System.out.println("Server listen on " + PORT);
		Future<AsynchronousSocketChannel> future = server.accept();
		AsynchronousSocketChannel socket = future.get();
		ByteBuffer readBuf = ByteBuffer.allocate(1024);
		readBuf.clear();
		socket.read(readBuf).get(100, TimeUnit.SECONDS);
		readBuf.flip();
		System.out.printf("received message:" + new String(readBuf.array()));
		System.out.println(Thread.currentThread().getName());

	}

	public void startWithCompletionHandler() throws InterruptedException,
			ExecutionException, TimeoutException {
		System.out.println("Server listen on " + PORT);
		//注册事件和事件完成后的处理器
		server.accept(null,
				new CompletionHandler<AsynchronousSocketChannel, Object>() {
					final ByteBuffer buffer = ByteBuffer.allocate(1024);

					public void completed(AsynchronousSocketChannel result,
							Object attachment) {
						System.out.println(Thread.currentThread().getName());
						System.out.println("start");
						try {
							buffer.clear();
							result.read(buffer).get(100, TimeUnit.SECONDS);
							buffer.flip();
							System.out.println("received message: "
									+ new String(buffer.array()));
						} catch (InterruptedException | ExecutionException e) {
							System.out.println(e.toString());
						} catch (TimeoutException e) {
							e.printStackTrace();
						} finally {

							try {
								result.close();
								server.accept(null, this);
							} catch (Exception e) {
								System.out.println(e.toString());
							}
						}

						System.out.println("end");
					}

					@Override
					public void failed(Throwable exc, Object attachment) {
						System.out.println("failed: " + exc);
					}
				});
		// 主线程继续自己的行为
		while (true) {
			System.out.println("main thread");
			Thread.sleep(1000);
		}

	}

	public static void main(String args[]) throws Exception {
		new AIOServer().startWithCompletionHandler();
	}
}
```

AIOClient.java

```java
package io.aio;

import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousSocketChannel;

public class AIOClient {

	public static void main(String... args) throws Exception {
		AsynchronousSocketChannel client = AsynchronousSocketChannel.open();
		client.connect(new InetSocketAddress("localhost", 9888));
		client.write(ByteBuffer.wrap("test".getBytes())).get();
	}
}
```

服务端写了两种处理实现方式，startWithCompletionHandler是通过Handler来处理，startWithFuture是通过Future方式来处理。startWithCompletionHandler方法里可以看到调用accepte()完成异步注册后，线程就可以继续自己的处理了，完全不被这个io所中断。

从以上来看AIO的代码简单了很多，至少比NIO的代码实现简单很多。

# 4. AIO和NIO性能哪个好

Java NIO ： 同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。
Java AIO(NIO.2) ： 异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理，

NIO方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中，编程比较复杂，JDK1.4开始支持。
AIO方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持

I/O属于底层操作，需要操作系统支持，并发也需要操作系统的支持，所以性能方面不同操作系统差异会比较明显。另外NIO的非阻塞，需要一直轮询，也是一个比较耗资源的。所以出现AIO

# 5. 使用J2SE进行服务器架构技术选型的变迁

虽然现在对大多数程序员来讲，基本不会再有使用Java开发一个服务器这样的任务，但是，这方面的技术研究一下，对自己的技术提高还是非常有帮助的。说不定啥时候能派上用场。

使用Java（J2SE）来设计服务器产品（不使用开源或其他已有产品）的架构，随着Java的不断发展，这几年也发生了很大变化。在JDK1.4之前，使用Java构建服务器应用本身就很少，所以这里也就不提了，我们从JDK1.4开始说。

## 阶段1：一个连接一个线程

![](http://dl.iteye.com/upload/attachment/0082/2981/abb0fbde-f71c-378d-8f6a-7a82c0685ecc.jpg) 

## 阶段2：服务器端采用了线程池

 ![](http://dl.iteye.com/upload/attachment/0082/2983/884bb847-da6d-3294-80d0-f16fb8447882.jpg)

阶段1和阶段2虽然简单，但是很实用，在很多场景下仍然是第一选择。而且编程模型业内非常简单。

## 阶段3：采用非阻塞IO，多路复用技术，又有两种不同的方式

![](http://dl.iteye.com/upload/attachment/0082/2985/9ae17c49-93c0-3e17-966e-f1ed9826df48.jpg) 

![](http://dl.iteye.com/upload/attachment/0082/2987/f8407c68-ac15-377d-9d29-3cad5a82d0bd.jpg)

这种方式很重要的一点就是在IO事件发生时得到通知，由程序进行处理。

NIO给编程带来了很大的复杂度，使用NIO开发非常不容易，也很容易犯错误，所以，采用别人的框架是一个简单而自然的选择，采用grizzly和mina都很不错，对通用的场景都能满足要求。这里提醒一下，不管mina和grizzly，都有一些你不想用的特性，干扰你想用的功能，需要小心对待，最好自己也能处理mina和grizzly的bug，改进这些框架的功能。

再有，给予NIO来开发SSL也很复杂。

## 阶段4：使用AIO技术

![](http://dl.iteye.com/upload/attachment/0082/2989/ee24411e-e909-39b0-889c-878ab833b7d5.jpg)

AIO最大的特性就是事前先设置好事件的回调函数，事件发生时自动调用回调。而且，得到的通知是“IO操作完成”，而不是NIO的“IO操作即将开始”。

使用AIO，在上层开发SSL也也很麻烦。

# 6. 原文链接

- http://www.iteye.com/topic/1113611
- http://windshome.iteye.com/blog/1837558
- https://zhidao.baidu.com/question/873378431735591372.html