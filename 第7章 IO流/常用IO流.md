# 1. 数据流
## 1.1 概述

数据流是操作基本数据类型的流，分为数据输入流，数据输出流。

## 1.2 数据输入流

1、DataOutputStream：数据输出流允许应用程序以适当方式将基本 Java 数据类型写入输出流中。然后，应用程序可以使用数据输入流将数据读入。

2、构造方法
DataOutputStream(OutputStream out) ：创建一个新的数据输出流，将数据写入指定基础输出流。

## 1.3 数据输出流

1、DataInputStream：数据输入流允许应用程序以与机器无关方式从底层输入流中读取基本 Java 数据类型。应用程序可以使用数据输出流写入稍后由数据输入流读取的数据。

2、构造方法
   DataInputStream(InputStream in) ：使用指定的底层 InputStream 创建一个 DataInputStream。

```java
package cn.itcast_01;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

/*
 * 可以读写基本数据类型的数据
 * 数据输入流：DataInputStream
 * 			DataInputStream(InputStream in)
 * 数据输出流：DataOutputStream
 * 			DataOutputStream(OutputStream out) 
 */
public class DataStreamDemo {
	public static void main(String[] args) throws IOException {
		// 写
		// write();

		// 读
		read();
	}

	private static void read() throws IOException {
		// DataInputStream(InputStream in)
		// 创建数据输入流对象
		DataInputStream dis = new DataInputStream(
				new FileInputStream("dos.txt"));

		// 读数据
		byte b = dis.readByte();
		short s = dis.readShort();
		int i = dis.readInt();
		long l = dis.readLong();
		float f = dis.readFloat();
		double d = dis.readDouble();
		char c = dis.readChar();
		boolean bb = dis.readBoolean();

		// 释放资源
		dis.close();

		System.out.println(b);
		System.out.println(s);
		System.out.println(i);
		System.out.println(l);
		System.out.println(f);
		System.out.println(d);
		System.out.println(c);
		System.out.println(bb);
	}

	private static void write() throws IOException {
		// DataOutputStream(OutputStream out)
		// 创建数据输出流对象
		DataOutputStream dos = new DataOutputStream(new FileOutputStream(
				"dos.txt"));

		// 写数据了
		dos.writeByte(10);
		dos.writeShort(100);
		dos.writeInt(1000);
		dos.writeLong(10000);
		dos.writeFloat(12.34F);
		dos.writeDouble(12.56);
		dos.writeChar('a');
		dos.writeBoolean(true);

		// 释放资源
		dos.close();
	}
}
```
运行结果：

![](http://img.blog.csdn.net/20150812193046398?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

HttpURLConnection设置请求参数

```java
    protected void setRequestParams(HttpURLConnection connection, Request<?> request)
            throws ProtocolException, IOException {
        Request.HttpMethod method = request.getHttpMethod();
        connection.setRequestMethod(method.toString());
        // add params
        byte[] body = request.getBody();
        if (body != null) {
            // enable output
            connection.setDoOutput(true);
            // set content type
            connection.addRequestProperty(Request.HEADER_CONTENT_TYPE, request.getBodyContentType());
            // write params data to connection
            DataOutputStream dataOutputStream = new DataOutputStream(connection.getOutputStream());
            dataOutputStream.write(body);
            dataOutputStream.close();
        }
    }
```

# 2. 内存操作流

## 2.1 概述
内存操作流：用于处理临时存储信息的，程序结束，数据就从内存中消失。

## 2.2 操作字节数组

### 2.2.1 ByteArrayInputStream

ByteArrayInputStream 包含一个内部缓冲区，该缓冲区包含从流中读取的字节。内部计数器跟踪 read 方法要提供的下一个字节。 

关闭 ByteArrayInputStream 无效。此类中的方法在关闭此流后仍可被调用，而不会产生任何 IOException。 

![](http://img.blog.csdn.net/20150915113704568)
### 2.2.2 ByteArrayOutputStream

此类实现了一个输出流，其中的数据被写入一个 byte 数组。缓冲区会随着数据的不断写入而自动增长。可使用 toByteArray() 和 toString() 获取数据。 

关闭 ByteArrayOutputStream 无效。此类中的方法在关闭此流后仍可被调用，而不会产生任何 IOException。

![](http://img.blog.csdn.net/20150915113809621)
# 3. 操作字符数组

## 3.1 CharArrayReader

此类实现一个可用作字符输入流的字符缓冲区。

```java
CharArrayReader(char[] buf) // 根据指定的 char 数组创建一个 CharArrayReader。 
CharArrayReader(char[] buf, int offset, int length) // 根据指定的 char 数组创建一个 CharArrayReader。 
```

## 3.2 CharArrayWriter

此类实现一个可用作 Writer 的字符缓冲区。缓冲区会随向流中写入数据而自动增长。可使用 toCharArray() 和 toString() 获取数据。 

PS：在此类上调用 close() 无效，并且在关闭该流后可以调用此类中的各个方法，而不会产生任何 IOException。

```java
CharArrayWriter() // 创建一个新的 CharArrayWriter。 
CharArrayWriter(int initialSize) // 创建一个具有指定初始大小的新 CharArrayWriter。 
```

# 4. 操作字符串

## 4.1 StringReader

其源为一个字符串的字符流。StringReader(String s) ：创建一个新字符串 reader。

## 4.2 StringWriter

一个字符流，可以用其回收在字符串缓冲区中的输出来构造字符串。 
关闭 StringWriter 无效。此类中的方法在关闭该流后仍可被调用，而不会产生任何 IOException。 

```java
StringWriter() // 使用默认初始字符串缓冲区大小创建一个新字符串 writer。 
StringWriter(int initialSize) // 使用指定初始字符串缓冲区大小创建一个新字符串 writer。 
```

```java
package cn.itcast_02;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;

/*
 * 内存操作流：用于处理临时存储信息的，程序结束，数据就从内存中消失。
 * 字节数组：
 * 		ByteArrayInputStream
 * 		ByteArrayOutputStream
 * 字符数组：
 * 		CharArrayReader
 * 		CharArrayWriter
 * 字符串：
 * 		StringReader
 * 		StringWriter
 */
public class ByteArrayStreamDemo {
	public static void main(String[] args) throws IOException {
		// 写数据
		// ByteArrayOutputStream()
		ByteArrayOutputStream baos = new ByteArrayOutputStream();

		// 写数据
		for (int x = 0; x < 10; x++) {
			baos.write(("hello" + x).getBytes());
		}

		// 释放资源
		// 通过查看源码我们知道这里什么都没做，所以根本需要close()
		// baos.close();

		// public byte[] toByteArray()
		byte[] bys = baos.toByteArray();

		// 读数据
		// ByteArrayInputStream(byte[] buf)
		ByteArrayInputStream bais = new ByteArrayInputStream(bys);

		int by = 0;
		while ((by = bais.read()) != -1) {
			System.out.print((char) by);
		}

		// bais.close();
	}
}
```
运行结果：

![](http://img.blog.csdn.net/20150812194600220)
# 5. 打印流

## 5.1 打印流概述
打印流分为： 字节流打印流PrintStream和字符打印流PrintWriter。

## 5.2 字节流打印流

PrintStream 为其他输出流添加了功能，使它们能够方便地打印各种数据值表示形式。它还提供其他两项功能。与其他输出流不同，PrintStream 永远不会抛出 IOException；而是，异常情况仅设置可通过 checkError 方法测试的内部标志。另外，为了自动刷新，可以创建一个 PrintStream；这意味着可在写入 byte 数组之后自动调用 flush 方法，可调用其中一个 println 方法，或写入一个换行符或字节 ('\n')。 
PrintStream 打印的所有字符都使用平台的默认字符编码转换为字节。在需要写入字符而不是写入字节的情况下，应该使用 PrintWriter 类。

![](http://img.blog.csdn.net/20150915113935210)  

## 5.3 字符打印流

向文本输出流打印对象的格式化表示形式。此类实现在 PrintStream 中的所有 print 方法。它不包含用于写入原始字节的方法，对于这些字节，程序应该使用未编码的字节流进行写入。

与 PrintStream 类不同，如果启用了自动刷新，则只有在调用 println、printf 或 format 的其中一个方法时才可能完成此操作，而不是每当正好输出换行符时才完成。这些方法使用平台自有的行分隔符概念，而不是换行符。 

此类中的方法不会抛出 I/O 异常，尽管其某些构造方法可能抛出异常。客户端可能会查询调用 checkError() 是否出现错误。

![](http://img.blog.csdn.net/20150915114040727)
```java
package cn.itcast_03;

import java.io.IOException;
import java.io.PrintWriter;

/*
 * 打印流
 * 字节流打印流	PrintStream
 * 字符打印流	PrintWriter
 * 
 * 打印流的特点：
 * 		A:只有写数据的，没有读取数据。只能操作目的地，不能操作数据源。
 * 		B:可以操作任意类型的数据。
 * 		C:如果启动了自动刷新，能够自动刷新。
 * 		D:该流是可以直接操作文本文件的。
 * 			哪些流对象是可以直接操作文本文件的呢?
 * 			FileInputStream
 * 			FileOutputStream
 * 			FileReader
 * 			FileWriter
 * 			PrintStream
 * 			PrintWriter
 * 			看API,查流对象的构造方法，如果同时有File类型和String类型的参数，一般来说就是可以直接操作文件的。
 * 
 * 			流：
 * 				基本流：就是能够直接读写文件的
 * 				高级流：在基本流基础上提供了一些其他的功能
 */
public class PrintWriterDemo {
	public static void main(String[] args) throws IOException {
		// 作为Writer的子类使用
		PrintWriter pw = new PrintWriter("pw.txt");

		pw.write("hello");
		pw.write("world");
		pw.write("java");
		
		pw.close();
	}
}

package cn.itcast_03;

import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;

/*
 * 1:可以操作任意类型的数据。
 * 		print()
 * 		println()
 * 2:启动自动刷新
 * 		PrintWriter pw = new PrintWriter(new FileWriter("pw2.txt"), true);
 * 		还是应该调用println()的方法才可以
 * 		这个时候不仅仅自动刷新了，还实现了数据的换行。
 * 
 * 		println()
 *		其实等价于于：
 *		bw.write();
 *		bw.newLine();		
 *		bw.flush();
 */
public class PrintWriterDemo2 {
	public static void main(String[] args) throws IOException {
		// 创建打印流对象
		// PrintWriter pw = new PrintWriter("pw2.txt");
		PrintWriter pw = new PrintWriter(new FileWriter("pw2.txt"), true);

		// write()是搞不定的，怎么办呢?
		// 我们就应该看看它的新方法
		// pw.print(true);
		// pw.print(100);
		// pw.print("hello");

		pw.println("hello");
		pw.println(true);
		pw.println(100);

		pw.close();
	}
}
```

## 5.4 打印流特点

- 只能操作目的地，不能操作数据。
- 可以操作任意类型的数据。
- 如果启动了自动刷新，能够自动刷新。
- 可以操作文件的流

## 5.5 打印流复制文本文件

```java
package cn.itcast_03;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;

/*
 * 需求：DataStreamDemo.java复制到Copy.java中
 * 数据源：
 * 		DataStreamDemo.java -- 读取数据 -- FileReader -- BufferedReader
 * 目的地：
 * 		Copy.java -- 写出数据 -- FileWriter -- BufferedWriter -- PrintWriter
 */
public class CopyFileDemo {
	public static void main(String[] args) throws IOException {
		// 以前的版本
		// 封装数据源
		// BufferedReader br = new BufferedReader(new FileReader(
		// "DataStreamDemo.java"));
		// // 封装目的地
		// BufferedWriter bw = new BufferedWriter(new FileWriter("Copy.java"));
		//
		// String line = null;
		// while ((line = br.readLine()) != null) {
		// bw.write(line);
		// bw.newLine();
		// bw.flush();
		// }
		//
		// bw.close();
		// br.close();

		// 打印流的改进版
		// 封装数据源
		BufferedReader br = new BufferedReader(new FileReader(
				"DataStreamDemo.java"));
		// 封装目的地
		PrintWriter pw = new PrintWriter(new FileWriter("Copy.java"), true);
		
		String line = null;
		while((line=br.readLine())!=null){
			pw.println(line);
		}
		
		pw.close();
		br.close();
	}
}
```

# 6. 标准输入输出流

## 6.1 标准输入输出流

- System类中的字段：in，out。
- 它们各代表了系统标准的输入和输出设备。
- 默认输入设备是键盘，输出设备是显示器。
- System.in的类型是InputStream.
- System.out的类型是PrintStream是OutputStream的子类FilterOutputStream 的子类.

代码示例：System.in 标准输入流。是从键盘获取数据的

```java
package cn.itcast_04;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

/*
 * System.in 标准输入流。是从键盘获取数据的
 * 
 * 键盘录入数据：
 * 		A:main方法的args接收参数。
 * 			java HelloWorld hello world java
 * 		B:Scanner(JDK5以后的)
 * 			Scanner sc = new Scanner(System.in);
 * 			String s = sc.nextLine();
 * 			int x = sc.nextInt()
 * 		C:通过字符缓冲流包装标准输入流实现
 * 			BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
 */
public class SystemInDemo {
	public static void main(String[] args) throws IOException {
		// //获取标准输入流
		// InputStream is = System.in;
		// //我要一次获取一行行不行呢?
		// //行。
		// //怎么实现呢?
		// //要想实现，首先你得知道一次读取一行数据的方法是哪个呢?
		// //readLine()
		// //而这个方法在哪个类中呢?
		// //BufferedReader
		// //所以，你这次应该创建BufferedReader的对象，但是底层还是的使用标准输入流
		// // BufferedReader br = new BufferedReader(is);
		// //按照我们的推想，现在应该可以了，但是却报错了
		// //原因是：字符缓冲流只能针对字符流操作，而你现在是字节流，所以不能是用?
		// //那么，我还就想使用了，请大家给我一个解决方案?
		// //把字节流转换为字符流，然后在通过字符缓冲流操作
		// InputStreamReader isr = new InputStreamReader(is);
		// BufferedReader br= new BufferedReader(isr);
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

		System.out.println("请输入一个字符串：");
		String line = br.readLine();
		System.out.println("你输入的字符串是：" + line);

		System.out.println("请输入一个整数：");
		// int i = Integer.parseInt(br.readLine());
		line = br.readLine();
		int i = Integer.parseInt(line);
		System.out.println("你输入的整数是：" + i);
	}
}
```
运行结果：

![](http://img.blog.csdn.net/20150812201249023)

代码示例：标准输入输出流

```java
package cn.itcast_04;

import java.io.PrintStream;

/*
 * 标准输入输出流
 * System类中的两个成员变量：
 *		public static final InputStream in “标准”输入流。
 * 		public static final PrintStream out “标准”输出流。
 * 
 * 		InputStream is = System.in;
 * 		PrintStream ps = System.out;
 */
public class SystemOutDemo {
	public static void main(String[] args) {
		// 有这里的讲解我们就知道了，这个输出语句其本质是IO流操作，把数据输出到控制台。
		System.out.println("helloworld");

		// 获取标准输出流对象
		PrintStream ps = System.out;
		ps.println("helloworld");
		
		ps.println();
		// ps.print();//这个方法不存在
		
		// System.out.println();
		// System.out.print();
	}
}
代码示例：转换流的应用
package cn.itcast_04;

import java.io.BufferedWriter;
import java.io.IOException;
import java.io.OutputStreamWriter;
/*
 * 转换流的应用。
 */
public class SystemOutDemo2 {
	public static void main(String[] args) throws IOException {
		// 获取标准输入流
		// // PrintStream ps = System.out;
		// // OutputStream os = ps;
		// OutputStream os = System.out; // 多态
		// // 我能不能按照刚才使用标准输入流的方式一样把数据输出到控制台呢?
		// OutputStreamWriter osw = new OutputStreamWriter(os);
		// BufferedWriter bw = new BufferedWriter(osw);
		BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(
				System.out));

		bw.write("hello");
		bw.newLine();
		// bw.flush();
		bw.write("world");
		bw.newLine();
		// bw.flush();
		bw.write("java");
		bw.newLine();
		bw.flush();
		
		bw.close();
	}
}
```
运行结果：

![](http://img.blog.csdn.net/20150812201726994)

# 7. 随机访问流

## 7.1 RandomAccessFile概述

RandomAccessFile类不属于流，是Object类的子类。但它融合了InputStream和OutputStream的功能。

此类的实例支持对随机访问文件的读取和写入。随机访问文件的行为类似存储在文件系统中的一个大型 byte 数组。存在指向该隐含数组的光标或索引，称为文件指针；输入操作从文件指针开始读取字节，并随着对字节的读取而前移此文件指针。如果随机访问文件以读取/写入模式创建，则输出操作也可用；输出操作从文件指针开始写入字节，并随着对字节的写入而前移此文件指针。写入隐含数组的当前末尾之后的输出操作导致该数组扩展。该文件指针可以通过 getFilePointer 方法读取，并通过 seek 方法设置。

通常，如果此类中的所有读取例程在读取所需数量的字节之前已到达文件末尾，则抛出 EOFException（是一种 IOException）。如果由于某些原因无法读取任何字节，而不是在读取所需数量的字节之前已到达文件末尾，则抛出 IOException，而不是 EOFException。需要特别指出的是，如果流已被关闭，则可能抛出 IOException。 

## 7.2 构造方法

public RandomAccessFile(String name,String mode)

第一个参数是文件路径，第二个参数是操作文件的模式。模式有四种，我们最常用的一种叫"rw",这种方式表示我既可以写数据，也可以读取数据 

```java
package cn.itcast_05;

import java.io.IOException;
import java.io.RandomAccessFile;

/*
 * 随机访问流：
 * 		RandomAccessFile类不属于流，是Object类的子类。
 * 		但它融合了InputStream和OutputStream的功能。
 * 		支持对文件的随机访问读取和写入。
 * 
 * public RandomAccessFile(String name,String mode)：第一个参数是文件路径，第二个参数是操作文件的模式。
 * 		模式有四种，我们最常用的一种叫"rw",这种方式表示我既可以写数据，也可以读取数据 
 */
public class RandomAccessFileDemo {
	public static void main(String[] args) throws IOException {
		// write();
		read();
	}

	private static void read() throws IOException {
		// 创建随机访问流对象
		RandomAccessFile raf = new RandomAccessFile("raf.txt", "rw");

		int i = raf.readInt();
		System.out.println(i);
		// 该文件指针可以通过 getFilePointer方法读取，并通过 seek 方法设置。
		System.out.println("当前文件的指针位置是：" + raf.getFilePointer());

		char ch = raf.readChar();
		System.out.println(ch);
		System.out.println("当前文件的指针位置是：" + raf.getFilePointer());

		String s = raf.readUTF();
		System.out.println(s);
		System.out.println("当前文件的指针位置是：" + raf.getFilePointer());

		// 我不想重头开始了，我就要读取a，怎么办呢?
		raf.seek(4);
		ch = raf.readChar();
		System.out.println(ch);
	}

	private static void write() throws IOException {
		// 创建随机访问流对象
		RandomAccessFile raf = new RandomAccessFile("raf.txt", "rw");

		// 怎么玩呢?
		raf.writeInt(100);
		raf.writeChar('a');
		raf.writeUTF("中国");

		raf.close();
	}
}
```
运行结果：

![](http://img.blog.csdn.net/20150812214652815)

# 8. 合并流

## 8.1 SequenceInputStream概述

SequenceInputStream类可以将多个输入流串流在一起，合并为一个输入流，因此，该流也被称为合并流。

SequenceInputStream 表示其他输入流的逻辑串联。它从输入流的有序集合开始，并从第一个输入流开始读取，直到到达文件末尾，接着从第二个输入流读取，依次类推，直到到达包含的最后一个输入流的文件末尾为止。 

## 8.2 SequenceInputStream的构造方法

![](http://img.blog.csdn.net/20150915114300896)
![](http://img.blog.csdn.net/20150915114300896)

## 8.3 把多个文件的内容写入到一个文本文件

```java
package cn.itcast_06;

import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.SequenceInputStream;
import java.util.Enumeration;
import java.util.Vector;

/*
 * 以前的操作：
 * a.txt -- b.txt
 * c.txt -- d.txt
 * e.txt -- f.txt
 * 
 * 现在想要：
 * a.txt+b.txt+c.txt -- d.txt
 */
public class SequenceInputStreamDemo2 {
	public static void main(String[] args) throws IOException {
		// 需求：把下面的三个文件的内容复制到Copy.java中
		// ByteArrayStreamDemo.java,CopyFileDemo.java,DataStreamDemo.java

		// SequenceInputStream(Enumeration e)
		// 通过简单的回顾我们知道了Enumeration是Vector中的一个方法的返回值类型。
		// Enumeration<E> elements()
		Vector<InputStream> v = new Vector<InputStream>();
		InputStream s1 = new FileInputStream("ByteArrayStreamDemo.java");
		InputStream s2 = new FileInputStream("CopyFileDemo.java");
		InputStream s3 = new FileInputStream("DataStreamDemo.java");
		v.add(s1);
		v.add(s2);
		v.add(s3);
		Enumeration<InputStream> en = v.elements();
		SequenceInputStream sis = new SequenceInputStream(en);
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream("Copy.java"));

		// 如何写读写呢，其实很简单，你就按照以前怎么读写，现在还是怎么读写
		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = sis.read(bys)) != -1) {
			bos.write(bys, 0, len);
		}

		bos.close();
		sis.close();
	}
}
```

# 9. 序列化流

## 9.1 序列化流ObjectOutputStream

ObjectOutputStream 将 Java 对象的基本数据类型和图形写入 OutputStream。可以使用 ObjectInputStream 读取（重构）对象。通过在流中使用文件可以实现对象的持久存储。如果流是网络套接字流，则可以在另一台主机上或另一个进程中重构对象。 

只能将支持 java.io.Serializable 接口的对象写入流中。每个 serializable 
对象的类都被编码，编码内容包括类名和类签名、对象的字段值和数组值，以及从初始对象中引用的其他所有对象的闭包。 

writeObject 方法用于将对象写入流中。所有对象（包括 String 和数组）都可以通过 writeObject 写入。可将多个对象或基元写入流中。必须使用与写入对象时相同的类型和顺序从相应 ObjectInputstream 中读回对象。 

还可以使用 DataOutput 中的适当方法将基本数据类型写入流中。还可以使用 writeUTF 方法写入字符串。 

 对象的默认序列化机制写入的内容是：对象的类，类签名，以及非瞬态和非静态字段的值。其他对象的引用（瞬态和静态字段除外）也会导致写入那些对象。可使用引用共享机制对单个对象的多个引用进行编码，这样即可将对象的图形恢复为最初写入它们时的形状。 

构造方法：

ObjectOutputStream() ：为完全重新实现 ObjectOutputStream 的子类提供一种方法，让它不必分配仅由 ObjectOutputStream 的实现使用的私有数据。 

ObjectOutputStream(OutputStream out) ：创建写入指定 OutputStream 的 ObjectOutputStream。 

## 9.2 反序列化流ObjectInputStream

1、ObjectInputStream 对以前使用 ObjectOutputStream 写入的基本数据和对象进行反序列化。 

2、ObjectOutputStream 和 ObjectInputStream 分别与 FileOutputStream 和 FileInputStream 一起使用时，可以为应用程序提供对对象图形的持久存储。ObjectInputStream 用于恢复那些以前序列化的对象。其他用途包括使用套接字流在主机之间传递对象，或者用于编组和解组远程通信系统中的实参和形参。 

3、ObjectInputStream 确保从流创建的图形中所有对象的类型与 Java 虚拟机中显示的类相匹配。使用标准机制按需加载类。 

4、只有支持 java.io.Serializable 或 java.io.Externalizable 接口的对象才能从流读取。 

5、readObject 方法用于从流读取对象。应该使用 Java 的安全强制转换来获取所需的类型。在 Java 中，字符串和数组都是对象，所以在序列化期间将其视为对象。读取时，需要将其强制转换为期望的类型。 

6、可以使用 DataInput 上的适当方法从流读取基本数据类型。 

7、默认情况下，对象的反序列化机制会将每个字段的内容恢复为写入时它所具有的值和类型。反序列化进程将忽略声明为瞬态或静态的字段。对其他对象的引用使得根据需要从流中读取这些对象。使用引用共享机制能够正确地恢复对象的图形。反序列化时始终分配新对象，这样可以避免现有对象被重写。 

8、序列化操作问题：NotSerializableException:未序列化异常
9、为什么要实现序列化?如何实现序列化?

类通过实现 java.io.Serializable 接口以启用其序列化功能。未实现此接口的类将无法使其任何状态序列化或反序列化。
该接口居然没有任何方法，类似于这种没有方法的接口被称为标记接口

10、序列化数据后，再次修改类文件，读取数据会出问题，如何解决呢?

 每次修改java文件的内容的时候,class文件的id值都会发生改变。而读取文件的时候，会和class文件中的id值进行匹配。所以，就会出问题。让这个id值在java文件中是一个固定的值，这样，你修改文件的时候，这个id值就不会发生改变。

我们要知道的是：看到类实现了序列化接口的时候，要想解决黄色警告线问题，就可以自动产生一个序列化id值。而且产生这个值以后，我们对类进行任何改动，它读取以前的数据是没有问题的。
​      
11、我一个类中可能有很多的成员变量，有些我不想进行序列化。请问该怎么办呢?
      使用transient关键字声明不需要序列化的成员变量

```java
package cn.itcast_07;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

/*
 * 序列化流：把对象按照流一样的方式存入文本文件或者在网络中传输。对象 -- 流数据(ObjectOutputStream)
 * 反序列化流:把文本文件中的流对象数据或者网络中的流对象数据还原成对象。流数据 -- 对象(ObjectInputStream)
 */
public class ObjectStreamDemo {
	public static void main(String[] args) throws IOException,
			ClassNotFoundException {
		// 由于我们要对对象进行序列化，所以我们先自定义一个类
		// 序列化数据其实就是把对象写到文本文件
		// write();

		read();
	}

	private static void read() throws IOException, ClassNotFoundException {
		// 创建反序列化对象
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream(
				"oos.txt"));

		// 还原对象
		Object obj = ois.readObject();

		// 释放资源
		ois.close();

		// 输出对象
		System.out.println(obj);
	}

	private static void write() throws IOException {
		// 创建序列化流对象
		ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(
				"oos.txt"));

		// 创建对象
		Person p = new Person("林青霞", 27);

		// public final void writeObject(Object obj)
		oos.writeObject(p);

		// 释放资源
		oos.close();
	}
}
```

# 10. Properties集合

## 10.1 Properties概述

- Properties：属性集合类。是一个可以和IO流相结合使用的集合类。
- Properties 可保存在流中或从流中加载。属性列表中每个键及其对应值都是一个字符串。
- 是Hashtable的子类，说明是一个Map集合。

## 10.2 Properties的常用操作方法

### 10.2.1 构造方法
```java
Properties() // 创建一个无默认值的空属性列表。 
Properties(Properties defaults) // 创建一个带有指定默认值的空属性列表
```

### 10.2.2 Properties作为Map集合的使用

```java
package cn.itcast_08;

import java.util.Properties;
import java.util.Set;

/*
 * Properties:属性集合类。是一个可以和IO流相结合使用的集合类。
 * Properties 可保存在流中或从流中加载。属性列表中每个键及其对应值都是一个字符串。 
 * 
 * 是Hashtable的子类，说明是一个Map集合。
 */
public class PropertiesDemo {
	public static void main(String[] args) {
		// 作为Map集合的使用
		// 下面这种用法是错误的，一定要看API，如果没有<>，就说明该类不是一个泛型类,在使用的时候就不能加泛型
		// Properties<String, String> prop = new Properties<String, String>();

		Properties prop = new Properties();

		// 添加元素
		prop.put("it002", "hello");
		prop.put("it001", "world");
		prop.put("it003", "java");

		// System.out.println("prop:" + prop);

		// 遍历集合
		Set<Object> set = prop.keySet();
		for (Object key : set) {
			Object value = prop.get(key);
			System.out.println(key + "---" + value);
		}
	}
}
```

运行结果：

![](http://img.blog.csdn.net/20150812232958740)

## 10.3 Properties的特殊功能

```java
public Object setProperty(String key,String value) // 添加元素
public String getProperty(String key) // 获取元素
public Set<String> stringPropertyNames() // 获取所有的键的集合
```

```java
package cn.itcast_08;

import java.util.Properties;
import java.util.Set;

/*
 * 特殊功能：
 * public Object setProperty(String key,String value)：添加元素
 * public String getProperty(String key):获取元素
 * public Set<String> stringPropertyNames():获取所有的键的集合
 */
public class PropertiesDemo2 {
	public static void main(String[] args) {
		// 创建集合对象
		Properties prop = new Properties();

		// 添加元素
		prop.setProperty("张三", "30");
		prop.setProperty("李四", "40");
		prop.setProperty("王五", "50");

		// public Set<String> stringPropertyNames():获取所有的键的集合
		Set<String> set = prop.stringPropertyNames();
		for (String key : set) {
			String value = prop.getProperty(key);
			System.out.println(key + "---" + value);
		}
	}
}

/*
 * class Hashtalbe<K,V> { public V put(K key,V value) { ... } }
 * 
 * class Properties extends Hashtable { public V setProperty(String key,String
 * value) { return put(key,value); } }
 */
```

运行结果：

![](http://img.blog.csdn.net/20150812233438061)
## 10.4 Properties和IO流的结合使用

```java
public void load(Reader reader) // 把文件中的数据读取到集合中
public void store(Writer writer,String comments) // 把集合中的数据存储到文件
```

```java
package cn.itcast_08;

import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Reader;
import java.io.Writer;
import java.util.Properties;

/*
 * 这里的集合必须是Properties集合：
 * public void load(Reader reader):把文件中的数据读取到集合中
 * public void store(Writer writer,String comments):把集合中的数据存储到文件
 * 
 * 单机版游戏：
 * 		进度保存和加载。
 * 		三国群英传，三国志，仙剑奇侠传...
 * 
 * 		吕布=1
 * 		方天画戟=1
 */
public class PropertiesDemo3 {
	public static void main(String[] args) throws IOException {
		// myLoad();

		myStore();
	}

	private static void myStore() throws IOException {
		// 创建集合对象
		Properties prop = new Properties();

		prop.setProperty("林青霞", "27");
		prop.setProperty("武鑫", "30");
		prop.setProperty("刘晓曲", "18");
		
		//public void store(Writer writer,String comments):把集合中的数据存储到文件
		Writer w = new FileWriter("name.txt");
		prop.store(w, "helloworld");
		w.close();
	}

	private static void myLoad() throws IOException {
		Properties prop = new Properties();

		// public void load(Reader reader):把文件中的数据读取到集合中
		// 注意：这个文件的数据必须是键值对形式
		Reader r = new FileReader("prop.txt");
		prop.load(r);
		r.close();

		System.out.println("prop:" + prop);
	}
}
```

## 10.5 Properties练习

我有一个文本文件，我知道数据是键值对形式的，但是不知道内容是什么。请写一个程序判断是否有“lisi”这样的键存在，如果有就改变其实为”100”

```java
package cn.itcast_08;

import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Reader;
import java.io.Writer;
import java.util.Properties;
import java.util.Set;

/*
 * 我有一个文本文件(user.txt)，我知道数据是键值对形式的，但是不知道内容是什么。
 * 请写一个程序判断是否有“lisi”这样的键存在，如果有就改变其实为”100”
 * 
 * 分析：
 * 		A:把文件中的数据加载到集合中
 * 		B:遍历集合，获取得到每一个键
 * 		C:判断键是否有为"lisi"的，如果有就修改其值为"100"
 * 		D:把集合中的数据重新存储到文件中
 */
public class PropertiesTest {
	public static void main(String[] args) throws IOException {
		// 把文件中的数据加载到集合中
		Properties prop = new Properties();
		Reader r = new FileReader("user.txt");
		prop.load(r);
		r.close();

		// 遍历集合，获取得到每一个键
		Set<String> set = prop.stringPropertyNames();
		for (String key : set) {
			// 判断键是否有为"lisi"的，如果有就修改其值为"100"
			if ("lisi".equals(key)) {
				prop.setProperty(key, "100");
				break;
			}
		}

		// 把集合中的数据重新存储到文件中
		Writer w = new FileWriter("user.txt");
		prop.store(w, null);
		w.close();
	}
}
```

我有一个猜数字小游戏的程序，请写一个程序实现在测试类中只能用5次，超过5次提示：游戏试玩已结束，请付费。

```java
package cn.itcast_08;

import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Reader;
import java.io.Writer;
import java.util.Properties;

/*
 * 我有一个猜数字小游戏的程序，请写一个程序实现在测试类中只能用5次，超过5次提示：游戏试玩已结束，请付费。
 */
public class PropertiesTest2 {
	public static void main(String[] args) throws IOException {
		// 读取某个地方的数据，如果次数不大于5，可以继续玩。否则就提示"游戏试玩已结束，请付费。"
		// 创建一个文件
		// File file = new File("count.txt");
		// if (!file.exists()) {
		// file.createNewFile();
		// }

		// 把数据加载到集合中
		Properties prop = new Properties();
		Reader r = new FileReader("count.txt");
		prop.load(r);
		r.close();

		// 我自己的程序，我当然知道里面的键是谁
		String value = prop.getProperty("count");
		int number = Integer.parseInt(value);

		if (number > 5) {
			System.out.println("游戏试玩已结束，请付费。");
			System.exit(0);
		} else {
			number++;
			prop.setProperty("count", String.valueOf(number));
			Writer w = new FileWriter("count.txt");
			prop.store(w, null);
			w.close();

			GuessNumber.start();
		}
	}
}
```