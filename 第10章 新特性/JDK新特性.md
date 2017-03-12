# **1. ENUM枚举**
## **1.1 枚举概述**
枚举是指将变量的值一一列出来,变量的值只限于列举出来的值的范围内。举例：一周只有7天，一年只有12个月等。

回想单例设计模式：单例类是一个类只有一个实例

那么多例类就是一个类有多个实例，但不是无限个数的实例，而是有限个数的实例。这才能是枚举类。

格式是：只有枚举项的枚举类
```java
public enum 枚举类名 {
      枚举项1，枚举项2，枚举项3…;
  }
```
## **1.2 注意事项**

- 定义枚举类要用关键字enum
- 所有枚举类都是Enum的子类
- 枚举类的第一行上必须是枚举项，最后一个枚举项后的分号是可以省略的，但是如果枚举类有其他的东西，这个分号就不能省略。建议不要省略
- 枚举类可以有构造器，但必须是private的，它默认的也是private的。枚举项的用法比较特殊：枚举(“”);
- 枚举类也可以有抽象方法，但是枚举项必须重写该方法
- 枚举在switch语句中的使用

## **1.3 枚举类中的几个常见方法**

![enum枚举](http://img.blog.csdn.net/20161019100055988)

##**1.4 枚举的应用**

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

# **2. 静态导入** 

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

# **3. 增强for循环**

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

# **4. 可变参数**

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

![可变参数](http://img.blog.csdn.net/20150829015451942?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

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

![可变参数](http://img.blog.csdn.net/20150829015634395?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

# **5. 基本数据类型的自动拆箱与装箱** 

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
# 1. 泛型

## 6.1 泛型概述

泛型（Generic type 或者 generics）是对 Java 语言的类型系统的一种扩展，以支持创建可以按类型进行参数化的类。可以把类型参数看作是使用参数化类型时指定的类型的一个占位符，就像方法的形式参数是运行时传递的值的占位符一样。

泛型是Java SE 1.5的新特性，泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。这种参数类型可以用在类、接口和方法的创建中，分别称为泛型类、泛型接口、泛型方法。 Java语言引入泛型的好处是安全简单。

在Java SE 1.5之前，没有泛型的情况的下，通过对类型Object的引用来实现参数的“任意化”，“任意化”带来的缺点是要做显式的强制类型转换，而这种转换是要求开发者对实际参数类型可以预知的情况下进行的。对于强制类型转换错误的情况，编译器可能不提示错误，在运行的时候才出现异常，这是一个安全隐患。

泛型的好处是在编译的时候检查类型安全，并且所有的强制转换都是自动和隐式的，以提高代码的重用率。

可以在集合框架（Collection framework）中看到泛型的动机。例如，Map 类允许您向一个 Map添加任意类的对象，即使最常见的情况是在给定映射（map）中保存某个特定类型（比如 String）的对象。

因为 Map.get() 被定义为返回 Object，所以一般必须将 Map.get() 的结果强制类型转换为期望的类型，如下面的代码所示：

```java
  Map m = new HashMap();
  m.put("key", "blarg");
  String s = (String) m.get("key");
```

要让程序通过编译，必须将 get() 的结果强制类型转换为 String，并且希望结果真的是一个 String。但是有可能某人已经在该映射中保存了不是 String 的东西，这样的话，上面的代码将会抛出 ClassCastException。

理想情况下，您可能会得出这样一个观点，即 m 是一个 Map，它将 String 键映射到 String 值。这可以让您消除代码中的强制类型转换，同时获得一个附加的类型检查层，该检查层可以防止有人将错误类型的键或值保存在集合中。这就是泛型所做的工作。

```java
package cn.itcast_01;

import java.util.ArrayList;
import java.util.Iterator;

/*
 * ArrayList存储字符串并遍历
 * 
 * 我们按照正常的写法来写这个程序， 结果确出错了。
 * 为什么呢?
 * 		因为我们开始存储的时候，存储了String和Integer两种类型的数据。
 * 		而在遍历的时候，我们把它们都当作String类型处理的，做了转换，所以就报错了。
 * 但是呢，它在编译期间却没有告诉我们。
 * 所以，我就觉得这个设计的不好。
 * 回想一下，我们的数组
 * 		String[] strArray = new String[3];
 * 		strArray[0] = "hello";
 * 		strArray[1] = "world";
 * 		strArray[2] = 10;
 * 集合也模仿着数组的这种做法，在创建对象的时候明确元素的数据类型。这样就不会在有问题了。
 * 而这种技术被称为：泛型。
 * 
 * 泛型：是一种把类型明确的工作推迟到创建对象或者调用方法的时候才去明确的特殊的类型。参数化类型，把类型当作参数一样的传递。
 * 格式：
 * 		<数据类型>
 * 		此处的数据类型只能是引用类型。
 * 好处：
 * 		A:把运行时期的问题提前到了编译期间
 * 		B:避免了强制类型转换
 * 		C:优化了程序设计，解决了黄色警告线
 */
public class GenericDemo {
	public static void main(String[] args) {
		// 创建
		ArrayList<String> array = new ArrayList<String>();

		// 添加元素
		array.add("hello");
		array.add("world");
		array.add("java");
		// array.add(new Integer(100));
		//array.add(10); // JDK5以后的自动装箱
		// 等价于：array.add(Integer.valueOf(10));

		// 遍历
		Iterator<String> it = array.iterator();
		while (it.hasNext()) {
			// ClassCastException
			// String s = (String) it.next();
			String s = it.next();
			System.out.println(s);
		}

		// 看下面这个代码
		// String[] strArray = new String[3];
		// strArray[0] = "hello";
		// strArray[1] = "world";
		// strArray[2] = 10;
	}
}
```
## 6.2 泛型的好处

Java 语言中引入泛型是一个较大的功能增强。不仅语言、类型系统和编译器有了较大的变化，以支持泛型，而且类库也进行了大翻修，所以许多重要的类，比如集合框架，都已经成为泛型化的了。这带来了很多好处：

### 6.2.1 类型安全。 
 泛型的主要目标是提高 Java 程序的类型安全。通过知道使用泛型定义的变量的类型限制，编译器可以在一个高得多的程度上验证类型假设。没有泛型，这些假设就只存在于程序员的头脑中（或者如果幸运的话，还存在于代码注释中）。

 Java 程序中的一种流行技术是定义这样的集合，即它的元素或键是公共类型的，比如“String 列表”或者“String 到 String 的映射”。通过在变量声明中捕获这一附加的类型信息，泛型允许编译器实施这些附加的类型约束。类型错误现在就可以在编译时被捕获了，而不是在运行时当作 ClassCastException 展示出来。将类型检查从运行时挪到编译时有助于您更容易找到错误，并可提高程序的可靠性。

### 6.2.2 消除强制类型转换。

 泛型的一个附带好处是，消除源代码中的许多强制类型转换。这使得代码更加可读，并且减少了出错机会。
 尽管减少强制类型转换可以降低使用泛型类的代码的罗嗦程度，但是声明泛型变量会带来相应的罗嗦。

 3、优化了程序设计，解决了黄色警告线

## 6.3 泛型的应用

### 6.3.1 泛型的内部原理

泛型是提供给javac编译器使用的，可以限定集合中的输入类型，让编译器挡住源程序中的非法输入。但是，编译器编译带类型说明的集合时会去除掉“类型”信息，目的就是使程序运行效率不受影响。因此，对于参数化的泛型类型，getClass()方法的返回值和原始类型完全一样。

```java
package com.itheima.day2;

import java.util.ArrayList;

public class GenericTest {
      
       public static void main(String[] args) {
            ArrayList<String> collection1 = new ArrayList<String>();
            ArrayList collection2 = new ArrayList();
            System. out.println(collection1.getClass() == collection2.getClass());
             //结果：true
      }
}
```

由于编译生成的字节码会去掉泛型的类型信息，只要能跳过编译器，就可以往某个泛型集合中加入其它类型的数据，例如，用反射得到集合，再调用其add方法即可。

```java
package com.itheima.day2;

import java.util.ArrayList;

public class GenericTest {
      
       public static void main(String[] args) throws Exception {
            ArrayList<Integer> collection1 = new ArrayList<Integer>();
            collection1.getClass().getMethod( "add",Object.class).invoke(collection1, "abc");
            System. out.println(collection1.get(0));
      }
}
```

ArrayList<E>类定义和ArrayList<Integer>类引用中涉及如下术语：

- 整个称为ArrayList<E>泛型类型
- ArrayList&lt;E>中的E称为类型变量或类型参数
- 整个ArrayList&lt;Integer>称为参数化的类型
- ArrayList&lt;Integer>中的Integer称为类型参数的实例或实际类型参数
- ArrayList&lt;Integer>中的<>念着typeof
- ArrayList称为原始类型

参数化类型与原始类型的兼容性：参数化类型可以引用一个原始类型的对象，编译报告警告，例如

```java
Collection<String> c = new Vector();//考虑到对以前代码的兼容性，编译器是可以通过的
```
原始类型可以引用一个参数化类型的对象，编译报告警告，例如
```java
Collection c = new Vector<String>();//原来的方法接受一个集合参数，新的类型也要能传进去
```
参数化类型不考虑类型参数的继承关系：

```java
Vector<String> v = new Vector<Object>(); //错误!不写<Object>没错，写了就是明知故犯
Vector<Object> v = new Vector<String>(); //也错误!
```

注意： 

假设Vector&lt;String> v = new Vector&lt;Object>();可以的话，那么以后从v中取出的对象当作String用，而v实际指向的对象中可以加入任意的类型对象；

假设Vector&lt;Object> v = new Vector&lt;String>();可以的话，那么以后可以向v中加入任意的类型对象，而v实际指向的集合中只能装String类型的对象。

编译器不允许创建泛型变量的数组。即在创建数组实例时，数组的元素不能使用参数化的类型。

例如，下面语句有错误：

```java
Vector<Integer> vectorList[] = new Vector<Integer>[10];
```

思考题：

下面的代码会报错误吗？

```java
Vector v1 = new Vector<String>();
Vector<Object> v = v1;
```

答案：编译的时候是不会报错的，因为编译器是一行一行按照语法检查代码的，因此不会出错。

## 6.4 泛型类

把泛型定义在类上，格式:public class 类名<泛型类型1,…>，注意:泛型类型必须是引用类型

```java
package cn.itcast_04;

/*
 * 泛型类的测试
 */
public class ObjectToolDemo {
	public static void main(String[] args) {
		// ObjectTool ot = new ObjectTool();
		//
		// ot.setObj(new String("风清扬"));
		// String s = (String) ot.getObj();
		// System.out.println("姓名是：" + s);
		//
		// ot.setObj(new Integer(30));
		// Integer i = (Integer) ot.getObj();
		// System.out.println("年龄是：" + i);

		// ot.setObj(new String("林青霞"));
		// // ClassCastException
		// Integer ii = (Integer) ot.getObj();
		// System.out.println("姓名是：" + ii);

		System.out.println("-------------");

		ObjectTool<String> ot = new ObjectTool<String>();
		// ot.setObj(new Integer(27)); //这个时候编译期间就过不去
		ot.setObj(new String("林青霞"));
		String s = ot.getObj();
		System.out.println("姓名是：" + s);

		ObjectTool<Integer> ot2 = new ObjectTool<Integer>();
		// ot2.setObj(new String("风清扬"));//这个时候编译期间就过不去
		ot2.setObj(new Integer(27));
		Integer i = ot2.getObj();
		System.out.println("年龄是：" + i);
	}
}
//泛型类：把泛型定义在类上
class ObjectTool<T> {
	private T obj;

	public T getObj() {
		return obj;
	}

	public void setObj(T obj) {
		this.obj = obj;
	}
}
```

## 6.5 泛型方法

把泛型定义在方法上，格式:public <泛型类型> 返回类型 方法名(泛型类型 .)

```java
package cn.itcast_05;

public class ObjectToolDemo {
	public static void main(String[] args) {
		// ObjectTool ot = new ObjectTool();
		// ot.show("hello");
		// ot.show(100);
		// ot.show(true);

		// ObjectTool<String> ot = new ObjectTool<String>();
		// ot.show("hello");
		//
		// ObjectTool<Integer> ot2 = new ObjectTool<Integer>();
		// ot2.show(100);
		//
		// ObjectTool<Boolean> ot3 = new ObjectTool<Boolean>();
		// ot3.show(true);

		// 定义泛型方法后
		ObjectTool ot = new ObjectTool();
		ot.show("hello");
		ot.show(100);
		ot.show(true);
	}
}
//泛型方法：把泛型定义在方法上
class ObjectTool {
	public <T> void show(T t) {
		System.out.println(t);
	}
}
```

## 6.6 泛型接口

把泛型定义在接口上，格式:public  interface 接口名<泛型类型1…>

```java
package cn.itcast_06;

public class InterDemo {
	public static void main(String[] args) {
		// 第一种情况的测试
		// Inter<String> i = new InterImpl();
		// i.show("hello");

		// // 第二种情况的测试
		Inter<String> i = new InterImpl<String>();
		i.show("hello");

		Inter<Integer> ii = new InterImpl<Integer>();
		ii.show(100);
	}
}
//泛型接口：把泛型定义在接口上 
interface Inter<T> {
	public abstract void show(T t);
}
/实现类在实现接口的时候
//第一种情况：已经知道该是什么类型的了

//public class InterImpl implements Inter<String> {
//
//	@Override
//	public void show(String t) {
//		System.out.println(t);
//	}
// }

//第二种情况：还不知道是什么类型的
class InterImpl<T> implements Inter<T> {

	@Override
	public void show(T t) {
		System.out.println(t);
	}
}
```

## 6.7 泛型高级(通配符)

 为了解决类型被限制死了不能动态根据实例来确定的缺点，引入了“通配符泛型”，针对上面的例子，使用通配泛型格式为<? extends Collection>，“？”代表未知类型，这个类型是实现Collection接口。? extends E：向下限定，E及其子类，限定通配符的上边界。? super E：向上限定，E及其父类，限定通配符的下边界。

```java
package cn.itcast_07;

import java.util.ArrayList;
import java.util.Collection;

/*
 * 泛型高级(通配符)
 * ?:任意类型，如果没有明确，那么就是Object以及任意的Java类了
 * ? extends E:向下限定，E及其子类
 * ? super E:向上限定，E极其父类
 */
public class GenericDemo {
	public static void main(String[] args) {
		// 泛型如果明确的写的时候，前后必须一致
		Collection<Object> c1 = new ArrayList<Object>();
		// Collection<Object> c2 = new ArrayList<Animal>();
		// Collection<Object> c3 = new ArrayList<Dog>();
		// Collection<Object> c4 = new ArrayList<Cat>();

		// ?表示任意的类型都是可以的
		Collection<?> c5 = new ArrayList<Object>();
		Collection<?> c6 = new ArrayList<Animal>();
		Collection<?> c7 = new ArrayList<Dog>();
		Collection<?> c8 = new ArrayList<Cat>();

		// ? extends E:向下限定，E及其子类
		// Collection<? extends Animal> c9 = new ArrayList<Object>();
		Collection<? extends Animal> c10 = new ArrayList<Animal>();
		Collection<? extends Animal> c11 = new ArrayList<Dog>();
		Collection<? extends Animal> c12 = new ArrayList<Cat>();

		// ? super E:向上限定，E极其父类
		Collection<? super Animal> c13 = new ArrayList<Object>();
		Collection<? super Animal> c14 = new ArrayList<Animal>();
		// Collection<? super Animal> c15 = new ArrayList<Dog>();
		// Collection<? super Animal> c16 = new ArrayList<Cat>();
	}
}

class Animal {
}

class Dog extends Animal {
}

class Cat extends Animal {
}
```