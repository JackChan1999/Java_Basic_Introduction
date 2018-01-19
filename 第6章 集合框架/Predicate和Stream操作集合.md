## Predicate 操作集合

Predicate 谓词对象，函数式接口，可以使用Lambda表达式作为参数。

- test()

Collection的removeIf(Predicate filter)方法，批量删除符合filter条件的元素

## Stream 操作集合

> A sequence of elements supporting sequential and parallel aggregate operations.

- Stream是元素的集合，这点让Stream看起来用些类似Iterator；
- 可以支持顺序和并行的对原Stream进行汇聚的操作；

### Stream是什么？高级迭代器

- 大家可以把Stream当成一个高级版本的Iterator
- Iterator,用户只能一个一个的遍历元素并对其执行某些操作
- Stream,用户只要给出需要对其包含的元素执行什么操作

### 什么是Stream api

jdk8使用流水线的方法处理数据。一堆操作符的集合。

dk8 Stream:是一个集合，但是可能接受多个环节的处理。每个环节专门的操作符(处理方法:由于非常简短看起来就像一个符号)

流式	API，获取Stream：Collection的stream()方法

- Stream
- IntStream
- LongStream
- DoubleStream

对应的Builder

- Stream.Builder
- IntStream.Builder
- LongStream.Builder
- DoubleStream.Builder

### Stream

中间方法

| 方法声明           | 功能描述  |
| -------------- | ----- |
| filter()       | 过滤    |
| map()          | 转换    |
| mapToXxx()     | 一对一转换 |
| flatMap()      | 集合扁平化 |
| flatMapToXxx() |       |
| peek()         |       |
| skip()         |       |
| distinct()     | 去重    |
| sorted()       | 排序    |
| limit()        |       |
| concat()       | 合并流   |

末端方法

| 方法声明        | 功能描述        |
| ----------- | ----------- |
| forEach()   | 遍历          |
| toArray()   | 将流中的元素转换成数组 |
| reduce()    | 合并流中的元素     |
| min()       | 最小值         |
| max()       | 最大值         |
| count()     | 获取流中元素的数量   |
| anyMatch()  |             |
| allMatch()  |             |
| nonMatch()  |             |
| findFirst() | 获取流中的第一个元素  |
| findAny()   | 获取流中的任意一个元素 |

### IntStream

| 方法声明       | 功能描述  |
| ---------- | ----- |
| builder()  |       |
| max()      |       |
| min()      |       |
| sum()      |       |
| count()    |       |
| average()  |       |
| allMatch() |       |
| anyMatch() |       |
| map()      | 一对一转换 |
| forEach()  | 遍历    |

