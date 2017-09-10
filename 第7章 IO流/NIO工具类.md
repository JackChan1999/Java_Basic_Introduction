## 字符集和CharSet

### CharSet

| 方法说明                | 功能描述           |
| ------------------- | -------------- |
| availableCharsets() | 获取当前JDK所支持的字符集 |
| forName()           | 创建CharSet对象    |
| newDecoder()        | 创建解码器          |
| newEncoder()        | 创建编码器          |
| decode()            | 解码             |
| encode()            | 编码             |

### CharsetDecoder

| 方法说明     | 功能描述 |
| -------- | ---- |
| decode() | 解码   |

### CharsetEncoder

| 方法说明     | 功能描述 |
| -------- | ---- |
| encode() | 编码   |

## 文件锁

### FileLock

| 方法说明       | 功能描述        |
| ---------- | ----------- |
| release()  | 释放锁         |
| isShared() | 判断文件锁是否是共享锁 |

## IO流工具类

### Path

| 方法说明             | 功能描述               |
| ---------------- | ------------------ |
| getNameCount()   | 获取path路径所包含的路径名的数量 |
| getRoot()        | 获取path路径的根路径       |
| toAbsolutePath() | 获取path对应的绝对路径      |
| getFileName()    | 获取path的文件名         |
| getParent()      | 获取path的父路径         |
| startsWith()     |                    |
| subpath()        |                    |
| toFile()         | 获取path路径的File对象    |
| toUri()          | 返回代表当前路径的uri       |
| register()       | 监控文件变化             |

### Paths

| 方法说明                              | 功能描述          |
| --------------------------------- | ------------- |
| get(String first, String... more) | 获取Path对象      |
| get(Uri uri)                      | 根据Uri获取Path对象 |

### Files

| 方法说明           | 功能描述             |
| -------------- | ---------------- |
| copy()         | 复制文件             |
| isHidden()     | 判断文件是否隐藏         |
| size()         | 获取文件大小           |
| write()        | 将数据写入给定的文件中      |
| readAllLines() | 一次性读取文件的所有行      |
| list()         | 列出当前目录下的所有文件和子目录 |
| lines()        | 读取文件内容           |
| getFileStore() | 获取FileStore对象    |
| walkFileTree() | 遍历文件和目录          |

### FileStore

| 方法说明             | 功能描述   |
| ---------------- | ------ |
| getTotalSpace()  | 获取总大小  |
| getUsableSpace() | 获取可用大小 |
| isReadOnly()     | 是否只读   |

### FileVisitor 文件访问器

| 方法说明                 | 功能描述             |
| -------------------- | ---------------- |
| postVisitDirectory() | 访问子目录之后触发该方法     |
| preVisitDirectory()  | 访问子目录之前触发该方法     |
| visitFile()          | 访问File文件时触发该方法   |
| visitFileFailed()    | 访问File文件失败时触发该方法 |

SampleFileVisitor

## WatchService

监控文件变化

| 方法说明                              | 功能描述                        |
| --------------------------------- | --------------------------- |
| poll()                            | 获取下一个WatchKey               |
| poll(long timeout, TimeUnit unit) | 等待 timeout 时间去获取下一个WatchKey |
| take()                            | 获取下一个WatchKey               |

```java
WatchService watchService = FileSystems.getDefault().newWatchService();
```

### WatchKey 

| 方法说明         | 功能描述 |
| ------------ | ---- |
| pollEvents() |      |
| reset()      | 重置   |

### WatchEvent

文件变化事件

| 方法说明      | 功能描述 |
| --------- | ---- |
| context() |      |
| kind()    | 事件类型 |
| count()   |      |

### FileSystems