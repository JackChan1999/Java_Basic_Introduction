# 1. Collections工具类

## 1.1 Collections类概述

针对集合操作 的工具类，里面的方法都是静态的，可以对集合进行排序、二分查找、反转、混排等。

## 1.2 Collection和Collections的区别

Collection:是单列集合的顶层接口，有子接口List和Set。Collections:是针对集合操作的工具类，有对集合进行排序和二分查找等方法

## 1.3 Collections常用方法

- public static <T> void sort(List<T> list)

使用sort方法可以根据元素的自然顺序 对指定列表按升序进行排序。列表中的所有元素都必须实现 Comparable 接口。此列表内的所有元素都必须是使用指定比较器可相互比较的 

- public static <T> int binarySearch(List<?> list,T key)

使用二分搜索法搜索指定列表，以获得指定对象。

- public static <T> T max(Collection<?> coll)

根据元素的自然顺序，返回给定 collection 的最大元素。

- public static void reverse(List<?> list)

反转指定列表中元素的顺序。

- public static void shuffle(List<?> list)

混排算法所做的正好与 sort 相反: 它打乱在一个 List 中可能有的任何排列的踪迹。也就是说，基于随机源的输入重排该 List, 这样的排列具有相同的可能性（假设随机源是公正的）。这个算法在实现一个碰运气的游戏中是非常有用的。例如，它可被用来混排代表一副牌的 Card 对象的一个 List 。另外，在生成测试案例时，它也是十分有用的。

- fill(List<? super T> list, T obj)

使用指定元素替换指定列表中的所有元素。

- copy(List<? super T> dest, List<? extends T> src) 

将所有元素从一个列表复制到另一个列表。用两个参数，一个目标 List 和一个源 List, 将源的元素拷贝到目标，并覆盖它的内容。目标 List 至少与源一样长。如果它更长，则在目标 List 中的剩余元素不受影响。 

- 集合线程安全化

List&lt;T> synchronizedList(List&lt;T> list);//返回支持的同步（线程安全的）List集合

Map&lt;K,V> synchronizedMap(Map&lt;K,V> m)：返回支持的同步（线程安全的）Map集合

```java
package cn.itcast_01;

import java.util.Collections;
import java.util.List;
import java.util.ArrayList;

/*
 * Collections:是针对集合进行操作的工具类，都是静态方法。
 * 
 * 面试题：
 * Collection和Collections的区别?
 * Collection:是单列集合的顶层接口，有子接口List和Set。
 * Collections:是针对集合操作的工具类，有对集合进行排序和二分查找的方法
 * 
 * 要知道的方法
 * public static <T> void sort(List<T> list)：排序 默认情况下是自然顺序。
 * public static <T> int binarySearch(List<?> list,T key):二分查找
 * public static <T> T max(Collection<?> coll):最大值
 * public static void reverse(List<?> list):反转
 * public static void shuffle(List<?> list):随机置换
 */
public class CollectionsDemo {
	public static void main(String[] args) {
		// 创建集合对象
		List<Integer> list = new ArrayList<Integer>();

		// 添加元素
		list.add(30);
		list.add(20);
		list.add(50);
		list.add(10);
		list.add(40);

		System.out.println("list:" + list);

		// public static <T> void sort(List<T> list)：排序 默认情况下是自然顺序。
		// Collections.sort(list);
		// System.out.println("list:" + list);
		// [10, 20, 30, 40, 50]

		// public static <T> int binarySearch(List<?> list,T key):二分查找
		// System.out
		// .println("binarySearch:" + Collections.binarySearch(list, 30));
		// System.out.println("binarySearch:"
		// + Collections.binarySearch(list, 300));

		// public static <T> T max(Collection<?> coll):最大值
		// System.out.println("max:"+Collections.max(list));

		// public static void reverse(List<?> list):反转
		// Collections.reverse(list);
		// System.out.println("list:" + list);
		
		//public static void shuffle(List<?> list):随机置换
		Collections.shuffle(list);
		System.out.println("list:" + list);
	}
}
```

运行结果：

![](http://img.blog.csdn.net/20150828214536645)

# 2. Arrays工具类  

## 2.1 概述

此类包含用来操作数组（比如排序和搜索）的各种方法。此类还包含一个允许将数组作为列表来查看的静态工厂。 

## 2.2 常用方法

### 集合与数组的转换

(1) 将数组转换为集合

Lsit<T>  asList(T... a);//返回一个受指定数组支持的固定大小的列表。

把数组变成List集合的好处：可以使用集合的思想和方法来操作数组中的元素。如：contains，get，indexOf，subList等方法。

PS:

- 将数组转换成集合，不可使用集合的增删方法，因为数组的长度是固定的。如果进行增删操作，则会产生UnsupportedOperationException的编译异常。
- 如果数组中的元素都是对象，则变成集合时，数组中的元素就直接转为集合中的元素。
- 如果数组中的元素都是基本数据类型，那么会将该数组作为集合中的元素存在。

(2) 集合转数组

### 查找

int binarySearch()：使用二分搜索法来搜索指定的 byte 型数组，以获得指定的值

### 判断

boolean equals(Object[] a, Object[] a2)：判断指定的两个数组是否相等

### 排序

void sort()：对指定的数组按数字升序进行排序。

### 复制

&lt;T> T[] copyOf(T[] original, int newLength) 
复制指定的数组，截取或用 null 填充（如有必要），以使副本具有指定的长度。

### 填充

 void  fill(Object[] a, Object val) 将指定的 Object 引用分配给指定 Object 数组的每个元素。

### 其他方法

- toString(Object[] a)：返回指定数组内容的字符串表示形式。
- int hashCode(Object[] a) 基于指定数组的内容返回哈希码。

```java
package com.heima.test;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Test {
	public static void main(String[] args) {
		System.out.println("1、将数组转成集合");
		System.out.println("-------------------");
		// asList()将数组转换为集合
		List<Integer> list = Arrays.asList(87, 67, 65, 544);
		for (Integer i : list) {
			System.out.println(i);
		}
		System.out.println("2、二分查找");
		System.out.println("-------------------");

		// binarySearch()二分查找
		int[] a = { 23, 45, 67, 8, 32, 45, 6, 7, 85, 54, 3, 432 };
		int index = Arrays.binarySearch(a, 45);
		System.out.println(index);

		System.out.println("3、排序");
		System.out.println("-------------------");

		// sort()排序
		System.out.println("排序前：" + Arrays.toString(a));
		Arrays.sort(a);
		System.out.println("排序后：" + Arrays.toString(a));

		System.out.println("4、集合转成数组");
		System.out.println("-------------------");

		// toArray()集合转成数组
		ArrayList<String> al = new ArrayList<String>();
		al.add("java");
		al.add("javase");
		al.add("php");
		al.add("ruby");
		al.add("android");

		String[] str = al.toArray(new String[al.size()]);
		System.out.println(Arrays.toString(str));
	}
}
```
运行结果：
![](http://img.blog.csdn.net/20150829000616251)