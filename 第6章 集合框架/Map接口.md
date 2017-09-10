## Map接口概述

Map：双列集合类的根接口，用于存储具有键(Key)、值(Value)映射关系的元素，每个元素都包含一对键值，在使用Map集合时可以通过指定的Key找到对应的Value，例如根据一个学生的学号就可以找到对应的学生。Map接口的主要实现类有HashMap和TreeMap。

将键映射到值的对象，一个映射不能包含重复的键，每个键最多只能映射到一个值。其实Map集合中存储的就是键值对。map集合中必须保证键的唯一性。

Map接口和Collection接口的不同

- Map是双列的,Collection是单列的
- Map的键唯一,Collection的子体系Set是唯一的
- Map集合的数据结构值针对键有效，跟值无关
- Collection集合的数据结构是针对元素有效

Map常用的子类：

- Hashtable：内部结构是哈希表，是同步的。不允许null作为键，null作为值。
- Properties：用来存储键值对型的配置文件的信息，可以和IO技术相结合。
- HashMap：内部结构式哈希表，不是同步的。允许null作为键，null作为值。
- TreeMap：内部结构式二叉树，不是同步的。可以对Map结合中的键进行排序。
- HashSet实现Set接口，由哈希表（实际上是一个HashMap实例）支持。

## Map接口常用方法

| 方法声明                | 功能描述         |
| :------------------ | :----------- |
| put(K key, V value) | 有添加和替换功能     |
| putAll(Map m)       | 添加一个Map的元素   |
| clear()             | 清空集合         |
| remove(Object key)  | 根据键删除一个元素    |
| containsKey()       | 判断集合是否包含指定的键 |
| containsValue()     | 判断集合是否包含指定的值 |
| isEmpty()           | 判断集合是否为空     |
| get(Object key)     | 根据键获取值       |
| keySet()            | 获取所有的键       |
| values()            | 获取所有的值       |
| entrySet()          | 获取所有的Entry   |
| size()              | 获取集合元素的个数    |

![map](http://img.blog.csdn.net/20150812154828706)

## HashMap

键是哈希表结构，可以保证键的唯一性

## LinkedHashMap

Map 接口的哈希表和链接列表实现，具有可预知的迭代顺序。

## TreeMap

键是红黑树结构，可以保证键的排序和唯一性，自然排序，比较器排序。

## Map集合遍历

方式1：根据键找值。获取所有键的集合，遍历键的集合，获取到每一个键，根据键找值。

```java
package cn.itcast;

import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/*
 * Map集合的遍历。
 * Map -- 夫妻对
 * 思路：
 * 		A:把所有的丈夫给集中起来。
 * 		B:遍历丈夫的集合，获取得到每一个丈夫。
 * 		C:让丈夫去找自己的妻子。
 * 
 * 转换：
 * 		A:获取所有的键
 * 		B:遍历键的集合，获取得到每一个键
 * 		C:根据键去找值
 */
public class MapDemo {
	public static void main(String[] args) {
		// 创建集合对象
		Map<String, String> map = new HashMap<String, String>();

		// 创建元素并添加到集合
		map.put("杨过", "小龙女");
		map.put("郭靖", "黄蓉");
		map.put("杨康", "穆念慈");
		map.put("陈玄风", "梅超风");

		// 遍历
		// 获取所有的键
		Set<String> set = map.keySet();
		// 遍历键的集合，获取得到每一个键
		for (String key : set) {
			// 根据键去找值
			String value = map.get(key);
			System.out.println(key + "---" + value);
		}
	}
}
```

方式2：根据键值对对象找键和值。

- 获取所有键值对对象的集合
- 遍历键值对对象的集合，获取到每一个键值对对象
- 根据键值对对象找键和值

```java
package cn.itcast;

import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/*
 * Map集合的遍历。
 * Map -- 夫妻对
 * 
 * 思路：
 * 		A:获取所有结婚证的集合
 * 		B:遍历结婚证的集合，得到每一个结婚证
 * 		C:根据结婚证获取丈夫和妻子
 * 
 * 转换：
 * 		A:获取所有键值对对象的集合
 * 		B:遍历键值对对象的集合，得到每一个键值对对象
 * 		C:根据键值对对象获取键和值
 * 
 * 这里面最麻烦的就是键值对对象如何表示呢?
 * 看看我们开始的一个方法：
 * 		Set<Map.Entry<K,V>> entrySet()：返回的是键值对对象的集合
 */
public class MapDemo {
	public static void main(String[] args) {
		// 创建集合对象
		Map<String, String> map = new HashMap<String, String>();

		// 创建元素并添加到集合
		map.put("杨过", "小龙女");
		map.put("郭靖", "黄蓉");
		map.put("杨康", "穆念慈");
		map.put("陈玄风", "梅超风");

		// 获取所有键值对对象的集合
		Set<Map.Entry<String, String>> set = map.entrySet();
		// 遍历键值对对象的集合，得到每一个键值对对象
		for (Map.Entry<String, String> me : set) {
			// 根据键值对对象获取键和值
			String key = me.getKey();
			String value = me.getValue();
			System.out.println(key + "---" + value);
		}
	}
}
```
## Map集合的应用及扩展

```java
package cn.itcast;

import java.util.Scanner;
import java.util.Set;
import java.util.TreeMap;

/*
 * 需求 ："aababcabcdabcde",获取字符串中每一个字母出现的次数要求结果:a(5)b(4)c(3)d(2)e(1)
 * 
 * 分析：
 * 		A:定义一个字符串(可以改进为键盘录入)
 * 		B:定义一个TreeMap集合
 * 			键:Character
 * 			值：Integer
 * 		C:把字符串转换为字符数组
 * 		D:遍历字符数组，得到每一个字符
 * 		E:拿刚才得到的字符作为键到集合中去找值，看返回值
 * 			是null:说明该键不存在，就把该字符作为键，1作为值存储
 * 			不是null:说明该键存在，就把值加1，然后重写存储该键和值
 * 		F:定义字符串缓冲区变量
 * 		G:遍历集合，得到键和值，进行按照要求拼接
 * 		H:把字符串缓冲区转换为字符串输出
 * 
 * 录入：linqingxia
 * 结果：result:a(1)g(1)i(3)l(1)n(2)q(1)x(1)
 */
public class TreeMapDemo {
	public static void main(String[] args) {
		// 定义一个字符串(可以改进为键盘录入)
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入一个字符串：");
		String line = sc.nextLine();

		// 定义一个TreeMap集合
		TreeMap<Character, Integer> tm = new TreeMap<Character, Integer>();

		// 把字符串转换为字符数组
		char[] chs = line.toCharArray();

		// 遍历字符数组，得到每一个字符
		for (char ch : chs) {
			// 拿刚才得到的字符作为键到集合中去找值，看返回值
			Integer i = tm.get(ch);

			// 是null:说明该键不存在，就把该字符作为键，1作为值存储
			if (i == null) {
				tm.put(ch, 1);
			} else {
				// 不是null:说明该键存在，就把值加1，然后重写存储该键和值
				i++;
				tm.put(ch, i);
			}
		}

		// 定义字符串缓冲区变量
		StringBuilder sb = new StringBuilder();

		// 遍历集合，得到键和值，进行按照要求拼接
		Set<Character> set = tm.keySet();
		for (Character key : set) {
			Integer value = tm.get(key);
			sb.append(key).append("(").append(value).append(")");
		}

		// 把字符串缓冲区转换为字符串输出
		String result = sb.toString();
		System.out.println("result:" + result);
	}
}
```
示例2：在很多项目中，应用比较多的是一对多的映射关系，这就可以通过嵌套的形式将多个映射定义到一个大的集合中，并将大的集合分级处理，形成一个体系。
```java
package cn.itcast;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.Set;

class Student {
	private String name;
	private int age;

	public Student() {
		super();
	}

	public Student(String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

}
/*
 * 黑马程序员
 * 		bj	北京校区
 * 			jc	基础班
 * 					林青霞		27
 * 					风清扬		30
 * 			jy	就业班	
 * 					赵雅芝		28
 * 					武鑫		29
 * 		sh	上海校区
 * 			jc	基础班
 * 					郭美美		20
 * 					犀利哥		22
 * 			jy	就业班	
 * 					罗玉凤		21
 * 					马征		23
 * 		gz	广州校区
 * 			jc	基础班
 * 					王力宏		30
 * 					李静磊		32
 * 			jy	就业班	
 * 					郎朗		31
 * 					柳岩		33
 * 		xa	西安校区
 * 			jc	基础班
 * 					范冰冰		27
 * 					刘意		30
 * 			jy	就业班	
 * 					李冰冰		28
 * 					张志豪		29
 */
public class HashMapDemo {
	public static void main(String[] args) {
		// 创建大集合
		HashMap<String, HashMap<String, ArrayList<Student>>> czbkMap = new HashMap<String, HashMap<String, ArrayList<Student>>>();

		// 北京校区数据
		HashMap<String, ArrayList<Student>> bjCzbkMap = new HashMap<String, ArrayList<Student>>();
		ArrayList<Student> array1 = new ArrayList<Student>();
		Student s1 = new Student("林青霞", 27);
		Student s2 = new Student("风清扬", 30);
		array1.add(s1);
		array1.add(s2);
		ArrayList<Student> array2 = new ArrayList<Student>();
		Student s3 = new Student("赵雅芝", 28);
		Student s4 = new Student("武鑫", 29);
		array2.add(s3);
		array2.add(s4);
		bjCzbkMap.put("基础班", array1);
		bjCzbkMap.put("就业班", array2);
		czbkMap.put("北京校区", bjCzbkMap);

		// 西安校区数据
		HashMap<String, ArrayList<Student>> xaCzbkMap = new HashMap<String, ArrayList<Student>>();
		ArrayList<Student> array3 = new ArrayList<Student>();
		Student s5 = new Student("范冰冰", 27);
		Student s6 = new Student("刘意", 30);
		array3.add(s5);
		array3.add(s6);
		ArrayList<Student> array4 = new ArrayList<Student>();
		Student s7 = new Student("李冰冰", 28);
		Student s8 = new Student("张志豪", 29);
		array4.add(s7);
		array4.add(s8);
		xaCzbkMap.put("基础班", array3);
		xaCzbkMap.put("就业班", array4);
		czbkMap.put("西安校区", xaCzbkMap);

		// 遍历集合
		Set<String> czbkMapSet = czbkMap.keySet();
		for (String czbkMapKey : czbkMapSet) {
			System.out.println(czbkMapKey);
			HashMap<String, ArrayList<Student>> czbkMapValue = czbkMap
					.get(czbkMapKey);
			Set<String> czbkMapValueSet = czbkMapValue.keySet();
			for (String czbkMapValueKey : czbkMapValueSet) {
				System.out.println("\t" + czbkMapValueKey);
				ArrayList<Student> czbkMapValueValue = czbkMapValue
						.get(czbkMapValueKey);
				for (Student s : czbkMapValueValue) {
					System.out.println("\t\t" + s.getName() + "---"
							+ s.getAge());
				}
			}
		}
	}
}
```
运行结果：

![](http://img.blog.csdn.net/20150810223459168)