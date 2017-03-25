这篇文章总结了所有的Java集合（Collection）。主要介绍各个集合的特性和用途，以及在不同的集合类型之间转换的方式。

## 1. Arrays

Array是Java特有的数组。在你知道所要处理数据元素个数的情况下非常好用。`java.util.Arrays` 包含了许多处理数据的实用方法：

| 方法声明                                     | 功能描述                                     |
| :--------------------------------------- | :--------------------------------------- |
| Arrays.asList()                          | 可以从 `Array` 转换成 `List`。可以作为其他集合类型构造器的参数  |
| Arrays.binarySearch()                    | 在一个已排序的或者其中一段中快速查找                       |
| Arrays.copyOf()                          | 如果你想扩大数组容量又不想改变它的内容的时候可以使用这个方法           |
| Arrays.copyOfRange()                     | 可以复制整个数组或其中的一部分                          |
| Arrays.deepEquals()<br>Arrays.deepHashCode() | `Arrays.equals/hashCode`的高级版本，支持子数组的操作   |
| Arrays.equals()                          | 如果你想要比较两个数组是否相等，应该调用这个方法而不是数组对象中的 `equals`方法（数组对象中没有重写`equals()`方法，所以这个方法之比较引用而不比较内容）。这个方法集合了Java 5的自动装箱和无参变量的特性，来实现将一个变量快速地传给 `equals()`方法——所以这个方法在比较了对象的类型之后是直接传值进去比较的 |
| Arrays.fill()                            | 用一个给定的值填充整个数组或其中的一部分                     |
| Arrays.hashCode()                        | 用来根据数组的内容计算其哈希值（数组对象的`hashCode()`不可用）。这个方法集合了Java 5的自动装箱和无参变量的特性，来实现将一个变量快速地传给 `Arrays.hashcode`方法——只是传值进去，不是对象 |
| Arrays.sort()                            | 对整个数组或者数组的一部分进行排序。也可以使用此方法用给定的比较器对对象数组进行排序 |
| Arrays.toString()                        | 打印数组的内容                                  |

如果想要复制整个数组或其中一部分到另一个数组，可以调用 `System.arraycopy()`方法。此方法从源数组中指定的位置复制指定个数的元素到目标数组里。这无疑是一个简便的方法。（有时候用 ByteBuffer bulk复制会更快。可以参考[这篇文章](http://java-performance.info/various-methods-of-binary-serialization-in-java/)）.

最后，所有的集合都可以用`T[] Collection.toArray( T[] a )` 这个方法复制到数组中。通常会用这样的方式调用：

```java
return coll.toArray( new T[ coll.size() ] );
```

这个方法会分配足够大的数组来储存所有的集合，这样 `toArray()` 在返回值时就不必再分配空间了。

## 2. 单线程集合

这一部分介绍的是不支持多线程的集合。这些集合都在`java.util`包里。其中一些在Java 1.o的时候就有了（现在已经弃用），其中大多数在Java 1.4中重新发布。枚举集合在Java 1.5中重新发布，并且从这个版本之后所有的集合都支持泛型。`PriorityQueue`也在Java 1.5中加入。非线程安全的集合架构的最后一个版本是`ArrayDeque` ，也在Java 1.6中重新发布了。

### 2.1 List

- [ArrayList](http://java-performance.info/arraylist-performance/)：最有用的`List`集合实现。由一个整形数字或数组存储了集合的大小（数组中第一个没有使用的元素）。像所有的`List`集合一样，`ArrayList`可以在必要的时候扩展它的大小。ArrayList访问元素的时间开销固定。在尾部添加元素成本低（为常数复杂度），而在头部添加元素成本很高（线性复杂度）。这是由`ArrayList`的实现原理——所有的元素的从角标为0开始一个接着一个排列造成的。也就是说，从要插入的元素位置往后，每个元素都要向后移动一个位置。CPU缓存友好的集合是基于数组的。（其实也不是很友好，因为有时数组会包含对象，这样存储的只是指向实际对象的指针）。
- [LinkedList](http://java-performance.info/linkedlist-performance/)：`Deque`实现：每一个节点都保存着上一个节点和下一个节点的指针。这就意味着数据的存取和更新具有线性复杂度（这也是一个最佳化的实现，每次操作都不会遍历数组一半以上，操作成本最高的元素就是数组中间的那个）。如果想写出高效的`LinkedList`代码可以使用 `ListIterators` 。如果你想用一个`Queue/Deque`实现的话（你只需读取第一个和最后一个元素就行了）——考虑用`ArrayDeque`代替。
- Vector：一个带有线程同步方法的`ArrayList`版本。现在直接用`ArrayList`代替了。

### 2.2 Queues/Deques

- [ArrayDeque](http://java-performance.info/linkedlist-performance/)：`Deque`是基于有首尾指针的数组（环形缓冲区）实现的。和`LinkedList`不同，这个类没有实现`List`接口。因此，如果没有首尾元素的话就不能取出任何元素。这个类比`LinkedList`要好一些，因为它产生的垃圾数量较少（在扩展的时候旧的数组会被丢弃）。
- Stack：一种后进先出的队列。不要在生产代码中使用，使用别的`Deque`来代替（`ArrayDeque`比较好）。
- PriorityQueue：一个基于优先级的队列。使用自然顺序或者制定的比较器来排序。他的主要属性——`poll/peek/remove/element`会返回一个队列的最小值。不仅如此，`PriorityQueue`还实现了`Iterable`接口，队列迭代时不进行排序（或者其他顺序）。在需要排序的集合中，使用这个队列会比`TreeSet`等其他队列要方便。

### 2.3 Maps

- HashMap：最常用的`Map`实现。只是将一个键和值相对应，并没有其他的功能。对于复杂的`hashCode method`，`get/put`方法有固定的复杂度。
- EnumMap：枚举类型作为键值的`Map`。因为键的数量相对固定，所以在内部用一个数组储存对应值。通常来说，效率要高于`HashMap`。
- HashTable：旧`HashMap`的同步版本，新的代码中也使用了`HashMap`。
- IdentityHashMap：这是一个特殊的`Map`版本，它违背了一般`Map`的规则：它使用 “==” 来比较引用而不是调用`Object.equals`来判断相等。这个特性使得此集合在遍历图表的算法中非常实用——可以方便地在`IdentityHashMap`中存储处理过的节点以及相关的数据。
- LinkedHashMap ：`HashMap`和`LinkedList`的结合，所有元素的插入顺序存储在`LinkedList`中。这就是为什么迭代`LinkedHashMap`的条目（entry）、键和值的时候总是遵循插入的顺序。在JDK中，这是每元素消耗内存最大的集合。
- TreeMap：一种基于已排序且带导向信息Map的红黑树。每次插入都会按照自然顺序或者给定的比较器排序。这个`Map`需要实现`equals`方法和`Comparable/Comparator`。`compareTo`需要前后一致。这个类实现了一个`NavigableMap`接口：可以带有与键数量不同的入口，可以得到键的上一个或者下一个入口，可以得到另一`Map`某一范围的键（大致和SQL的`BETWEEN`运算符相同），以及其他的一些方法。
- [WeakHashMap](http://www.codeceo.com/article/java-weakhashmap-source.html)：这种`Map`通常用在数据缓存中。它将键存储在`WeakReference`中，就是说，如果没有强引用指向键对象的话，这些键就可以被垃圾回收线程回收。值被保存在强引用中。因此，你要确保没有引用从值指向键或者将值也保存在弱引用中`m.put(key, new WeakReference(value))`。

### 2.4 Sets

- HashSet：一个基于`HashMap`的`Set`实现。其中，所有的值为“假值”（同一个`Object`对象具备和`HashMap`同样的性能。基于这个特性，这个数据结构会消耗更多不必要的内存。
- [EnumSet](http://java-performance.info/memory-consumption-of-java-data-types-1/)：值为枚举类型的`Set`。Java的每一个`enum`都映射成一个不同的`int`。这就允许使用`BitSet`——一个类似的集合结构，其中每一比特都映射成不同的`enum`。`EnumSet`有两种实现，`RegularEnumSet`——由一个单独的`long`存储（能够存储64个枚举值，99.9%的情况下是够用的），`JumboEnumSet`——由`long[]`存储。
- [BitSet](http://java-performance.info/bit-sets/)：一个比特Set。需要时常考虑用`BitSet`处理一组密集的整数`Set`（比如从一个预先知道的数字开始的id集合）。这个类用 `long[]`来存储`bit`。
- LinkedHashMap：与`HashSet`一样，这个类基于`LinkedHashMap`实现。这是唯一一个保持了插入顺序的`Set`。
- TreeSet：与`HashSet`类似。这个类是基于一个`TreeMap`实例的。这是在单线程部分唯一一个排序的`Set`。

## 3. java.util.Collections

就像有专门的`java.util.Arrays`来处理数组，Java中对集合也有`java.util.Collections`来处理。

第一组方法主要返回集合的各种数据：

- Collections.checkedCollection / checkedList / checkedMap / checkedSet / checkedSortedMap / checkedSortedSet：检查要添加的元素的类型并返回结果。任何尝试添加非法类型的变量都会抛出一个`ClassCastException`异常。这个功能可以防止在运行的时候出错。//fixme
- Collections.emptyList / emptyMap / emptySet ：返回一个固定的空集合，不能添加任何元素。
- Collections.singleton / singletonList / singletonMap：返回一个只有一个入口的 set/list/map 集合。
- Collections.synchronizedCollection / synchronizedList / synchronizedMap / synchronizedSet / synchronizedSortedMap / synchronizedSortedSet：获得集合的线程安全版本（多线程操作时开销低但不高效，而且不支持类似`put`或`update`这样的复合操作）
- Collections.unmodifiableCollection / unmodifiableList / unmodifiableMap / unmodifiableSet / unmodifiableSortedMap / unmodifiableSortedSet：返回一个不可变的集合。当一个不可变对象中包含集合的时候，可以使用此方法。

第二组方法中，其中有一些方法因为某些原因没有加入到集合中：

- Collections.addAll：添加一些元素或者一个数组的内容到集合中。
- Collections.binarySearch：和数组的`Arrays.binarySearch`功能相同。
- Collections.disjoint：检查两个集合是不是没有相同的元素。
- Collections.fill：用一个指定的值代替集合中的所有元素。
- Collections.frequency：集合中有多少元素是和给定元素相同的。
- Collections.indexOfSubList / lastIndexOfSubList：和`String.indexOf(String) / lastIndexOf(String)`方法类似——找出给定的`List`中第一个出现或者最后一个出现的子表。
- Collections.max / min：找出基于自然顺序或者比较器排序的集合中，最大的或者最小的元素。
- Collections.replaceAll：将集合中的某一元素替换成另一个元素。
- Collections.reverse：颠倒排列元素在集合中的顺序。如果你要在排序之后使用这个方法的话，在列表排序时，最好使用`Collections.reverseOrder`比较器。
- Collections.rotate：根据给定的距离旋转元素。
- Collections.shuffle：随机排放`List`集合中的节点，可以给定你自己的生成器——例如 `java.util.Random / java.util.ThreadLocalRandom or java.security.SecureRandom`。
- Collections.sort：将集合按照自然顺序或者给定的顺序排序。
- Collections.swap：交换集合中两个元素的位置（多数开发者都是自己实现这个操作的）。

## 4. 并发集合

这一部分将介绍`java.util.concurrent`包中线程安全的集合。这些集合的主要属性是一个不可分割的必须执行的方法。因为并发的操作，例如`add`或`update`或者`check`再`update`，都有一次以上的调用，必须同步。因为第一步从集合中组合操作查询到的信息在开始第二步操作时可能变为无效数据。

多数的并发集合是在Java 1.5引入的。`ConcurrentSkipListMap / ConcurrentSkipListSet` 和 `LinkedBlockingDeque`是在Java 1.6新加入的。Java 1.7加入了最后的 `ConcurrentLinkedDeque` 和 `LinkedTransferQueue`

### 4.1 Lists

- CopyOnWriteArrayList：list的实现每一次更新都会产生一个新的隐含数组副本，所以这个操作成本很高。通常用在遍历操作比更新操作多的集合，比如`listeners/observers`集合。

### 4.2 Queues/Deques

- ArrayBlockingQueue：基于数组实现的一个有界阻塞队，大小不能重新定义。所以当你试图向一个满的队列添加元素的时候，就会受到阻塞，直到另一个方法从队列中取出元素。
- ConcurrentLinkedDeque / ConcurrentLinkedQueue：基于链表实现的无界队列，添加元素不会堵塞。但是这就要求这个集合的消费者工作速度至少要和生产这一样快，不然内存就会耗尽。严重依赖于CAS(compare-and-set)操作。
- DelayQueue：无界的保存`Delayed`元素的集合。元素只有在延时已经过期的时候才能被取出。队列的第一个元素延期最小（包含负值——延时已经过期）。当你要实现一个延期任务的队列的时候使用（不要自己手动实现——使用`ScheduledThreadPoolExecutor`）。
- LinkedBlockingDeque / LinkedBlockingQueue：可选择有界或者无界基于链表的实现。在队列为空或者满的情况下使用`ReentrantLock-s`。
- LinkedTransferQueue：基于链表的无界队列。除了通常的队列操作，它还有一系列的`transfer`方法，可以让生产者直接给等待的消费者传递信息，这样就不用将元素存储到队列中了。这是一个基于CAS操作的无锁集合。
- PriorityBlockingQueue：`PriorityQueue`的无界的版本。
- SynchronousQueue：一个有界队列，其中没有任何内存容量。这就意味着任何插入操作必须等到响应的取出操作才能执行，反之亦反。如果不需要`Queue`接口的话，通过`Exchanger`类也能完成响应的功能。

### 4.3 Maps

- ConcurrentHashMap：`get`操作全并发访问，`put`操作可配置并发操作的哈希表。并发的级别可以通过构造函数中`concurrencyLevel`参数设置（默认级别16）。该参数会在`Map`内部划分一些分区。在`put`操作的时候只有只有更新的分区是锁住的。这种`Map`不是代替`HashMap`的线程安全版本——任何 `get-then-put`的操作都需要在外部进行同步。
- ConcurrentSkipListMap：基于跳跃列表（Skip List）的`ConcurrentNavigableMap`实现。本质上这种集合可以当做一种`TreeMap`的线程安全版本来使用。

### 4.4 Sets

- ConcurrentSkipListSet：使用 `ConcurrentSkipListMap`来存储的线程安全的`Set`。
- CopyOnWriteArraySet：使用`CopyOnWriteArrayList`来存储的线程安全的`Set`。

## 5. 相关阅读

- [Java 基本类型集合库：Trove](http://java-performance.info/primitive-types-collections-trove-library/)：Trove库概述——存储Java基本类型数据的集合库（与大多数JDK中的`Objects`类不同）。
- [Java常见数据类型内存占用（1）](http://java-performance.info/memory-consumption-of-java-data-types-1/)：各种类的内存占用会所名，包括enums、EnumMap、EnumSet、BitSet、ArrayList、LinkedList和ArrayDeque。
- [Java常见数据类型内存占用（2）](http://java-performance.info/memory-consumption-of-java-data-types-2/)：HashMap、HashSet、LinkedHashMap、LinkedHashSet、TreeMap、TreeSet和JDK 7 PriorityQueue内存占用，以及对应的Trove替代类说明。

## 6. 推荐阅读

如果想要了解更多关于Java集合的知识，推荐阅读以下书籍：

- [Cay S. Horstmann. Core Java Volume I–Fundamentals (9th Edition) (Core Series)](http://www.amazon.com/gp/product/0137081898/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=0137081898&linkCode=as2&tag=javaperfor07e-20) ：第13章描述了Java集合.
- [Naftalin, Wadler. Java Generics and Collections](http://www.amazon.com/gp/product/0596527756/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=0596527756&linkCode=as2&tag=javaperfor07e-20)：书的第2部分（第10章至第17章）专门讨论了Java集合。
- [Goetz, Peierls, Bloch, Bowbeer, Holmes, Lea. Java Concurrency in Practice](http://www.amazon.com/gp/product/0321349601/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=0321349601&linkCode=as2&tag=javaperfor07e-20)：最好的Java并发书籍。第5章讨论了Java 1.5引入的并发集合。

## 7. 总结

| 集合            | 单线程                                      | 并发                                       |
| ------------- | ---------------------------------------- | ---------------------------------------- |
| Lists         | `ArrayList`——基于泛型数<br>`LinkedList`——不推荐使用<br>`Vector`——已废弃（deprecated） | `CopyOnWriteArrayList`——几乎不更新，常用来遍历      |
| Queues/Deques | `ArrayDeque`——基于泛型数组`Stack`——已废弃（deprecated）<br>`PriorityQueue`——读取操作的内容已排序 | `ArrayBlockingQueue`——带边界的阻塞式队列<br>`ConcurrentLinkedDeque / ConcurrentLinkedQueue`——无边界的链表队列（CAS）<br>`DelayQueue`——元素带有延迟的队列<br>`LinkedBlockingDeque / LinkedBlockingQueue`——链表队列（带锁），可设定是否带边界<br>`LinkedTransferQueue`——可将元素`transfer`进行w/o存储<br>`PriorityBlockingQueue`——并发`PriorityQueue`<br>`SynchronousQueue`——使用`Queue`接口进行`Exchanger` |
| Maps          | `HashMap`——通用Map<br>`EnumMap`——键使用`enum`<br>`Hashtable`——已废弃（deprecated）<br>`IdentityHashMap`——键使用`==`进行比较<br>`LinkedHashMap`——保持插入顺序<br>`TreeMap`——键已排序<br>`WeakHashMap`——适用于缓存（cache） | `ConcurrentHashMap`——通用并发Map<br>`ConcurrentSkipListMap`——已排序的并发Map |
| Sets          | `HashSet`——通用set<br>`EnumSet`——`enum` Set<br>`BitSet`——比特或密集的整数Set<br>`LinkedHashSet`——保持插入顺序<br>`TreeSet`——排序Set | `ConcurrentSkipListSet`——排序并发Set<br>`CopyOnWriteArraySet`——几乎不更新，通常只做遍历 |