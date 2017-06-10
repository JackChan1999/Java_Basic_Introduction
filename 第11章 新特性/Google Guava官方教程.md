# 1. 引言

Guava工程包含了若干被Google的 Java项目广泛依赖 的核心库，例如：集合 [collections] 、缓存 [caching] 、原生类型支持 [primitives support] 、并发库 [concurrency libraries] 、通用注解 [common annotations] 、字符串处理 [string processing] 、I/O 等等。 所有这些工具每天都在被Google的工程师应用在产品服务中。

查阅Javadoc并不一定是学习这些库最有效的方式。在此，我们希望通过此文档为Guava中最流行和最强大的功能，提供更具可读性和解释性的说明。


# 2. 译文格式说明

Guava中的类被首次引用时，都会链接到Guava的API文档。如：Optional<T>。
Guava和JDK中的方法被引用时，一般都会链接到Guava或JDK的API文档，一些人所共知的JDK方法除外。如：Optional.of(T), Map.get(key)。
译者对文档的额外说明以斜体显示，并且以“译者注：”开始。

# 3. 目录

1、 基本工具 [Basic utilities]让使用Java语言变得更舒适

1.1 使用和避免null：null是模棱两可的，会引起令人困惑的错误，有些时候它让人很不舒服。很多Guava工具类用快速失败拒绝null值，而不是盲目地接受

1.2 前置条件: 让方法中的条件检查更简单

1.3 常见Object方法: 简化Object方法实现，如hashCode()和toString()

1.4 排序: Guava强大的”流畅风格比较器”

1.5 Throwables：简化了异常和错误的传播与检查

2、集合[Collections] Guava对JDK集合的扩展，这是Guava最成熟和为人所知的部分

2.1 不可变集合: 用不变的集合进行防御性编程和性能提升。

2.2 新集合类型: multisets, multimaps, tables, bidirectional maps等

2.3 强大的集合工具类: 提供java.util.Collections中没有的集合工具

2.4 扩展工具类：让实现和扩展集合类变得更容易，比如创建Collection的装饰器，或实现迭代器

3、缓存[Caches] Guava Cache：本地缓存实现，支持多种缓存过期策略

4、函数式风格[Functional idioms] Guava的函数式支持可以显著简化代码，但请谨慎使用它

5、并发[Concurrency] 强大而简单的抽象，让编写正确的并发代码更简单

5.1 ListenableFuture：完成后触发回调的Future

5.2 Service框架：抽象可开启和关闭的服务，帮助你维护服务的状态逻辑

6、字符串处理[Strings] 非常有用的字符串工具，包括分割、连接、填充等操作

7、原生类型[Primitives] 扩展 JDK 未提供的原生类型（如int、char）操作， 包括某些类型的无符号形式

8、区间[Ranges] 可比较类型的区间API，包括连续和离散类型

9、I/O 简化I/O尤其是I/O流和文件的操作，针对Java5和6版本

10、散列[Hash]
提供比Object.hashCode()更复杂的散列实现，并提供布鲁姆过滤器的实现

11、事件总线[EventBus]
发布-订阅模式的组件通信，但组件不需要显式地注册到其他组件中

12、数学运算[Math]
优化的、充分测试的数学工具类

13、反射[Reflection]
Guava 的 Java 反射机制工具类

[GitHub](https://github.com/google/guava) [在线文档](http://tool.oschina.net/apidocs/apidoc?api=guava)

Guava 是一个 Google 的基于java1.6的类库集合的扩展项目，包括 collections, caching, primitives support, concurrency libraries, common annotations, string processing, I/O, 等等. 这些高质量的 API 可以使你的JAVa代码更加优雅，更加简洁，让你工作更加轻松愉悦。下面我们就开启优雅Java编程学习之旅！

## 3.1 项目相关信息

- 官方首页：http://code.google.com/p/guava-libraries
- 官方下载：http://code.google.com/p/guava-libraries/downloads/list
- 官方文档：http://docs.guava-libraries.googlecode.com/git/javadoc
- http://www.ostools.net/apidocs/apidoc?api=guava

## 3.2 源码包的简单说明：

- com.google.common.annotations：普通注解类型。 
- com.google.common.base：基本工具类库和接口。 
- com.google.common.cache：缓存工具包，非常简单易用且功能强大的JVM内缓存。 
- com.google.common.collect：带泛型的集合接口扩展和实现，以及工具类，这里你会发现很多好玩的集合。 
- com.google.common.eventbus：发布订阅风格的事件总线。 
- com.google.common.hash： 哈希工具包。 
- com.google.common.io：I/O工具包。 
- com.google.common.math：原始算术类型和超大数的运算工具包。 
- com.google.common.net：网络工具包。 
- com.google.common.primitives：八种原始类型和无符号类型的静态工具包。 
- com.google.common.reflect：反射工具包。 
- com.google.common.util.concurrent：多线程工具包。


# 4. 类库使用手册

1、基本工具类：让使用Java语言更令人愉悦。

- 使用和避免 null：null 有语言歧义，会产生令人费解的错误，反正他总是让人不爽。很多 Guava的工具类在遇到null时会直接拒绝或出错，而不是默默地接受他们。
- 前提条件：更容易的对你的方法进行前提条件的测试。
- 常见的对象方法： 简化了Object常用方法的实现， 如 hashCode() 和 toString()。
- 排序： Guava 强大的 "fluent Comparator"比较器， 提供多关键字排序。
- Throwable类： 简化了异常检查和错误传播。

2、集合类：集合类库是 Guava 对 JDK 集合类的扩展， 这是 Guava 项目最完善和为人所知的部分。

- Immutable collections（不变的集合）： 防御性编程， 不可修改的集合，并且提高了效率。
- New collection types(新集合类型)：JDK collections 没有的一些集合类型，主要有：multisets，multimaps，tables， bidirectional maps等等
- Powerful collection utilities（强大的集合工具类）： java.util.Collections 中未包含的常用操作工具类
- Extension utilities（扩展工具类）: 给 Collection 对象添加一个装饰器? 实现迭代器? 我们可以更容易使用这些方法。

3、缓存: 本地缓存，可以很方便的操作缓存对象，并且支持各种缓存失效行为模式。

4、Functional idioms（函数式）: 简洁, Guava实现了Java的函数式编程，可以显著简化代码。

5、Concurrency（并发）：强大,简单的抽象,让我们更容易实现简单正确的并发性代码。

- ListenableFuture（可监听的Future）: Futures,用于异步完成的回调。
- Service: 控制事件的启动和关闭，为你管理复杂的状态逻辑。

6、Strings: 一个非常非常有用的字符串工具类: 提供 splitting，joining， padding 等操作。

7、Primitives: 扩展 JDK 中未提供的对原生类型（如int、char等）的操作， 包括某些类型的无符号的变量。

8、Ranges: Guava 一个强大的 API，提供 Comparable 类型的范围处理， 包括连续和离散的情况。

9、I/O: 简化 I/O 操作, 特别是对 I/O 流和文件的操作, for Java 5 and 6.

10、Hashing: 提供比 Object.hashCode() 更复杂的 hash 方法, 提供 Bloom filters.

11、EventBus: 基于发布-订阅模式的组件通信，但是不需要明确地注册在委托对象中。

12、Math: 优化的 math 工具类，经过完整测试。

13、Reflection: Guava 的 Java 反射机制工具类。

> 原文链接：http://ifeve.com/google-guava/