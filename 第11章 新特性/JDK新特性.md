# JDK5新特性

## **1. ENUM枚举**

### **1.1 枚举概述**
枚举是指将变量的值一一列出来,变量的值只限于列举出来的值的范围内。举例：一周只有7天，一年只有12个月等。

回想单例设计模式：单例类是一个类只有一个实例

那么多例类就是一个类有多个实例，但不是无限个数的实例，而是有限个数的实例。这才能是枚举类。

格式是：只有枚举项的枚举类
```java
public enum 枚举类名 {
      枚举项1，枚举项2，枚举项3…;
  }
```
### **1.2 注意事项**

- 定义枚举类要用关键字enum
- 所有枚举类都是Enum的子类
- 枚举类的第一行上必须是枚举项，最后一个枚举项后的分号是可以省略的，但是如果枚举类有其他的东西，这个分号就不能省略。建议不要省略
- 枚举类可以有构造器，但必须是private的，它默认的也是private的。枚举项的用法比较特殊：枚举(“”);
- 枚举类也可以有抽象方法，但是枚举项必须重写该方法
- 枚举在switch语句中的使用

### **1.3 枚举类中的几个常见方法**

![enum枚举](http://img.blog.csdn.net/20161019100055988)

###**1.4 枚举的应用**

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

### 1.5 常量接口 vs 枚举常量类

把常量定义在接口里与类里都能通过编译，那2者到底有什么区别呢？那个更合理？ 

#### 常量接口 
```java
public interface ConstInterfaceA {  
  public static final String CONST_A = "aa";  
  public static final String CONST_C = "ac";  
}  
```

存在问题: 

- 无法限制开发员继承/实现接口. 

- 开发员能够在子接口里继续添加常量.而这些常量可能得不到祖先层的支持. 

- 常量作为参数时,是String,int等弱类型,开发员可以传入没有在常量接口里定义的值,这个问题无法通过编译器发现. 

- 由于开发员可以直接写常量值, 所以不能用==对比,只能用equals对比,不能优化性能 

- 开发员在没有参考资料时,不可能知道某个int型的参数到底应该赋什么内容. 

- 编译时,是直接把常量的值编译到类的二进制代码里,常量的值在升级中变化后,需要重新编译所有引用常量的类,因为里面存的是旧值. 


#### 常量类 

```java
public class ConstClassA {  
  public static final String CONST_A = "aa";  
  public static final String CONST_C = "ac";  
  
  private ConstClassA() {  
  }  
}  
```

常量类可以设置构造函数为private,从而限制继承,也就没有继续添加常量的问题了. 
但是其他问题与常量接口一样无法解决 


#### 枚举常量类 

```java
public class EnumClassA {  
  private String name;  
  
  private EnumClassA(String name) {  
    this.name = name;  
  }  
  
  public static final EnumClassA CONST_A = new EnumClassA("aa");  
  public static final EnumClassA CONST_C = new EnumClassA("ac");  
}  
```

解决了以上所有问题,主要体现在: 

- 私有构造函数,避免被继承和扩展. 

- 定义方法的参数时,必须用枚举常量类类型,如上面的EnumClassA类型,这样就转变成了强类型,不会出现弱类型引起的问题. 

- 常量值地址唯一,可以用==直接对比,性能会有提高. 

- 开发员可以根据该参数类型打开对应的类,从而找到定义的常量. 

- 编译时,没有把常量值编译到代码里,即使常量的值发生变化也不会影响引用常量的类. 


### enum类型 

```java
public static enum Grade {  
   A(4),  
   B(3),  
   C(2),  
   D(1),  
   F(0);  
     
   private int points;  
   Grade(int points) {  
       this.points = points;  
   }  
     
   int getPoints() {  
       return points;  
   }  
};  
```
这是JDK1.5引入的,其实就是枚举常量类的代码封装简化而已。查看enum反编译后的代码与枚举常量类的结构非常相似。这可能是因为java的设计者一开始觉得enum与OO思想不符,所以没有提供支持,但是随着常量接口的滥用和枚举常量类方案的出现,才在JDK1.5里增加了enum

## **2. 静态导入** 

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

## **3. 增强for循环**

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

## **4. 可变参数**

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

## **5. 基本数据类型的自动拆箱与装箱** 

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
## 6. 编译时注解APT

# JDK8新特性

## 泛型的类型推导

### **1. 泛型究竟是什么？**

在讨论类型推导(type inference)之前，必须回顾一下什么是泛型(Generic).泛型是Java SE 1.5的新特性，泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。通俗点讲就是“类型的变量”。这种类型变量可以用在类、接口和方法的创建中。理解Java泛型最简单的方法是把它看成一种便捷语法，能节省你某些Java类型转换(casting)上的操作：

```java
List<Apple> box = new ArrayList<Apple>();box.add(new Apple());
Apple apple =box.get(0);
```

上面的代码自身已表达的很清楚：box是一个装有Apple对象的List。get方法返回一个Apple对象实例，这个过程不需要进行类型转换。没有泛型，上面的代码需要写成这样：

```java
Apple apple = (Apple)box.get(0);
```

当然，泛型绝不像我在这里描述的这么简单，但这不是我们今天的主角，对于泛型还不是很明白的同学需要补课了～当然，最好的参考资料还是官方文档。

### **2. 泛型带来的问题（Java 7之前）**

泛型的最大优点是提供了程序的类型安全同时可以向后兼容，但也有让开发者不爽的地方，就是每次定义时都要写明泛型的类型，这样显式指定不仅感觉有些冗长，最主要是很多程序员不熟悉泛型，因此很多时候不能够给出正确的类型参数，现在通过编译器自动推断泛型的参数类型，能够减少这样的情况，并提高代码可读性。

### **3. Java 7中对于泛型的类型推导方面的改进**

在Java 7以前的版本中使用泛型类型，需要在声明并赋值的时候，两侧都加上泛型类型。比方说这样：

```java
Map<String,Integer> map = new HashMap<String,Integer>(); 
```

很多人当初肯定和我一样，对此感到很不解：我在变量声明中不是已经声明了参数类型了吗？为什么在对象初始化的时候还要显示的写出来？这也是泛型在一开始出现的时候受到很多人吐槽的地方。不过，让人欣慰的是，java在进步的同时，那些设计者们也在不断的改进java的编译器，让它变的更加智能与人性化。这里，就是我们今天的主角：类型推倒...额...不是推倒，是类型推导，即type inference，这哥们儿的出现，再写上面这样的代码的时候，可以很开心地省略掉对象实例化时的参数类型，也就变成了这个样子：

```java
Map<String,Integer> map = new HashMap<>();
```

在这条语句中，编译器会根据变量声明时的泛型类型自动推断出实例化HashMap时的泛型类型。再次提醒一定要注意new HashMap后面的“&lt;>”，只有加上这个“&lt;>”才表示是自动类型推断，否则就是非泛型类型的HashMap，并且在使用编译器编译源代码时会给出一个警告提示（unchecked conversion warning）。这一对尖括号"&lt;>"官方文档中叫做"diamond"。

但是，这时候的类型推导做的并不完全（甚至算是一个半成品），因为在Java SE 7中创建泛型实例时的类型推断是有限制的：只有构造器的参数化类型在上下文中被显著的声明了，才可以使用类型推断，否则不行。例如：下面的例子在java 7无法正确编译（但现在在java8里面可以编译，因为根据方法参数来自动推断泛型的类型）：

```java
List<String> list = new ArrayList<>();
list.add("A");// 由于addAll期望获得Collection<? extends String>类型的参数，因此下面的语句无法通过
list.addAll(new ArrayList<>());
```

### **4. 在Java8中的再进化**

- 支持通过方法上下文推断泛型目标类型
- 支持在方法调用链路当中，泛型类型推断传递到最后一个方法

在最新的java官方文档之中，我们可以看到对于类型推导的定义：

```
Type inference is a Java compiler's ability to look at each method invocation and corresponding declaration to determine the type argument (or arguments) that make the invocation applicable. The inference algorithm determines the types of the arguments and, if available, the type that the result is being assigned, or returned. Finally, the inference algorithm tries to find the most specific type that works with all of the arguments.
```

简言之，类型推导也就是指编译器能够根据你调用的方法和相应的声明来确定需要的参数类型的能力。并且官方文档中还给出了一个例子加以诠释：

```java
static <T> T pick(T a1, T a2) { 
	return a2; 
}
Serializable s = pick("d", new ArrayList<String>());
```

在这里，编译器能够推导出传入pick方法中的第二个参数的类型是Serializable的。

在之前的java版本当中，上面的例子要能够通过编译的话需要这要写：

```java
Serializable s = this.<Serializable>pick("d", new ArrayList<String>());
```

这样写的详细原因可以在Bruce Eckel的java编程思想（第四版）的泛型一章看得到，当然这本书是基于java6的，这个版本还没有类型推导这个概念。看到这里，很多人已经明显能看得出来最新版本中类型推导的强力之处了。已经不仅仅局限于泛型类的声明与实例化过程了，而是延伸到了具有泛型参数的方法当中了。

#### **4.1 类型推导和泛型方法**

Type Inference and Generic Methods

关于新版本中的类型推导和泛型方法，文档中还给了一个稍微复杂一点的例子，我在这里贴出来，原理和上面的Serializable例子都是一样就不再赘述，想巩固的可以再看一下：

```java
public class BoxDemo {

  public static <U> void addBox(U u, java.util.List<Box<U>> boxes) {
    Box<U> box = new Box<>();
    box.set(u);
    boxes.add(box);
  }

  public static <U> void outputBoxes(java.util.List<Box<U>> boxes) {
    int counter = 0;
    for (Box<U> box: boxes) {
      U boxContents = box.get();
      System.out.println("Box #" + counter + " contains [" +
             boxContents.toString() + "]");
      counter++;
    }
  }

  public static void main(String[] args) {
    java.util.ArrayList<Box<Integer>> listOfIntegerBoxes =
      new java.util.ArrayList<>();
    BoxDemo.<Integer>addBox(Integer.valueOf(10), listOfIntegerBoxes);
    BoxDemo.addBox(Integer.valueOf(20), listOfIntegerBoxes);
    BoxDemo.addBox(Integer.valueOf(30), listOfIntegerBoxes);
    BoxDemo.outputBoxes(listOfIntegerBoxes);
  }
}
```

上面这段代码输出为：

```
Box #0 contains [10]
Box #1 contains [20]
Box #2 contains [30]
```

提一下，泛型方法addBox重点就在于在新java版本中你不需要再在方法调用中进行显式的类型说明，像这样：

```
BoxDemo.<Integer>addBox(Integer.valueOf(10), listOfIntegerBoxes);
```

编译器能够从传入addBox中的参数自动推断出参数类型是Integer.

#### **4.2 类型推导与泛型类和非泛型类的泛型构造器**

额...这个也许英语的更好断句一点：Type Inference and Generic Constructors of Generic and Non-Generic Classes

其实，泛型构造器并不是泛型类的专利品，非泛型类也完全可以有自己的泛型构造器，看一下这个例子：

```java
class MyClass<X> {
  <T> MyClass(T t) {
    // ...
  }
}
```

假如对 MyClass类做出下面这样的实例化：

```java
new MyClass<Integer>("")
```

OK，这里我们显示地指出了MyClass的泛参类型X是Integer，而对于构造器，编译器根据传入的String对象（""）推导出形式参数T是String，这个在java7版本之中已经实现了，在Java8中有了什么改进呢？在Java8之后，对于这种具有泛型构造器的泛型类的实例化我们可以这么写：

```java
MyClass<Integer> myObject = new MyClass<>("");
```

对，还是这一对尖括号（&lt;>），江湖人称diamond，这样我们的编译器就能够自动推导出形式参数X是Integer，T是String了。这个其实和我们一开始Map&lt;String,String>的例子很像，只是多了个构造器的泛型化。

需要注意的是：类型推导只能根据调用的参数类型、目标类型（这个马上会讲到）和返回类型（如果有返回的话）进行推导，而不能根据程序后面的一些需求来进行推导。

#### **4.3 目标类型(Target Type)**

前文已经提到过，编译器能够根据目标类型进行类型推导。一个表达式的目标类型指的是一种编译器根据表达式出现的位置而需要的正确的数据类型。比如这个例子：

```java
static <T> List<T> emptyList();
List<String> listOne = Collections.emptyList();
```

在这里，List&lt;String>就是目标类型，因为这里需要的是List&lt;String>，而Collections.emptyList()返回的是List&lt;T>，所以这里编译器就推断T一定是String。这个在Java 7 和 8 中都OK。但是在java 7 中，在下面这种情况中就不能正常编译了：

```java
void processStringList(List<String> stringList) {
    // process stringList
}

processStringList(Collections.emptyList());
```

这个时候，java7就会给出这种错误提示：

```java
//List<Object> cannot be converted to List<String>
```

原因：Collections.emptyList()  返回的是List&lt;T> ，这里的T需要一个具体类型，但是因为不能从方法声明中推断出所需的是String，所以编译器就给T了一个Object的值，很明显，List&lt;Object>不能转型到List&lt;String>.所以在java7版本中你需要这样调用这个方法：

```java
processStringList(Collections.<String>emptyList());
```

但是，在java8中，由于目标类型概念的引入，这里，很明显编译器需要的是List&lt;String>（也就是这里的Target Type），所以编译器推断返回的List&lt;T>中的T一定是String，所以processStringList(Collections.emptyList());这种描述是OK的。

目标类型的使用在Lambda表达式中优势最为明显，相关内容我会慢慢整理出来。

好了，以上就是关于java中类型推导的一些跟人见解，总结来说，越来越完善的类型推导就是完成了一些本来就感觉很理所当然的类型转换工作，只是这些工作满满地全交给了编译器去自动推导而不是让开发者显示地去指定。

## **Lambda表达式**

## **接口的默认方法与静态方法**

## **方法引用**