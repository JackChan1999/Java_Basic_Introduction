在[重复读取InputStream的方法](http://zhangbo-peipei-163-com.iteye.com/blog/2022442)我们已经简单的知道可以通过缓存InputStream来重复利用一个InputStream，但是这种方式的缺点也是明显的，就是要缓存一整个InputStream内存压力可能是比较大的。如果第一次读取InputStream是用来判断文件流类型，文件编码等用的，往往不需要所有的InputStream的数据，或许只需要前n个字节，这样一来，缓存一整个InputStream实际上也是一种浪费。

其实InputStream本身提供了三个接口：

第一个，InputStream是否支持mark，默认不支持。

```java
public boolean markSupported() {  
   return false;  
}  
```

第二个，mark接口。该接口在InputStream中默认实现不做任何事情。
```java
public synchronized void mark(int readlimit) {}  
```

第三个，reset接口。该接口在InputStream中实现，调用就会抛异常。
```java
public synchronized void reset() throws IOException {  
   throw new IOException("mark/reset not supported");  
}  
```

从三个接口定义中可以看出，首先InputStream默认是不支持mark的，子类需要支持mark必须重写这三个方法。
第一个接口很简单，就是标明该InputStream是否支持mark。

mark接口的官方文档解释：

“在此输入流中标记当前的位置。对 reset 方法的后续调用会在最后标记的位置重新定位此流，以便后续读取重新读取相同的字节。
readlimit 参数告知此输入流在标记位置失效之前允许读取许多字节。

mark 的常规协定是：如果方法 markSupported 返回 true，则输入流总会在调用 mark 之后记住所有读取的字节，并且无论何时调用方法 reset ，都会准备再次提供那些相同的字节。但是，如果在调用 reset 之前可以从流中读取多于 readlimit 的字节，则根本不需要该流记住任何数据。”

reset接口的官方文档解释：

将此流重新定位到对此输入流最后调用 mark 方法时的位置。
reset 的常规协定是：

如果方法 markSupported 返回 true，则：
如果创建流以来未调用方法 mark，或最后调用 mark 以来从该流读取的字节数大于最后调用 mark 时的参数，则可能抛出 IOException。
如果未抛出这样的 IOException，则将该流重新设置为这种状态：最近调用 mark 以来（或如果未调用 mark，则从文件开始以来）读取的所有字节将重新提供给 read 方法的后续调用方，后接可能是调用 reset 时的下一输入数据的所有字节。
如果方法 markSupported 返回 false，则：
对 reset 的调用可能抛出 IOException。
如果未抛出 IOException，则将该流重新设置为一种固定状态，该状态取决于输入流的特定类型和其创建方式的固定状态。提供给 read 方法的后续调用方的字节取决于特定类型的输入流。

简而言之就是：
调用mark方法会记下当前调用mark方法的时刻，InputStream被读到的位置。
调用reset方法就会回到该位置。
举个简单的例子：
```java
String content = "BoyceZhang!";  
InputStream inputStream = new ByteArrayInputStream(content.getBytes());  

// 判断该输入流是否支持mark操作  
if (!inputStream.markSupported()) {  
    System.out.println("mark/reset not supported!");  
}  
int ch;    
boolean marked = false;    
while ((ch = inputStream.read()) != -1) {  

    //读取一个字符输出一个字符    
    System.out.print((char)ch);    
    //读到 'e'的时候标记一下  
     if (((char)ch == 'e')& !marked) {    
        inputStream.mark(content.length());  //先不要理会mark的参数  
         marked = true;    
     }    

     //读到'!'的时候重新回到标记位置开始读  
      if ((char)ch == '!' && marked) {    
          inputStream.reset();    
          marked = false;  
      }    
}  

//程序最终输出：BoyceZhang!Zhang!  
```

看了这个例子之后对mark和reset接口有了很直观的认识。
但是mark接口的参数readlimit究竟是干嘛的呢？
我们知道InputStream是不支持mark的。要想支持mark子类必须重写这三个方法，我想说的是不同的实现子类，mark的参数readlimit作用不尽相同。
常用的FileInputStream不支持mark。

1. 对于BufferedInputStream，readlimit表示：InputStream调用mark方法的时刻起，在读取readlimit个字节之前，标记的该位置是有效的。如果读取的字节数大于readlimit，可能标记的位置会失效。

在BufferedInputStream的read方法源码中有这么一段：
```java
} else if (buffer.length >= marklimit) {  
     markpos = -1;   /* buffer got too big, invalidate mark */  
     pos = 0;        /* drop buffer contents */  
     } else {            /* grow buffer */  
```

## 为什么是可能会失效呢？
因为BufferedInputStream读取不是一个字节一个字节读取的，是一个字节数组一个字节数组读取的。
例如，readlimit=35，第一次比较的时候buffer.length=0（没开始读）<readlimit
然后buffer数组一次读取48个字节。这时的read方法只会简单的挨个返回buffer数组中的字节，不会做这次比较。直到读到buffer数组最后一个字节（第48个）后，才重新再次比较。这时如果我们读到buffer中第47个字节就reset。mark仍然是有效的。虽然47>35。

2. 对于InputStream的另外一个实现类：ByteArrayInputStream，我们发现readlimit参数根本就没有用，调用mark方法的时候写多少都无所谓。
```java
public void mark(int readAheadLimit) {  
   mark = pos;  
}  

public synchronized void reset() {  
   pos = mark;  
}  
```

因为对于ByteArrayInputStream来说，都是通过字节数组创建的，内部本身就保存了整个字节数组，mark只是标记一下数组下标位置，根本不用担心mark会创建太大的buffer字节数组缓存。

3. 其他的InputStream子类没有去总结。原理都是一样的。

所以由于mark和reset方法配合可以记录并回到我们标记的流的位置重新读流，很大一部分就可以解决我们的某些重复读的需要。
这种方式的优点很明显：不用缓存整个InputStream数据。对于ByteArrayInputStream甚至没有任何的内存开销。
当然这种方式也有缺点：就是需要通过干扰InputStream的读取细节，也相对比较复杂。

## 关于inputStream.available()方法获取下载文件的总大小

如果用inputStream对象的available()方法获取流中可读取的数据大小，通常我们调用这个函数是在下载文件或者对文件进行其他处理时获取文件的总大小。

以前在我们初学File和inputStream和outputStream时，有需要将文件从一个文件夹复制到另一个文件夹中，这时候我们用的就是inputStream.available()来获取文件的总大小，而且屡试不爽。

但是当我们要从网络URL中下载一个文件时，我们发现得到的数值并不是需要下载的文件的总大小。好吧。我们看看JDK文档中怎么解释。

> 返回此输入流下一个方法调用可以不受阻塞地从此输入流读取（或跳过）的估计字节数。下一个调用可能是同一个线程，也可能是另一个线程。一次读取或跳过此估计数个字节不会受阻塞，但读取或跳过的字节数可能小于该数。

注意，有些 InputStream 的实现将返回流中的字节总数，但也有很多实现不会这样做。试图使用此方法的返回值分配缓冲区，以保存此流所有数据的做法是不正确的。

如果已经调用 close() 方法关闭了此输入流，那么此方法的子类实现可以选择抛出 IOException。
类 InputStream 的 available 方法总是返回 0。
此方法应该由子类重写。

返回：
可以不受阻塞地从此输入流读取（或跳过）的估计字节数；如果到达输入流末尾，则返回 0。
抛出：IOException - 如果发生 I/O 错误。

inputStream 源代码
```java
/**
    * Returns the number of bytes that are available before this stream will
    * block. This implementation always returns 0. Subclasses should override
    * and indicate the correct number of bytes available.
    *  
    * @return the number of bytes available before blocking.
    * @throws IOException
    *             if an error occurs in this stream.
    * @since Android 1.0
    */  
public int available() throws IOException {  
       return 0;  
}
```

这里返回的是 0 值。
所以说要从网络中下载文件时，我们知道网络是不稳定的，也就是说网络下载时，read()方法是阻塞的，说明这时我们用
inputStream.available()获取不到文件的总大小。

但是从本地拷贝文件时，我们用的是FileInputStream.available()，难道它是将先将硬盘中的数据先全部读入流中？
然后才根据此方法得到文件的总大小？
好吧，我们来看看FileInputStream源代码吧

```java
/**
    * Returns the number of bytes that are available before this stream will
    * block. This method always returns the size of the file minus the current
    * position.
    *  
    * @return the number of bytes available before blocking.
    * @throws IOException
    *             if an error occurs in this stream.
    * @since Android 1.0
    */  
   @Override  
   public int available() throws IOException {  
       openCheck();  

       // BEGIN android-added  

       // Android always uses the ioctl() method of determining bytes  
       // available. See the long discussion in  
       // org_apache_harmony_luni_platform_OSFileSystem.cpp about its  
       // use.  

       <span style="color: #ff0000;">return fileSystem.ioctlAvailable(fd.descriptor);</span>  
       // END android-added   

       // BEGIN android-deleted  
       // synchronized (repositioningLock) {  
       //     // stdin requires special handling  
       //     if (fd == FileDescriptor.in) {  
       //         return (int) fileSystem.ttyAvailable();  
       //     }  
       //  
       //     long currentPosition = fileSystem.seek(fd.descriptor, 0L,  
       //             IFileSystem.SEEK_CUR);  
       //     long endOfFilePosition = fileSystem.seek(fd.descriptor, 0L,  
       //             IFileSystem.SEEK_END);  
       //     fileSystem.seek(fd.descriptor, currentPosition,  
       //             IFileSystem.SEEK_SET);  
       //     return (int) (endOfFilePosition - currentPosition);  
       // }  
       // END android-deleted  
   }
```

这里重写了inputStream中的available()方法

关键是：fileSystem.ioctlAvailable(fd.descriptor);
调用了FileSystem这是java没有公开的一个类，JavaDoc API没有。
其中fileSystem 是一个IFileSystem对象，IFileSySTEM是java没有公开的一个类，JavaDoc API中没有；
fd是一个FileDescriptor对象，即文件描述符
说明这句代码应该是通过文件描述符获取文件的总大小，而并不是事先将磁盘上的文件数据全部读入流中，再获取文件总大小

搞清楚了这些，但是我们的主要问题没有解决，怎么获得网络文件的总大小？
我想原理应该都差不多，应该也是通过一个类似文件描述符的东西来获取。

网络下载获取文件总大小的代码：

```java
HttpURLConnection httpconn = (HttpURLConnection)url.openConnection();  
httpconn.getContentLength();
```
我们再来看看httpconn.getContentLength();

```java
/**
 * Gets the content length in bytes specified by the response header field
 * {@code content-length} or {@code -1} if this field is not set.
 *  
 * @return the value of the response header field {@code content-length}.
 * @since Android 1.0
 */  
public int getContentLength() {  
    return getHeaderFieldInt("Content-Length", -1); //$NON-NLS-1$  
}  
```
关键：getHeaderFieldInt("Content-Length", -1);
意思是从http预解析头中获取“Content-length”字段的值，其实也是类似从文件描述符中获取文件的总大小

## 重复读取InputStream的方法

在这篇博客中我们已经知道了Java的InputStream是不能重复被读取的。
但是在有的场合中，我们需要重复利用InputStream的数据。
比如：
1. 一个office word文件流，我需要首先读取InputStream中的前一些字节来判断word文件的实际内容（word文件可以保存html，mht的内容）。然后再根据实际内容决定我要解析InputStream的方式。

2. 一个Html文件流，我需要首先读取InputStream中的一些字节来判断Html文件编码方式。然后再根据html文件编码方式读取Html内容。

3. 从socket收到的一个InputStream，我首先需要读取InputStream判断是什么类型的字符串。然后再将InputStream读取写到文件里。

总之，在实际的工作当中，我们常常会需要多次读取一个InputStream的需求。
如果该InputStream是我们通过某种方式“主动”获取的，那我们可以不必重复读取一个InputStream，而是再次获取一样数据的InputStream来处理。
例如：

```java
InputStream inputStream = new FileInputStream(path);  
//利用inputStream  
inputStream = new FileInputStream(path);  
//再次利用inputStream  
```
再例如：
```java
InputStream inputStream = httpconn.getInputStream();   
//利用inputStream  
inputStream = httpconn.getInputStream();  
//再次利用inputStream  
```
但是，对于“被动”利用InputStream的接口，在接口内部需要重复利用InputStream，对于InputStream的来源，可能是文件，可能是网络，也可能是内存里的一个String，对于InputStream的方式接口内部不得而知，因此更谈不上在接口内部重复获取了。
例如有这样一个接口：
```java
//将InputStream转换成一个文本字符串  
public String convert(InputStream inputStream);  
```
在接口内部我们可能需要首先读取InputStream前n个字节来判断InputStream流的数据流型，然后转化InputStream为一个字符串。

最简单的方式就是缓存，首先将InputStream缓存到内存，然后重复使用内存里的数据。
例如：
```java
package com.gs.cvoud.attachment.converter;  

import java.io.ByteArrayInputStream;  
import java.io.ByteArrayOutputStream;  
import java.io.IOException;  
import java.io.InputStream;  

import com.gs.cvoud.util.ObjectUtils;  

/**
 * 缓存InputStream，以便InputStream的重复利用
 * @author boyce
 * @version 2014-2-24
 */  
public class InputStreamCacher {  

    private static final org.slf4j.Logger logger = org.slf4j.LoggerFactory.getLogger(InputStreamCacher.class);  

    /**
     * 将InputStream中的字节保存到ByteArrayOutputStream中。
     */  
    private ByteArrayOutputStream byteArrayOutputStream = null;  

    public InputStreamCacher(InputStream inputStream) {  
        if (ObjectUtils.isNull(inputStream))  
            return;  

        byteArrayOutputStream = new ByteArrayOutputStream();  
        byte[] buffer = new byte[1024];    
        int len;    
        try {  
            while ((len = inputStream.read(buffer)) > -1 ) {    
                byteArrayOutputStream.write(buffer, 0, len);    
            }  
            byteArrayOutputStream.flush();  
        } catch (IOException e) {  
            logger.error(e.getMessage(), e);  
        }    
    }  

    public InputStream getInputStream() {  
        if (ObjectUtils.isNull(byteArrayOutputStream))  
            return null;  

        return new ByteArrayInputStream(byteArrayOutputStream.toByteArray());  
    }  
}  
```
接口内部使用情景：
```java
InputStreamCacher  cacher = new InputStreamCacher(inputStream);  
InputStream stream = cacher.getInputStream();  
//读取stream  
stream = cacher.getInputStream();  
```
上述的方式是将InputStream缓存到一个ByteArrayOutputStream中，当然缓存的数据类型和方式都是任意的，这只是一种解决思路。

这种方式有一个最大的缺点，就是内存压力。

外部传给接口的InputStream有可能很大。每调用一次接口就将InputStream缓存到内存中，内存要承受的压力是可想而知的。

编程永远都是在时间和空间之间找到一个平衡点，前面说的“主动获取方式”的重复获取也有它的缺点，就是需要重新读取文件，获取重新建立网络连接等，这就是需要消耗更多的时间。

万事万物都是这样，天下没有完美的事，有舍才有得，选择什么就意味着放弃什么，开了一扇窗可能就要关一扇门。所以不管是生活还是编程，我们都需要在舍与得，选择的与放弃的，窗和门之间做出相对合理的抉择，或者说不得不做的抉择。

闲话扯远了，其实重复利用InputStream还有另一种方式： 通过mark和reset方法重复利用InputStream
