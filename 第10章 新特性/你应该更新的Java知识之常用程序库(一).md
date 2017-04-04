在很多人眼中，Java已经是一门垂垂老矣的语言，但并不妨碍Java世界依然在前进。如果你曾离开Java，云游于其它世界，或是每日只在遗留代码中挣扎，或许是时候抬起头，看看老Java中的新东西。

**Guava**

一句话，只要你做Java项目，就应该用[Guava](https://code.google.com/p/guava-libraries/)。

guava是Google出品的一套Java核心库，在我看来，它甚至应该是JDK的一部分。作为一个Java程序员，如果你没抱怨过JDK的设计，只能说明一点，你写得程序还是太少。正是JDK设计不彰，才有了一些项目来补充JDK的不足。如果说老Java程序员应该听说过[Apache Commons Lang](http://commons.apache.org/lang/)，那新Java程序员该知道的就是Guava了。

老Java程序员更多的是知道Google Collections，不妨到[它的主页](http://code.google.com/p/google-collections/)上走一遭，你会看到这个库已经改名为Guava。事实上，Guava并不直接等于Google Collections，Guava是一个超集。Guava实在太强大了，要想展现它的强大，需要专门的介绍，这里就不展开了。

下面以一个统计单词出现个数的小程序作为这个段落的结尾，虽然无法与许多其它语言的实现相提并论，但作为一个Java程序员，你不妨想一下按照传统方式，这段代码应该是什么样子。

```java
String content = Files.toString(new File(args[0]), Charset.defaultCharset());
Iterable texts = Splitter.on(CharMatcher.WHITESPACE)
        .omitEmptyStrings()
        .trimResults()
        .split(content);
Multiset collection = HashMultiset.create(texts);
```

**Joda Time**

你觉得一个API设计得差到什么份上，才会把自己差不多的API全部Deprecated掉。java.util.Date便是这样的奇葩。因为它的API几乎都是反直觉的，几乎所有敢于用它的Java程序员都吃过它的亏。想初始化个2013年的第一天，还真不那么容易：

  Date firstDayOf2013 = new Date(113, 0, 1);

如果你是个Java新手，你能猜出113是从哪来的吗？（好吧，它是2013-1900，至于为什么是1900，这真得问API的设计者了）。

[Joda Time](http://joda-time.sourceforge.net/)就是人们实在无法忍受这样东西的产物。同样的代码用Joda Time实现：

`DateTime firstDayOf2013 = new DateTime().withDate(2013, 1, 1);`

无论如何，你知道这能看出这些参数的含义了。不只如此，你还可以计算两天后是什么日子：

`firstDate.plusDays(2);`

日期格式化，也是JDK Date系列API里一大特色，你必须把代码写成下面这样：

`new SimpleDateFormat("yyyy.MM.dd").format(firstDayOf2013)`

作为一个初始化很慢的构造函数，你还必须每次调用，因为它不是线程安全的。同样的代码，在Joda Time里，我们可以用

```java
DateTimeFormatter：
DateTimeFormatter formatter = DateTimeFormat.forPattern("yyyy.MM.dd");
...
formatter.print(dateTime);
```

请尽管放心大胆把formatter声明成一个字段，因为它是线程安全的。

当然，Joda Time的强大远不止于此。当然，JDK也并不是那么完全的自暴自弃，于是，有了一个JSR 310专门设计新的Date API。JSR 310的spec lead是[Steven Colebourne](http://www.blogger.com/profile/01454237967846880639)，此人正是Joda Time的作者。不过，虽然JSR 310为我们描绘了Date的全新景象，但Java 8出来之前就先别打它的主意了，乖乖地用Joda Time吧。

> 原文链接：http://www.blogbus.com/dreamhead-logs/226738702.html