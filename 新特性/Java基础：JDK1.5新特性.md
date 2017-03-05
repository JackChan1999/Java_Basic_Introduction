##**ENUM枚举**
###**1、枚举概述**
枚举是指将变量的值一一列出来,变量的值只限于列举出来的值的范围内。举例：一周只有7天，一年只有12个月等。

回想单例设计模式：单例类是一个类只有一个实例

那么多例类就是一个类有多个实例，但不是无限个数的实例，而是有限个数的实例。这才能是枚举类。

格式是：只有枚举项的枚举类
```java
public enum 枚举类名 {
      枚举项1，枚举项2，枚举项3…;
  }
```
###**2、注意事项**

- 定义枚举类要用关键字enum
- 所有枚举类都是Enum的子类
- 枚举类的第一行上必须是枚举项，最后一个枚举项后的分号是可以省略的，但是如果枚举类有其他的东西，这个分号就不能省略。建议不要省略
- 枚举类可以有构造器，但必须是private的，它默认的也是private的。枚举项的用法比较特殊：枚举(“”);
- 枚举类也可以有抽象方法，但是枚举项必须重写该方法
- 枚举在switch语句中的使用

###**3、枚举类中的几个常见方法**
  ![这里写图片描述](http://img.blog.csdn.net/20161019100055988)
###**4、枚举的应用**

**用法一：常量**

在JDK1.5 之前，我们定义常量都是： publicstaticfianl.... 。现在好了，有了枚举，可以把相关的常量分组到一个枚举类型里，而且枚举提供了比常量更多的方法。

```java
public enum Color {
  RED, GREEN, BLANK, YELLOW
}
```

**用法二：SWITCH**

JDK1.6之前的switch语句只支持int,char,enum类型，使用枚举，能让我们的代码可读性更强。

```java
enum Signal {
	GREEN, YELLOW, RED
}
public class TrafficLight {
	Signal color = Signal.RED;
	public void change() {
		switch (color) {
		case RED:
			color = Signal.GREEN;
			break;
		case YELLOW:
			color = Signal.RED;
			break;
		case GREEN:
			color = Signal.YELLOW;
			break;
		}
	}
}
```

**用法三：向枚举中添加新方法**

如果打算自定义自己的方法，那么必须在enum实例序列的最后添加一个分号。而且 Java 要求必须先定义 enum 实例。

```java
public enum Color {
	RED("红色", 1), GREEN("绿色", 2), BLANK("白色", 3), YELLO("黄色", 4);
	// 成员变量
	private String name;
	private int index;
	// 构造方法
	private Color(String name, int index) {
		this.name = name;
		this.index = index;
	}
	// 普通方法
	public static String getName(int index) {
		for (Color c : Color.values()) {
			if (c.getIndex() == index) {
				return c.name;
			}
		}
		return null;
	}
	// get set 方法
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getIndex() {
		return index;
	}
	public void setIndex(int index) {
		this.index = index;
	}
}
```
**用法四：覆盖枚举的方法**

下面给出一个toString()方法覆盖的例子。

```java
public enum Color {
	RED("红色", 1), GREEN("绿色", 2), BLANK("白色", 3), YELLO("黄色", 4);
	// 成员变量
	private String name;
	private int index;
	// 构造方法
	private Color(String name, int index) {
		this.name = name;
		this.index = index;
	}
	//覆盖方法
	@Override
	public String toString() {
		return this.index+"_"+this.name;
	}
}
```

**用法五：实现接口**

所有的枚举都继承自java.lang.Enum类。由于Java 不支持多继承，所以枚举对象不能再继承其他类。

```java
public interface Behaviour {
	void print();
	String getInfo();
}
public enum Color implements Behaviour{
	RED("红色", 1), GREEN("绿色", 2), BLANK("白色", 3), YELLO("黄色", 4);
	// 成员变量
	private String name;
	private int index;
	// 构造方法
	private Color(String name, int index) {
		this.name = name;
		this.index = index;
	}
//接口方法
	@Override
	public String getInfo() {
		return this.name;
	}
	//接口方法
	@Override
	public void print() {
		System.out.println(this.index+":"+this.name);
	}
}
```

**用法六：使用接口组织枚举**

```java
public interface Food {
	enum Coffee implements Food{
		BLACK_COFFEE,DECAF_COFFEE,LATTE,CAPPUCCINO
	}
	enum Dessert implements Food{
		FRUIT, CAKE, GELATO
	}
}
```

##**静态导入** 

1、要使用用静态成员（方法和变量）我们必须给出提供这个方法的类。使用静态导入可以使被导入类的所有静态变量和静态方法在当前类直接可见，使用这些静态成员无需再给出他们的类名。

2、不过，过度使用这个特性也会一定程度上降低代码地可读性。
3、格式：import static 包名….类名.方法名;
4、静态导入的注意事项：

- 方法必须是静态的
- 如果有多个同名的静态方法，容易不知道使用谁?这个时候要使用，必须加前缀。由此可见，意义不大，所以一般不用，但是要能看懂。

```java
package cn.itcast_02;

/*
 * 静态导入：
 * 格式：import static 包名….类名.方法名;
 * 可以直接导入到方法的级别
 * 
 * 静态导入的注意事项：
 * 		A:方法必须是静态的
 * 		B:如果有多个同名的静态方法，容易不知道使用谁?这个时候要使用，必须加前缀。由此可见，意义不大，所以一般不用，但是要能看懂。
 */
import static java.lang.Math.abs;
import static java.lang.Math.pow;
import static java.lang.Math.max;

//错误
//import static java.util.ArrayList.add;

public class StaticImportDemo {
	public static void main(String[] args) {
		// System.out.println(java.lang.Math.abs(-100));
		// System.out.println(java.lang.Math.pow(2, 3));
		// System.out.println(java.lang.Math.max(20, 30));
		// 太复杂，我们就引入到import

		// System.out.println(Math.abs(-100));
		// System.out.println(Math.pow(2, 3));
		// System.out.println(Math.max(20, 30));
		// 太复杂，有更简单

//		System.out.println(abs(-100));
		System.out.println(java.lang.Math.abs(-100));
		System.out.println(pow(2, 3));
		System.out.println(max(20, 30));
	}
	
	public static void abs(String s){
		System.out.println(s);
	}
}
```

##**增强for循环**

1、增强for：是for循环的一种。
2、格式：
 
```java
 for(元素数据类型 变量 : 数组或者Collection集合) {
      使用变量即可，该变量就是元素
  }  
```
3、好处：简化了数组和集合的遍历。 
4、弊端： 增强for的目标不能为null。
5、如何解决呢?对增强for的目标先进行不为null的判断，然后在使用。

```java
package cn.itcast_01;

import java.util.ArrayList;
import java.util.List;

/*
 * JDK5的新特性：自动拆装箱,泛型,增强for,静态导入,可变参数,枚举
 * 
 * 增强for:是for循环的一种。
 * 
 * 格式：
 * 		for(元素数据类型 变量 : 数组或者Collection集合) {
 *			使用变量即可，该变量就是元素
 *   	}
 *   
 * 好处：简化了数组和集合的遍历。
 * 
 * 弊端： 增强for的目标不能为null。
 * 如何解决呢?对增强for的目标先进行不为null的判断，然后在使用。
 */
public class ForDemo {
	public static void main(String[] args) {
		// 定义一个int数组
		int[] arr = { 1, 2, 3, 4, 5 };
		for (int x = 0; x < arr.length; x++) {
			System.out.println(arr[x]);
		}
		System.out.println("---------------");
		// 增强for
		for (int x : arr) {
			System.out.println(x);
		}
		System.out.println("---------------");
		// 定义一个字符串数组
		String[] strArray = { "林青霞", "风清扬", "东方不败", "刘意" };
		// 增强for
		for (String s : strArray) {
			System.out.println(s);
		}
		System.out.println("---------------");
		// 定义一个集合
		ArrayList<String> array = new ArrayList<String>();
		array.add("hello");
		array.add("world");
		array.add("java");
		// 增强for
		for (String s : array) {
			System.out.println(s);
		}
		System.out.println("---------------");

		List<String> list = null;
		// NullPointerException
		// 这个s是我们从list里面获取出来的，在获取前，它肯定还好做一个判断
		// 说白了，这就是迭代器的功能
		if (list != null) {
			for (String s : list) {
				System.out.println(s);
			}
		}

		// 增强for其实是用来替代迭代器的
		//ConcurrentModificationException
		// for (String s : array) {
		// if ("world".equals(s)) {
		// array.add("javaee");
		// }
		// }
		// System.out.println("array:" + array);
	}
}
```

##**可变参数**

1、可变参数概述：定义方法的时候不知道该定义多少个参数
2、格式
```java
 修饰符 返回值类型 方法名(数据类型…  变量名){}
```
注意：

- 这里的变量其实是一个数组
- 如果一个方法有可变参数，并且有多个参数，那么，可变参数肯定是最后一个

代码示例1：

```java
package cn.itcast_03;

/*
 * 可变参数：定义方法的时候不知道该定义多少个参数
 * 格式：
 * 		修饰符 返回值类型 方法名(数据类型…  变量名){
 * 
 * 		}
 * 
 * 		注意：
 * 			这里的变量其实是一个数组
 * 			如果一个方法有可变参数，并且有多个参数，那么，可变参数肯定是最后一个
 */				
public class ArgsDemo {
	public static void main(String[] args) {
		// 2个数据求和
		int a = 10;
		int b = 20;
		int result = sum(a, b);
		System.out.println("result:" + result);

		// 3个数据的求和
		int c = 30;
		result = sum(a, b, c);
		System.out.println("result:" + result);

		// 4个数据的求和
		int d = 30;
		result = sum(a, b, c, d);
		System.out.println("result:" + result);

		// 需求：我要写一个求和的功能，到底是几个数据求和呢，我不太清楚，但是我知道在调用的时候我肯定就知道了
		// 为了解决这个问题，Java就提供了一个东西：可变参数
		result = sum(a, b, c, d, 40);
		System.out.println("result:" + result);

		result = sum(a, b, c, d, 40, 50);
		System.out.println("result:" + result);
	}

	public static int sum(int... a) {
		// System.out.println(a);
		//return 0;

		int s = 0;
		
		for(int x : a){
			s +=x;
		}
		
		return s;
	}

	// public static int sum(int a, int b, int c, int d) {
	// return a + b + c + d;
	// }
	//
	// public static int sum(int a, int b, int c) {
	// return a + b + c;
	// }
	//
	// public static int sum(int a, int b) {
	// return a + b;
	// }
}
```

运行结果：

![这里写图片描述](http://img.blog.csdn.net/20150829015451942?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

代码示例2：把数组转成集合

```java
package cn.itcast_03;

import java.util.Arrays;
import java.util.List;

/*
 * public static <T> List<T> asList(T... a):把数组转成集合
 * 
 * 注意事项：
 * 		虽然可以把数组转成集合，但是集合的长度不能改变。
 */
public class ArraysDemo {
	public static void main(String[] args) {
		// 定义一个数组
		// String[] strArray = { "hello", "world", "java" };
		// List<String> list = Arrays.asList(strArray);

		List<String> list = Arrays.asList("hello", "world", "java");
		// UnsupportedOperationException
		// list.add("javaee");
		// UnsupportedOperationException
		// list.remove(1);
		list.set(1, "javaee");

		for (String s : list) {
			System.out.println(s);
		}
	}
}
```

运行结果：

![这里写图片描述](http://img.blog.csdn.net/20150829015634395?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

##**基本数据类型的自动拆箱与装箱** 

1、自动装箱：把基本类型转换为包装类类型
2、自动拆箱：把包装类类型转换为基本类型

```java
package cn.itcast_05;

/*
 * JDK5的新特性
 * 自动装箱：把基本类型转换为包装类类型
 * 自动拆箱：把包装类类型转换为基本类型
 * 
 * 注意一个小问题：
 * 		在使用时，Integer  x = null;代码就会出现NullPointerException。
 * 		建议先判断是否为null，然后再使用。
 */
public class IntegerDemo {
	public static void main(String[] args) {
		// 定义了一个int类型的包装类类型变量i
		// Integer i = new Integer(100);
		Integer ii = 100;
		ii += 200;
		System.out.println("ii:" + ii);

		// 通过反编译后的代码
		// Integer ii = Integer.valueOf(100); //自动装箱
		// ii = Integer.valueOf(ii.intValue() + 200); //自动拆箱，再自动装箱
		// System.out.println((new StringBuilder("ii:")).append(ii).toString());

		Integer iii = null;
		// NullPointerException
		if (iii != null) {
			iii += 1000;
			System.out.println(iii);
		}
	}
}
```