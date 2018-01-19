## 集合框架

set集合

map

元素是Entry< k, v >，Entry就是将key和value捆绑在一起

当一个set的元素是Entry的时候，该set就是一个map

hash()算法可以快速的计算出存、取的地址，存和取元素的效率很高，但是hash算法不能表示出元素之间的关系。hash算法决定了集合是无序的。

数组+链表，bucket桶结构，hash冲突

HashSet底层实现是HashMap

TreeMap

红黑树，平衡二叉树，排序二叉树（二叉查找树，左小右大，中序有序），有序，自然排序compareTo()方法，比较器排序Comparator接口

Entry< key, value >

## 断言assert

## IO流

- 字节流
- 字符流
- 转换流
- 高效流
- 随机访问流
- 序列化流
- NIO jdk 4
  - Channel 通道，面向块的处理
  - Buffer 缓冲，capacity，limit，position
  - Files
  - Paths
  - FileVisitor 遍历文件和目录

### Buffer 缓冲区

| 方法声明       | 功能描述                                     |
| ---------- | ---------------------------------------- |
| allocate() | 分配内存空间，创建Buffer对象                        |
| put()      | 存数据                                      |
| get()      | 取数据                                      |
| position() | 位置                                       |
| limit()    | 界限                                       |
| capacity() | 容量                                       |
| flip()     | 为输出数据做好准备，limit = position；position = 0； |
| clear()    | 为装入数据做准备，limit = capacity；position = 0；  |

### Channel 通道

| 方法声明    | 功能描述                           |
| ------- | ------------------------------ |
| map()   | 将buffer对应的部分或全部数据映射成ByteBuffer |
| write() | 向buffer写入数据                    |
| read()  | 从buffer读取数据                    |

- AIO Jdk 7
- 字符集和Charset
  - 编解码
- 文件锁FileLock

### AIO

Path

| 方法声明           | 功能描述 |
| -------------- | ---- |
| getNameCount() |      |
| getRoot()      |      |
|                |      |

Paths

| 方法声明  | 功能描述 |
| ----- | ---- |
| get() |      |
|       |      |
|       |      |

Files

| 方法声明       | 功能描述 |
| ---------- | ---- |
| copy()     |      |
| isHidden() |      |
| size()     |      |
| write()    |      |
| list()     |      |
| lines()    |      |



## 网络编程
