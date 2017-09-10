## Predicate 操作集合

Predicate 谓词对象，函数式接口

- test()

Collection的removeIf(Predicate filter)方法，批量删除符合filter条件的元素

## Stream操作集合

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
| map()          |       |
| mapToXxx()     | 一对一转换 |
| flatMap()      |       |
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

