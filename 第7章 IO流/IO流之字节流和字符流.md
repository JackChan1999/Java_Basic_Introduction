---
typora-copy-images-to: img
---

![io流](http://img.blog.csdn.net/20161003130057678)
# **1. 流的概念**
流(stream)的概念源于UNIX中管道(pipe)的概念。在UNIX中，管道是一条不间断的字节流，用来实现程序或进程间的通信，或读写外围设备、外部文件等。

一个流，必有源端和目的端，它们可以是计算机内存的某些区域，也可以是磁盘文件，甚至可以是Internet上的某个URL。

流的方向是重要的，根据流的方向，流可分为两类：输入流和输出流。用户可以从输入流中读取信息，但不能写它。相反，对输出流，只能往输入流写，而不能读它。

实际上，流的源端和目的端可简单地看成是字节的生产者和消费者，对输入流，可不必关心它的源端是什么，只要简单地从流中读数据，而对输出流，也可不知道它的目的端，只是简单地往流中写数据。 

形象的比喻——水流 ，文件==程序 ，文件和程序之间连接一个管道，水流就在之间形成了,自然也就出现了方向：可以流进，也可以流出.便于理解，这么定义流： 流就是一个管道里面有流水，这个管道连接了文件和程序。

# **2. IO流概述**
大多数应用程序都需要实现与设备之间的数据传输，例如键盘可以输入数据，显示器可以显示程序的运行结果等。在Java中，将这种通过不同输入输出设备（键盘，内存，显示器，网络等）之间的数据传输抽象的表述为“流”，程序允许通过流的方式与输入输出设备进行数据传输。Java中的“流”都位于Java.io包中，称之为IO（输入输出）流。 IO流：即InputOutput的缩写。

输入流和输出流相对于内存设备而言。将外设中的数据读取到内存中：输入。将内存的数写入到外设中：输出。

IO流的特点：

- IO流用来处理设备间的数据传输。
- Java对数据的操作是通过流的方式。
- Java用于操作流的对象都在IO包中。
- 流按操作数据分为两种：字节流和字符流。
- 流按流向分为：输入流和输出流。

PS：流只能操作数据，而不能操作文件。
# **3. 流的三种分类方式**      
- 按流的方向分为：输入流和输出流     
- 按流的数据单位不同分为：字节流和字符流     
- 按流的功能不同分为:节点流和处理流

![io](http://img.blog.csdn.net/20161002235649942)
# **4. 流的层次结构** 

 IO流的常用基类：

- 字节流的抽象基流：InputStream和OutputStream
- 字符流的抽象基流：Reader和Writer

**PS：**此四个类派生出来的子类名称都是以父类名作为子类名的后缀，以前缀为其功能；如InputStream子类FileInputStream；Reader子类FileReader

字符流的由来：
​      
其实就是：字节流读取文字字节数据后，不直接操作而是先查指定的编码表，获取对应的文字。再对这个文字进行操作。简单说：字节流+编码表。

## **4.1 InputStream**
![io流](http://img.blog.csdn.net/20150827230436781)

## **4.2 OutputStream**
![io流](http://img.blog.csdn.net/20150827230338538)

## **4.3 Reader**
![io流](http://img.blog.csdn.net/20150827230507625)

## **4.4 Writer**
![io](http://img.blog.csdn.net/20150827230537267)

# **5. 字节流**

在计算机中，无论是文本、图片、音频还是视频，所有文件都是以二进制(字节)形式存在的，IO流中针对字节的输入输出提供了一系列的流，统称为字节流。字节流是程序中最常用的流，根据数据的传输方向可将其分为字节输入流和字节输出流。在JDK中，提供了两个抽象类InputStream和OutputStream，它们是字节流的顶级父类，所有的字节输入流都继承自InputStream，所有的字节输出流都继承自OutputStream。为了方便理解，可以把InputStream和OutputStream比作两根“水管”，如图所示。

![1500708330117](img/1500708330117.png)

InputStream和OutputStream这两个类虽然提供了一系列和读写数据有关的方法，但是这两个类是抽象类，不能被实例化，因此，针对不同的功能，InputStream和OutputStream提供了不同的子类，这些子类形成了一个体系结构，如图所示。

![1500708378450](img/1500708378450.png)

![1500708385651](img/1500708385651.png)

## **5.1 字节流写数据**
字节输出流：抽象类OutputStream，实现类FileOutputStream

![io流](http://img.blog.csdn.net/20150915111705070)

字节输出流操作步骤：

- 创建字节输出流对象
- 写数据
- 释放资源

**字节流写数据的方式**

| 方法                              | 说明            |
| :------------------------------ | :------------ |
| write(int b)                    | 一次写一个字节       |
| write(byte[] b)                 | 一次写一个字节数组     |
| write(byte[] b,int off,int len) | 一次写一个字节数组的一部分 |
| flush()                         | 刷新缓冲区         |
| close()                         | 释放资源          |
<br>
**如何实现数据的换行?**

- 为什么没有换行呢?
  因为只是写了字节数据，并没有写入换行符号。
- 如何实现呢?
  写入换行符号即可

**PS：不同的系统针对不同的换行符号识别是不一样的?**

- windows：\r\n
- linux：\n
- Mac：\r
- 而一些常见的个高级记事本，是可以识别任意换行符号的。

**如何实现数据的追加写入?**

用构造方法带第二个参数是true的情况即可

```java
package cn.itcast_01;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
/*
 * 需求：我要往一个文本文件中输入一句话："hello,io"
 * 
 * 分析：
 * 	A:这个操作最好是采用字符流来做，但是呢，字符流是在字节流之后才出现的，所以，今天我先讲解字节流如何操作。
 * 	B:由于我是要往文件中写一句话，所以我们要采用字节输出流。
 */
public class FileOutputStreamDemo {
	public static void main(String[] args) throws IOException {
		// 创建字节输出流对象
		// FileOutputStream(File file)
		// File file = new File("fos.txt");
		// FileOutputStream fos = new FileOutputStream(file);
		// FileOutputStream(String name)
		FileOutputStream fos = new FileOutputStream("fos.txt");
		/*
		 * 创建字节输出流对象了做了几件事情：
		 * A:调用系统功能去创建文件
		 * B:创建fos对象
		 * C:把fos对象指向这个文件
		 */
		
		//写数据
		fos.write("hello,IO".getBytes());
		fos.write("java".getBytes());
		
		//释放资源
		//关闭此文件输出流并释放与此流有关的所有系统资源。
		fos.close();
		/*
		 * 为什么一定要close()呢?
		 * A:让流对象变成垃圾，这样就可以被垃圾回收器回收了
		 * B:通知系统去释放跟该文件相关的资源
		 */
		//java.io.IOException: Stream Closed
		//fos.write("java".getBytes());
	}
}
```
## **5.2 字节流写数据的方式**

```java
package cn.itcast_01;
import java.io.FileOutputStream;
import java.io.IOException;
/*
 * 字节输出流操作步骤：
 * A:创建字节输出流对象
 * B:调用write()方法
 * C:释放资源
 * 
 * public void write(int b):写一个字节
 * public void write(byte[] b):写一个字节数组
 * public void write(byte[] b,int off,int len):写一个字节数组的一部分
 */
public class FileOutputStreamDemo2 {
	public static void main(String[] args) throws IOException {
		// 创建字节输出流对象
		// OutputStream os = new FileOutputStream("fos2.txt"); // 多态
		FileOutputStream fos = new FileOutputStream("fos2.txt");

		// 调用write()方法
		//fos.write(97); //97 -- 底层二进制数据	-- 通过记事本打开 -- 找97对应的字符值 -- a
		// fos.write(57);
		// fos.write(55);
		
		//public void write(byte[] b):写一个字节数组
		byte[] bys={97,98,99,100,101};
		fos.write(bys);
		
		//public void write(byte[] b,int off,int len):写一个字节数组的一部分
		fos.write(bys,1,3);
		
		//释放资源
		fos.close();
	}
}
```
**字节流写数据加入异常处理**

```java
package cn.itcast_01;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
/*
 * 加入异常处理的字节输出流操作
 */
public class FileOutputStreamDemo4 {
	public static void main(String[] args) {
		// 分开做异常处理
		// FileOutputStream fos = null;
		// try {
		// fos = new FileOutputStream("fos4.txt");
		// } catch (FileNotFoundException e) {
		// e.printStackTrace();
		// }
		//
		// try {
		// fos.write("java".getBytes());
		// } catch (IOException e) {
		// e.printStackTrace();
		// }
		//
		// try {
		// fos.close();
		// } catch (IOException e) {
		// e.printStackTrace();
		// }

		// 一起做异常处理
		// try {
		// FileOutputStream fos = new FileOutputStream("fos4.txt");
		// fos.write("java".getBytes());
		// fos.close();
		// } catch (FileNotFoundException e) {
		// e.printStackTrace();
		// } catch (IOException e) {
		// e.printStackTrace();
		// }

		// 改进版
		// 为了在finally里面能够看到该对象就必须定义到外面，为了访问不出问题，还必须给初始化值
		FileOutputStream fos = null;
		try {
			// fos = new FileOutputStream("z:\\fos4.txt");
			fos = new FileOutputStream("fos4.txt");
			fos.write("java".getBytes());
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			// 如果fos不是null，才需要close()
			if (fos != null) {
				// 为了保证close()一定会执行，就放到这里了
				try {
					fos.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
		}
	}
}
```
## **5.3 字节流读取数据**

字节输入流：抽象类InputStream，实现类FileInputStream

![io流](http://img.blog.csdn.net/20150915111743638)

字节输入流操作步骤：

- A：创建字节输入流对象
- B：调用read()方法读取数据，并把数据显示在控制台
- C：释放资源

**字节流读数据的方式**

| 方法                                 | 功能描述          |
| :--------------------------------- | :------------ |
| int read()                         | 一次读取一个字节      |
| int read(byte[] b)                 | 一次读取一个字节数组    |
| int read(byte[] b,int off,int len) | 一次读一个字节数组的一部分 |
| void close()                       | 释放资源          |
<br>
```java
package cn.itcast_02;
import java.io.FileInputStream;
import java.io.IOException;
/*
 * 字节输入流操作步骤：
 * A:创建字节输入流对象
 * B:调用read()方法读取数据，并把数据显示在控制台
 * C:释放资源
 * 
 * 读取数据的方式：
 * A:int read():一次读取一个字节
 * B:int read(byte[] b):一次读取一个字节数组
 * C:int read(byte[] b):一次读取一个字节数组
 *   返回值其实是实际读取的字节个数。
 */
public class FileInputStreamDemo {
	public static void main(String[] args) throws IOException {
		// FileInputStream(String name)
		// FileInputStream fis = new FileInputStream("fis.txt");
		FileInputStream fis = new FileInputStream("FileOutputStreamDemo.java");

		// // 调用read()方法读取数据，并把数据显示在控制台
		// // 第一次读取
		// int by = fis.read();
		// System.out.println(by);
		// System.out.println((char) by);
		//
		// // 第二次读取
		// by = fis.read();
		// System.out.println(by);
		// System.out.println((char) by);
		//
		// // 第三次读取
		// by = fis.read();
		// System.out.println(by);
		// System.out.println((char) by);
		// // 我们发现代码的重复度很高，所以我们要用循环改进
		// // 而用循环，最麻烦的事情是如何控制循环判断条件呢?
		// // 第四次读取
		// by = fis.read();
		// System.out.println(by);
		// // 第五次读取
		// by = fis.read();
		// System.out.println(by);
		// //通过测试，我们知道如果你读取的数据是-1，就说明已经读取到文件的末尾了

		// 用循环改进
		// int by = fis.read();
		// while (by != -1) {
		// System.out.print((char) by);
		// by = fis.read();
		// }

		// 最终版代码
		int by = 0;
		// 读取，赋值，判断
		while ((by = fis.read()) != -1) {
			System.out.print((char) by);
		}
                // 数组的长度一般是1024或者1024的整数倍
		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = fis.read(bys)) != -1) {
			System.out.print(new String(bys, 0, len));
		}

		// 释放资源
		fis.close();
	}
}
```

## **5.4 字节流复制数据练习**

代码示例：把c:\\a.txt内容复制到d:\\b.txt中

```java
package cn.itcast_03;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
/*
 * 需求：把c盘下的a.txt的内容复制到d盘下的b.txt中
 * 
 * 数据源：
 * 		c:\\a.txt	--	读取数据--	FileInputStream
 * 目的地：
 * 		d:\\b.txt	--	写出数据	--	FileOutputStream
 */
public class CopyFileDemo2 {
	public static void main(String[] args) throws IOException {
		// 封装数据源
		FileInputStream fis = new FileInputStream("c:\\a.txt");
		// 封装目的地
		FileOutputStream fos = new FileOutputStream("d:\\b.txt");

		// 复制数据
		int by = 0;
		while ((by = fis.read()) != -1) {
			fos.write(by);
		}

		// 释放资源
		fos.close();
		fis.close();
	}
}
```
## **4、字节缓冲流**
一个字节一个字节的读写，需要频繁的操作文件，效率非常低。这就好比从北京运送烤鸭到上海，如果有一万只烤鸭，每次运送一只，就必须运输一万次，这样的效率显然非常低。为了减少运输次数，可以先把一批烤鸭装在车厢中，这样就可以成批的运送烤鸭，这时的车厢就相当于一个临时缓冲区。当通过流的方式拷贝文件时，为了提高效率也可以定义一个字节数组作为缓冲区。在拷贝文件时，可以一次性读取多个字节的数据，并保存在字节数组中，然后将字节数组中的数据一次性写入文件。

在IO包中提供两个带缓冲的字节流，分别是BufferedInputStream和BufferedOutputStream，它们的构造方法中分别接收InputStream和OutputStream类型的参数作为对象，在读写数据时提供缓冲功能。应用程序、缓冲流和底层字节流之间的关系如图所示。

![1500708462472](img/1500708462472.png)

字节流一次读写一个数组的速度明显比一次读写一个字节的速度快很多，这是加入了数组这样的缓冲区效果，java本身在设计的时候，也考虑到了这样的设计思想(装饰设计模式后面讲解)，所以提供了字节缓冲区流。

 字节缓冲输出流：BufferedOutputStream，字节缓冲输入流：BufferedInputStream。
```java
package cn.itcast_05;
import java.io.BufferedOutputStream;
import java.io.FileOutputStream;
import java.io.IOException;
/*
 * 通过定义数组的方式确实比以前一次读取一个字节的方式快很多，所以，看来有一个缓冲区还是非常好的。
 * 既然是这样的话，那么，java开始在设计的时候，它也考虑到了这个问题，就专门提供了带缓冲区的字节类。
 * 这种类被称为：缓冲区类(高效类)
 * 写数据：BufferedOutputStream
 * 读数据：BufferedInputStream
 * 
 * 构造方法可以指定缓冲区的大小，但是我们一般用不上，因为默认缓冲区大小就足够了。
 * 
 * 为什么不传递一个具体的文件或者文件路径，而是传递一个OutputStream对象呢?
 * 原因很简单，字节缓冲区流仅仅提供缓冲区，为高效而设计的。但是呢，真正的读写操作还得靠基本的流对象实现。
 */
public class BufferedOutputStreamDemo {
	public static void main(String[] args) throws IOException {
		// BufferedOutputStream(OutputStream out)
		// FileOutputStream fos = new FileOutputStream("bos.txt");
		// BufferedOutputStream bos = new BufferedOutputStream(fos);
		// 简单写法
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream("bos.txt"));

		// 写数据
		bos.write("hello".getBytes());

		// 释放资源
		bos.close();
	}
}
```
## **5.5 字节缓冲流复制数据练习**

```java
package cn.itcast_06;
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
/*
 * 需求：把e:\\a.mp4复制到当前项目目录下的b.mp4中
 * 
 * 字节流四种方式复制文件：
 * 基本字节流一次读写一个字节：	共耗时：117235毫秒
 * 基本字节流一次读写一个字节数组： 共耗时：156毫秒
 * 高效字节流一次读写一个字节： 共耗时：1141毫秒
 * 高效字节流一次读写一个字节数组： 共耗时：47毫秒
 */
public class CopyMp4Demo {
	public static void main(String[] args) throws IOException {
		long start = System.currentTimeMillis();
		// method1("e:\\a.mp4", "copy1.mp4");
		// method2("e:\\a.mp4", "copy2.mp4");
		// method3("e:\\a.mp4", "copy3.mp4");
		method4("e:\\a.mp4", "copy4.mp4");
		long end = System.currentTimeMillis();
		System.out.println("共耗时：" + (end - start) + "毫秒");
	}

	// 高效字节流一次读写一个字节数组：
	public static void method4(String srcString, String destString)
			throws IOException {
		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(
				srcString));
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream(destString));

		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = bis.read(bys)) != -1) {
			bos.write(bys, 0, len);
		}

		bos.close();
		bis.close();
	}

	// 高效字节流一次读写一个字节：
	public static void method3(String srcString, String destString)
			throws IOException {
		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(
				srcString));
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream(destString));

		int by = 0;
		while ((by = bis.read()) != -1) {
			bos.write(by);

		}

		bos.close();
		bis.close();
	}

	// 基本字节流一次读写一个字节数组
	public static void method2(String srcString, String destString)
			throws IOException {
		FileInputStream fis = new FileInputStream(srcString);
		FileOutputStream fos = new FileOutputStream(destString);

		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = fis.read(bys)) != -1) {
			fos.write(bys, 0, len);
		}

		fos.close();
		fis.close();
	}

	// 基本字节流一次读写一个字节
	public static void method1(String srcString, String destString)
			throws IOException {
		FileInputStream fis = new FileInputStream(srcString);
		FileOutputStream fos = new FileOutputStream(destString);

		int by = 0;
		while ((by = fis.read()) != -1) {
			fos.write(by);
		}

		fos.close();
		fis.close();
	}
}
```
# **6. 字符流**

InputStream类和OutputStream类在读写文件时操作的都是字节，如果希望在程序中操作字符，使用这两个类就不太方便，为此JDK提供了字符流。同字节流一样，字符流也有两个抽象的顶级父类，分别是Reader和Writer。其中Reader是字符输入流，用于从某个源设备读取字符，Writer是字符输出流，用于向某个目标设备写入字符。Reader和Writer作为字符流的顶级父类，也有许多子类，接下来通过继承关系图来列出Reader和Writer的一些常用子类，如图所示。

![1500708507437](img/1500708507437.png)

![1500708514090](img/1500708514090.png)

## **6.1 转换流出现的原因及思想**
由于字节流操作中文不是特别方便，所以，java就提供了转换流。字符流=字节流+编码表。

编码表：由字符及其对应的数值组成的一张表

常见编码表

计算机只能识别二进制数据，早期由来是电信号。为了方便应用计算机，让它可以识别各个国家的文字。就将各个国家的文字用数字来表示，并一一对应，形成一张表。

![io流](http://img.blog.csdn.net/20150812155310074)

字符串中的编码问题

编码：把看得懂的变成看不懂的
解码：把看不懂的变成看得懂

```java
package cn.itcast_01;
import java.io.UnsupportedEncodingException;
import java.util.Arrays;
/*
 * String(byte[] bytes, String charsetName):通过指定的字符集解码字节数组
 * byte[] getBytes(String charsetName):使用指定的字符集合把字符串编码为字节数组
 * 
 * 编码:把看得懂的变成看不懂的
 * String -- byte[]
 * 
 * 解码:把看不懂的变成看得懂的
 * byte[] -- String
 * 
 * 举例：谍战片(发电报，接电报)
 * 
 * 码表：小本子
 * 		字符	数值
 * 
 * 要发送一段文字：
 * 		今天晚上在老地方见
 * 
 * 		发送端：今 -- 数值 -- 二进制 -- 发出去
 * 		接收端：接收 -- 二进制 -- 十进制 -- 数值 -- 字符 -- 今
 * 
 * 		今天晚上在老地方见
 * 
 * 编码问题简单，只要编码解码的格式是一致的。
 */
public class StringDemo {
	public static void main(String[] args) throws UnsupportedEncodingException {
		String s = "你好";

		// String -- byte[]
		byte[] bys = s.getBytes(); // [-60, -29, -70, -61]
		// byte[] bys = s.getBytes("GBK");// [-60, -29, -70, -61]
		// byte[] bys = s.getBytes("UTF-8");// [-28, -67, -96, -27, -91, -67]
		System.out.println(Arrays.toString(bys));

		// byte[] -- String
		String ss = new String(bys); // 你好
		// String ss = new String(bys, "GBK"); // 你好
		// String ss = new String(bys, "UTF-8"); // ???
		System.out.println(ss);
	}
}
```
## **6.2 转换流概述**

IO流可分为字节流和字符流，有时字节流和字符流之间也需要进行转换。在JDK中提供了两个类可以将字节流转换为字符流，它们分别是InputStreamReader和OutputStreamWriter。

OutputStreamWriter是Writer的子类，它可以将一个字节输出流转换成字符输出流，方便直接写入字符，而InputStreamReader是Reader的子类，它可以将一个字节输入流转换成字符输入流，方便直接读取字符。通过转换流进行数据读写的过程如图所示。

![1500708562018](img/1500708562018.png)

OutputStreamWriter 字符输出流

![io流](http://img.blog.csdn.net/20150915111452739)

InputStreamReader 字符输入流

![io流](http://img.blog.csdn.net/20150915111611235)

**OutputStreamWriter写数据**

| 方法                                 | 功能描述         |
| ---------------------------------- | ------------ |
| write(int c)                       | 写入一个字符       |
| write(char[] cbuf)                 | 写入一个字符数组     |
| write(char[] cbuf,int off,int len) | 写入一个字符数组的一部分 |
| write(String str)                  | 写入一个字符串      |
| write(String str,int off,int len)  | 写入一个字符串的一部分  |
<br>
字符流操作要注意的问题：

字符流数据没有直接进文件而是到缓冲区，所以要刷新缓冲区。

**flush()和close()的区别：**

- A：close()关闭流对象，但是先刷新一次缓冲区。关闭之后，流对象不可以继续再使用了。
- B：flush()仅仅刷新缓冲区,刷新之后，流对象还可以继续使用。

```java
package cn.itcast_03;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;
/*
 * OutputStreamWriter的方法：
 * public void write(int c):写一个字符
 * public void write(char[] cbuf):写一个字符数组
 * public void write(char[] cbuf,int off,int len):写一个字符数组的一部分
 * public void write(String str):写一个字符串
 * public void write(String str,int off,int len):写一个字符串的一部分
 */
public class OutputStreamWriterDemo {
	public static void main(String[] args) throws IOException {
		// 创建对象
		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream(
				"osw2.txt"));

		// 写数据
		// public void write(int c):写一个字符
		// osw.write('a');
		// osw.write(97);
		// 为什么数据没有进去呢?
		// 原因是：字符 = 2字节
		// 文件中数据存储的基本单位是字节。
		// void flush()

		// public void write(char[] cbuf):写一个字符数组
		// char[] chs = {'a','b','c','d','e'};
		// osw.write(chs);

		// public void write(char[] cbuf,int off,int len):写一个字符数组的一部分
		// osw.write(chs,1,3);

		// public void write(String str):写一个字符串
		// osw.write("我爱林青霞");

		// public void write(String str,int off,int len):写一个字符串的一部分
		osw.write("我爱林青霞", 2, 3);

		// 刷新缓冲区
		osw.flush();
		// osw.write("我爱林青霞", 2, 3);

		// 释放资源
		osw.close();
		// java.io.IOException: Stream closed
		// osw.write("我爱林青霞", 2, 3);
	}
}
```
## **6.3 InputStreamReader读数据**
- public int read()：一次读一个字符 
- public int read(char[] cbuf)：一次读一个字符数组

```java
package cn.itcast_03;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStreamReader;
/*
 * InputStreamReader的方法：
 * int read():一次读取一个字符
 * int read(char[] chs):一次读取一个字符数组
 */
public class InputStreamReaderDemo {
	public static void main(String[] args) throws IOException {
		// 创建对象
		InputStreamReader isr = new InputStreamReader(new FileInputStream(
				"StringDemo.java"));

		// 一次读取一个字符
		// int ch = 0;
		// while ((ch = isr.read()) != -1) {
		// System.out.print((char) ch);
		// }

		// 一次读取一个字符数组
		char[] chs = new char[1024];
		int len = 0;
		while ((len = isr.read(chs)) != -1) {
			System.out.print(new String(chs, 0, len));
		}

		// 释放资源
		isr.close();
	}
}
```
## **6.4 字符流复制文本文件**

```java
package cn.itcast_04;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
/*
 * 需求：把当前项目目录下的a.txt内容复制到当前项目目录下的b.txt中
 * 
 * 数据源：
 * 		a.txt -- 读取数据 -- 字符转换流 -- InputStreamReader
 * 目的地：
 * 		b.txt -- 写出数据 -- 字符转换流 -- OutputStreamWriter
 */
public class CopyFileDemo {
	public static void main(String[] args) throws IOException {
		// 封装数据源
		InputStreamReader isr = new InputStreamReader(new FileInputStream(
				"a.txt"));
		// 封装目的地
		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream(
				"b.txt"));

		// 读写数据
		// 方式1
		// int ch = 0;
		// while ((ch = isr.read()) != -1) {
		// osw.write(ch);
		// }

		// 方式2
		char[] chs = new char[1024];
		int len = 0;
		while ((len = isr.read(chs)) != -1) {
			osw.write(chs, 0, len);
			// osw.flush();
		}

		// 释放资源
		osw.close();
		isr.close();
	}
}
```
## **6.5 转换流的简化写法**
转换流的名字比较长，而我们常见的操作都是按照本地默认编码实现的，所以，为了简化我们的书写，转换流提供了对应的子类。

**FileWriter**

![io流](http://img.blog.csdn.net/20150915111939505)

代码示例：把当前项目目录下的a.txt内容复制到当前项目目录下的b.txt中

```java
package cn.itcast_04;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
/*
 * 由于我们常见的操作都是使用本地默认编码，所以，不用指定编码。
 * 而转换流的名称有点长，所以，Java就提供了其子类供我们使用。
 * OutputStreamWriter = FileOutputStream + 编码表(GBK)
 * FileWriter = FileOutputStream + 编码表(GBK)
 * 
 * InputStreamReader = FileInputStream + 编码表(GBK)
 * FileReader = FileInputStream + 编码表(GBK)
 * 
 /*
 * 需求：把当前项目目录下的a.txt内容复制到当前项目目录下的b.txt中
 * 
 * 数据源：
 * 		a.txt -- 读取数据 -- 字符转换流 -- InputStreamReader -- FileReader
 * 目的地：
 * 		b.txt -- 写出数据 -- 字符转换流 -- OutputStreamWriter -- FileWriter
 */
public class CopyFileDemo2 {
	public static void main(String[] args) throws IOException {
		// 封装数据源
		FileReader fr = new FileReader("a.txt");
		// 封装目的地
		FileWriter fw = new FileWriter("b.txt");

		// 一次一个字符
		// int ch = 0;
		// while ((ch = fr.read()) != -1) {
		// fw.write(ch);
		// }

		// 一次一个字符数组
		char[] chs = new char[1024];
		int len = 0;
		while ((len = fr.read(chs)) != -1) {
			fw.write(chs, 0, len);
			fw.flush();
		}

		// 释放资源
		fw.close();
		fr.close();
	}
}
```
## **6.6 FileReader**

![io流](http://img.blog.csdn.net/20150915112037930)

代码示例：把c:\\a.txt内容复制到d:\\b.txt中

```java
package cn.itcast_04;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
/*
 * 需求：把c:\\a.txt内容复制到d:\\b.txt中
 * 
 * 数据源：
 * 		c:\\a.txt -- FileReader
 * 目的地:
 * 		d:\\b.txt -- FileWriter
 */
public class CopyFileDemo3 {
	public static void main(String[] args) throws IOException {
		// 封装数据源
		FileReader fr = new FileReader("c:\\a.txt");
		// 封装目的地
		FileWriter fw = new FileWriter("d:\\b.txt");

		// 读写数据
		// int ch = 0;
		int ch;
		while ((ch = fr.read()) != -1) {
			fw.write(ch);
		}
		
		//释放资源
		fw.close();
		fr.close();
	}
}
```
# **7. 字符缓冲流**
字符流为了高效读写，也提供了对应的字符缓冲流。BufferedWriter：字符缓冲输出流，BufferedReader：字符缓冲输入流。

## **7.1 BufferedWriter基本用法**
将文本写入字符输出流，缓冲各个字符，从而提供单个字符、数组和字符串的高效写入。 可以指定缓冲区的大小，或者接受默认的大小。在大多数情况下，默认值就足够大了。 

![io流](http://img.blog.csdn.net/20150915112318443)

![io流](http://img.blog.csdn.net/20150915112318443)

代码示例：BufferedWriter基本用法

```java
package cn.itcast_05;
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
/**
 * 字符流为了高效读写，也提供了对应的字符缓冲流。
 * BufferedWriter:字符缓冲输出流
 * BufferedReader:字符缓冲输入流
 * 
 * BufferedWriter:字符缓冲输出流
 * 将文本写入字符输出流，缓冲各个字符，从而提供单个字符、数组和字符串的高效写入。 
 * 可以指定缓冲区的大小，或者接受默认的大小。在大多数情况下，默认值就足够大了。 
 */
public class BufferedWriterDemo {
	public static void main(String[] args) throws IOException {
		// BufferedWriter(Writer out)
		// BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(
		// new FileOutputStream("bw.txt")));

		BufferedWriter bw = new BufferedWriter(new FileWriter("bw.txt"));

		bw.write("hello");
		bw.write("world");
		bw.write("java");
		bw.flush();

		bw.close();
	}
}
```
##**7.2 BufferedReader基本用法**
从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。  可以指定缓冲区的大小，或者可使用默认的大小。大多数情况下，默认值就足够大了。 

![io流](http://img.blog.csdn.net/20150915112441236)

![io流](http://img.blog.csdn.net/20150915112520875)

代码示例：  BufferedReader基本用法

```java
package cn.itcast_05;
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
/**
 * BufferedReader
 * 从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。 
 * 可以指定缓冲区的大小，或者可使用默认的大小。大多数情况下，默认值就足够大了。 
 * 
 * BufferedReader(Reader in)
 */
public class BufferedReaderDemo {
	public static void main(String[] args) throws IOException {
		// 创建字符缓冲输入流对象
		BufferedReader br = new BufferedReader(new FileReader("bw.txt"));

		// 方式1
		// int ch = 0;
		// while ((ch = br.read()) != -1) {
		// System.out.print((char) ch);
		// }

		// 方式2
		char[] chs = new char[1024];
		int len = 0;
		while ((len = br.read(chs)) != -1) {
			System.out.print(new String(chs, 0, len));
		}

		// 释放资源
		br.close();
	}
}
```
## **7.3 特殊功能**

- BufferedWriter，newLine()：根据系统来决定换行符
- BufferedReader，String readLine()：一次读取一行数据

代码示例：字符缓冲流的特殊方法

```java
package cn.itcast_05;
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
/*
 * 字符缓冲流的特殊方法：
 * BufferedWriter:
 * 		public void newLine():根据系统来决定换行符
 * BufferedReader:
 * 		public String readLine()：一次读取一行数据
 * 		包含该行内容的字符串，不包含任何行终止符，如果已到达流末尾，则返回 null
 */
public class BufferedDemo {
	public static void main(String[] args) throws IOException {
		// write();
		read();
	}

	private static void read() throws IOException {
		// 创建字符缓冲输入流对象
		BufferedReader br = new BufferedReader(new FileReader("bw2.txt"));

		// public String readLine()：一次读取一行数据
		// String line = br.readLine();
		// System.out.println(line);
		// line = br.readLine();
		// System.out.println(line);

		// 最终版代码
		String line = null;
		while ((line = br.readLine()) != null) {
			System.out.println(line);
		}
		
		//释放资源
		br.close();
	}

	private static void write() throws IOException {
		// 创建字符缓冲输出流对象
		BufferedWriter bw = new BufferedWriter(new FileWriter("bw2.txt"));
		for (int x = 0; x < 10; x++) {
			bw.write("hello" + x);
			// bw.write("\r\n");
			bw.newLine();
			bw.flush();
		}
		bw.close();
	}

}
```
**代码示例：字符缓冲流复制文本文件**

```java
package cn.itcast_06;
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
/*
 * 需求：把当前项目目录下的a.txt内容复制到当前项目目录下的b.txt中
 * 
 * 数据源：
 * 		a.txt -- 读取数据 -- 字符转换流 -- InputStreamReader -- FileReader -- BufferedReader
 * 目的地：
 * 		b.txt -- 写出数据 -- 字符转换流 -- OutputStreamWriter -- FileWriter -- BufferedWriter
 */
public class CopyFileDemo2 {
	public static void main(String[] args) throws IOException {
		// 封装数据源
		BufferedReader br = new BufferedReader(new FileReader("a.txt"));
		// 封装目的地
		BufferedWriter bw = new BufferedWriter(new FileWriter("b.txt"));

		// 读写数据
		String line = null;
		while ((line = br.readLine()) != null) {
			bw.write(line);
			bw.newLine();
			bw.flush();
		}

		// 释放资源
		bw.close();
		br.close();
	}
}
```

# 8. 模拟记事本

```java
package cn.itcast.chapter07.task02;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.Scanner;
/**
 * 模拟记事本程序
 */
public class Notepad {
	private static String filePath;
	private static String message = "";
	public static void main(String[] args) throws Exception {
		Scanner sc = new Scanner(System.in);
		System.out.println("--1:新建文件 2:打开文件  3:修改文件  4:保存 5:退出--");
		while (true) {
			System.out.print("请输入操作指令：");
			int command = sc.nextInt();
			switch (command) {
			case 1:
				createFile();// 1:新建文件
				break;
			case 2:
				openFile();// 2:打开文件
				break;
			case 3:
				editFile();// 3:修改文件
				break;
			case 4:
				saveFile();// 4:保存
				break;
			case 5:
				exit();// 5:退出
				break;
			default:
				System.out.println("您输入的指令错误！");
				break;
			}
		}
	}
	/**
	 * 新建文件 从控制台获取内容
	 */
	private static void createFile() {
		message = "";// 新建文件时，暂存文件内容清空
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入内容，停止编写请输入\"stop\":");// 提示
		StringBuffer stb = new StringBuffer();// 用于后期输入内容的拼接
		String inputMessage = "";
		while (!inputMessage.equals("stop")) {// 当输入“stop”时，停止输入
			if (stb.length() > 0) {
				stb.append("\r\n");// 追加换行符
			}
			stb.append(inputMessage);// 拼接输入信息
			inputMessage = sc.nextLine();// 获取输入信息
		}
		message = stb.toString();// 将输入内容暂存
	}
	/**
	 * 打开文件
	 */
	private static void openFile() throws Exception {
		message = "";// 打开文件时，将暂存内容清空
		Scanner sc = new Scanner(System.in);
		System.out.print("请输入打开文件的位置：");
		filePath = sc.next();// 获取打开文件的路径
		// 控制只能输入txt格式的文件路径
		if (filePath != null && !filePath.endsWith(".txt")) {
			System.out.print("请选择文本文件！");
			return;
		}
		FileReader in = new FileReader(filePath);// 实例化一个FileReader对象
		char[] charArray = new char[1024];// 缓冲数组
		int len = 0;
		StringBuffer sb = new StringBuffer();
		// 循环读取，一次读取一个字符数组
		while ((len = in.read(charArray)) != -1) {
			sb.append(charArray);
		}
		message = sb.toString();// 将打开文件内容暂存
		System.out.println("打开文件内容：" + "\r\n" + message);
		in.close();// 释放资源
	}
	/**
	 * 修改文件内容 通过字符串替换的形式
	 */
	private static void editFile() {
		if (message == "" && filePath == null) {
			System.out.println("请先新建文件或者打开文件");
			return;
		}
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入要修改的内容（以 \"修改的目标文字:修改之后的文字\" 格式）,"
				+ "停止修改请输入\"stop\":");
		String inputMessage = "";
		while (!inputMessage.equals("stop")) {// 当输入stop时,停止修改
			inputMessage = sc.nextLine();
			if (inputMessage != null && inputMessage.length() > 0) {
				// 将输入的文字根据“：”拆分成数组
				String[] editMessage = inputMessage.split(":");
				if (editMessage != null && editMessage.length > 1) {
					// 根据输入的信息将文件中内容替换
					message = message.replace(editMessage[0], editMessage[1]);
				}
			}
		}
		System.out.println("修改后的内容:" + "\r\n" + message);
	}
	/**
	 * 保存 新建文件存在用户输入的路径 打开的文件将原文件覆盖
	 */
	private static void saveFile() throws IOException {
		Scanner sc = new Scanner(System.in);
		FileWriter out = null;
		if (filePath != null) {// 文件是由“打开”载入的
			out = new FileWriter(filePath);// 将原文件覆盖
		} else {// 新建的文件
			System.out.print("请输入文件保存的绝对路径：");
			String path = sc.next();// 获取文件保存的路径
			filePath = path;
			// 将输入路径中大写字母替换成小写字母后判断是不是文本格式
			if (!filePath.toLowerCase().endsWith(".txt")) {
				filePath += ".txt";
			}
			out = new FileWriter(filePath);// 构造输出流
		}
		out.write(message);// 写入暂存的内容
		out.close();// 关闭输出流
		message = "";// 修改文件前现将写入内容置空
		filePath = null;// 将文件路径至null
	}
	/**
	 * 退出
	 */
	private static void exit() {
		System.out.println("您已退出系统，谢谢使用！");
		System.exit(0);
	}
}
```

# 9. 总结

![io流](http://img.blog.csdn.net/20150827230556993)

![io流](http://img.blog.csdn.net/20150812142557398)