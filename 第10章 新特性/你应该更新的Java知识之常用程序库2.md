## Hamcrest

一句话，如果你写单元测试，就应该用[Hamcrest](http://hamcrest.org/)。

如今不写单元测试，你都不好意思说自己在做工程项目了。但你一般这么写断言呢？如果你还写成下面这样，我只能说你落伍了：

```
assertEquals(a, b);
```

请告诉我，哪个是执行结果，哪个是预期结果，不管你是怎样，反正大多数情况下，我是记不住的。所以，这个在只重功能不重可读性年代产生的API该更新了。于是，Hamcrest就是为了解决这样的问题而生的。
```
assertThat(a, is(b));
```
很明显，前面一个是执行结果，后面一个是预期结果，当然这只是一个简单的例子。由于Hamcrest引入了matcher的概念（就是你看到的is部分），我们可以进行更多的组合：

```java
assertThat(number, greaterThan(5));
assertThat(text, startsWith("Hello"));
assertThat(array, hasItem("World"));
```

Hamcrest如此好用，以至于JUnit已经将其吸纳进去。如果你现在用的JUnit是4.4之后的版本，那你已经有了Hamcrest。无需额外的配置，就可以拿过来用。

## Mockito

写单元测试不用Mock框架几乎是一件不可能的事，我是说Mock框架，不是Mock模式哦！对于老Java程序员来说，提起Mock框架，率先在脑海中撞线的多半是[JMock](http://jmock.org/)或[EasyMock](http://www.easymock.org/)。

使用[Mockito](http://code.google.com/p/mockito/)，只要有一个理由就够了，简单。相比于JMock，它不用写checking，相比于EasyMock，它省去了replay。下面是个例子：

```java
List mockedList = mock(List.class);
when(mockedList.get(0)).thenReturn("first");
System.out.println(mockedList.get(0));
```

当然，[Mockito还是非常强大的](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html)。

最后再强调一遍，无论使用哪个框架，请尽量不要使用verify，也就是传说中的Mock模式，那是把代码拉入泥潭的开始。

## SLF4J和Logback

日志几乎是稍微有点规模的项目躲不开的一个东西，如果你是个老Java程序员，你必然知道[Log4J](http://logging.apache.org/log4j/)，多半也知道[Commons Logging](http://commons.apache.org/logging/)。是时候把它们扔掉了，因为有[SLF4J](http://www.slf4j.org/)和[Logback](http://logback.qos.ch/)了。SLF4J要替代Commons Logging，而Logback的目标是Log4J。

程序员里愤青多，SLF4J和Logback的作者就是一个，他叫[Ceki Gülcü](http://ceki.blogspot.com/)，事实上，他也是Log4J的作者。Log4J的开发状态实在让他太不爽了，于是，他另起炉灶，打造出新的替代品。

只凭一点就足以让我们对SLF4J义无反顾了，你还记得用Commons Logging写出这样的代码吗？

```java
if (logger.debugEnable()) {

  logger.debug("Hello, ", name);

}
```

而SLF4J的写法只有一句话：

```java
logger.debug("Hello, {}", name);
```

从根源来说，这是时代造成的，Commons Logging是Java 5之前产生的，那时候还没有变参，所以，我们不得不说，它老了。

至于Logback，性能是最重要的噱头，当然，还有[一些其它的理由](http://logback.qos.ch/reasonsToSwitch.html)。理由里有一点并未提及，但对于开发人员很贴心的改进，就是日志模式的改进，还记得Log4J那密码一样的日志模式吗？

```
%d{dd MMM yyyy HH:mm:ss} [%t] %-5p %m%n
```

下面是Logback的版本，不用查文档，我也看出每段表示的都是什么：

```
%d{dd MMM yyyy HH:mm:ss} [%thread] %-5level %msg%n
```

这里介绍的几个程序库都是很通用的，无论是你做怎样的开发，应该都或多或少给你一些帮助。时间未曾停步，Java开发也未曾停留，如果你是个老Java程序员，是时候更新一下自己的知识了。