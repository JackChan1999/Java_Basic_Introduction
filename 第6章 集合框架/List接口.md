## List接口概述

有序的 collection（也称为序列/线性表）。此接口的用户可以对列表中每个元素的插入位置进行精确地控制。用户可以根据元素的整数索引（在列表中的位置）访问元素，并搜索列表中的元素。与 set 不同，列表通常允许重复的元素。

List接口是Collection接口的一个子接口，List集合的特性是：有序，可重复，元素有索引，List接口有三个实现类

- ArrayList：底层数据结构是数组，查询快，增删慢，非线程安全，效率高，数据增长为原来的50%
- Vector：底层数据结构是数组，查询快，增删慢，线程安全，效率低，数据增长为原来的一倍
- LinkedList：底层数据结构是链表，查询慢，增删快，非线程安全，效率高


## Vector

底层数据结构是数组，查询快，增删慢，线程安全，效率低。

Vector类特有功能

- public void addElement(E obj)：添加元素
- public E elementAt(int index)：根据索引获取元素
- public Enumeration elements()：获取所有的元素

## ArrayList

底层数据结构是数组，查询快，增删慢，线程不安全，效率高。

## LinkedList

与ArrayList基于数组结构不同的是，LinkedList是基于链表的结构，因此具有链表特有的功能

| 方法声明          | 功能描述          |
| :------------ | :------------ |
| addFirst()    | 在集合的第0个位置添加元素 |
| addLast()     | 在集合的最后位置添加元素  |
| getFirst()    | 获取集合的第0个元素    |
| getLast()     | 获取集合的最后一个元素   |
| removeFirst() | 删除集合的第0个元素    |
| removeLast()  | 删除集合的最后一个元素   |

代码示例：使用LinkedList来模拟一个栈数据结构

```java
package cn.itcast;

import java.util.LinkedList;

/*
 *使用LinkedList模拟栈数据结构的集合，并测试
 *1、栈的特点先进后出
 *2、 LinkedList的特有添加功能addFirst()
 */
class MyStack {
	private LinkedList link;

	public MyStack() {
		link = new LinkedList();
	}

	public void add(Object obj) {
		// 将指定元素插入此列表的开头
		link.addFirst(obj);
	}

	public Object get() {
		// 移除并返回此列表的第一个元素。
		// return link.getFirst();
		return link.removeFirst();
	}

	public boolean isEmpty() {
		return link.isEmpty();
	}
}

/*
 * MyStack的测试
 */
public class MyStackDemo {
	public static void main(String[] args) {
		// 创建集合对象
		MyStack ms = new MyStack();
		// 添加元素
		ms.add("hello");
		ms.add("world");
		ms.add("java");
		ms.add("android");
		ms.add("javase");
		while (!ms.isEmpty()) {
			System.out.println(ms.get());
		}
	}
}
```
运行结果：

![](http://img.blog.csdn.net/20150810204449423)