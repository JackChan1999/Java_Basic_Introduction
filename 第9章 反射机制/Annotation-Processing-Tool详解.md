在这篇文章中我将阐述如何实现一个注解处理器。首先我将向你解释什么是注解处理器，你可以使用这个强大的工具来做什么及不能做什么。接下来我们将一步一步来实现一个简单的注解处理器。

## 1. 一些基本概念

在开始之前，我们需要声明一件重要的事情是：我们不是在讨论在**运行时**通过反射机制运行处理的注解，而是在讨论在**编译时**处理的注解。
注解处理器是 javac 自带的一个工具，用来在编译时期扫描处理注解信息。你可以为某些注解注册自己的注解处理器。这里，我假设你已经了解什么是注解及如何自定义注解。如果你还未了解注解的话，可以查看[官方文档](http://docs.oracle.com/javase/tutorial/java/annotations/index.html)。注解处理器在 Java 5 的时候就已经存在了，但直到 Java 6 （发布于2006看十二月）的时候才有可用的API。过了一段时间java的使用者们才意识到注解处理器的强大。所以最近几年它才开始流行。
一个特定注解的处理器以 java 源代码（或者已编译的字节码）作为输入，然后生成一些文件（通常是`.java`文件）作为输出。那意味着什么呢？你可以生成 java 代码！这些 java 代码在生成的`.java`文件中。因此你不能改变已经存在的java类，例如添加一个方法。这些生成的 java 文件跟其他手动编写的 java 源代码一样，将会被 javac 编译。

## 2. AbstractProcessor

让我们来看一下处理器的 API。所有的处理器都继承了`AbstractProcessor`，如下所示：

```java
package com.example;

import java.util.LinkedHashSet;
import java.util.Set;
import javax.annotation.processing.AbstractProcessor;
import javax.annotation.processing.ProcessingEnvironment;
import javax.annotation.processing.RoundEnvironment;
import javax.annotation.processing.SupportedAnnotationTypes;
import javax.annotation.processing.SupportedSourceVersion;
import javax.lang.model.SourceVersion;
import javax.lang.model.element.TypeElement;

public class MyProcessor extends AbstractProcessor {

	@Override
	public boolean process(Set<? extends TypeElement> annoations,
			RoundEnvironment env) {
		return false;
	}

	@Override
	public Set<String> getSupportedAnnotationTypes() {
		Set<String> annotataions = new LinkedHashSet<String>();
	    annotataions.add("com.example.MyAnnotation");
	    return annotataions;
	}

	@Override
	public SourceVersion getSupportedSourceVersion() {
		return SourceVersion.latestSupported();
	}

	@Override
	public synchronized void init(ProcessingEnvironment processingEnv) {
		super.init(processingEnv);
	}

}
```

- `init(ProcessingEnvironment processingEnv)` ：所有的注解处理器类**都必须有一个无参构造函数**。然而，有一个特殊的方法`init()`，它会被注解处理工具调用，以`ProcessingEnvironment`作为参数。ProcessingEnvironment 提供了一些实用的工具类`Elements`, `Types`和`Filer`。我们在后面将会使用到它们。

- `process(Set<? extends TypeElement> annoations, RoundEnvironment env)` ：这类似于每个处理器的`main()`方法。你可以在这个方法里面编码实现扫描，处理注解，生成 java 文件。使用`RoundEnvironment` 参数，你可以查询被特定注解标注的元素（原文：you can query for elements annotated with a certain annotation ）。后面我们将会看到详细内容。

- `getSupportedAnnotationTypes()`：在这个方法里面你必须指定哪些注解应该被注解处理器注册。注意，它的返回值是一个`String`集合，包含了你的注解处理器想要处理的注解类型的全称。换句话说，你在这里定义你的注解处理器要处理哪些注解。

- `getSupportedSourceVersion()` ： 用来指定你使用的 java 版本。通常你应该返回`SourceVersion.latestSupported()` 。不过，如果你有足够的理由坚持用 java 6 的话，你也可以返回`SourceVersion.RELEASE_6`。我建议使用`SourceVersion.latestSupported()`。在 Java 7 中，你也可以使用注解的方式来替代重写`getSupportedAnnotationTypes()` 和 `getSupportedSourceVersion()`，如下所示：

  ```java
  @SupportedSourceVersion(value=SourceVersion.RELEASE_7)
  @SupportedAnnotationTypes({
     // Set of full qullified annotation type names
  	"com.example.MyAnnotation",
  	"com.example.AnotherAnnotation"
   })
  public class MyProcessor extends AbstractProcessor {

  	@Override
  	public boolean process(Set<? extends TypeElement> annoations,
  			RoundEnvironment env) {
  		return false;
  	}
  	@Override
  	public synchronized void init(ProcessingEnvironment processingEnv) {
  		super.init(processingEnv);
  	}
  }
  ```

由于兼容性问题，特别是对于 android ，我建议重写`getSupportedAnnotationTypes()` 和 `getSupportedSourceVersion()` ，而不是使用 `@SupportedAnnotationTypes` 和 `@SupportedSourceVersion`。

接下来你必须知道的事情是：注解处理器运行在它自己的 JVM 中。是的，你没看错。javac 启动了一个完整的 java 虚拟机来运行注解处理器。这意味着什么？你可以使用任何你在普通 java 程序中使用的东西。使用 `guava`! 你可以使用依赖注入工具，比如`dagger`或者任何其他你想使用的类库。但不要忘记，即使只是一个小小的处理器，你也应该注意使用高效的算法及设计模式，就像你在开发其他 java 程序中所做的一样。

## 3. 注册你的处理器

你可能会问 “怎样注册我的注解处理器到 javac ？”。你必须提供一个`.jar`文件。就像其他 .jar 文件一样，你将你已经编译好的注解处理器打包到此文件中。并且，在你的 .jar 文件中，你必须打包一个特殊的文件`javax.annotation.processing.Processor`到`META-INF/services`目录下。因此你的 .jar 文件目录结构看起来就你这样：

```
MyProcess.jar
	-com
		-example
			-MyProcess.class
	-META-INF
		-services
			-javax.annotation.processing.Processor
```

`javax.annotation.processing.Processor` 文件的内容是一个列表，每一行是一个注解处理器的全称。例如：

```
com.example.MyProcess
com.example.AnotherProcess
```

## 4. 例子：工厂模式

现在可以举一个实际的例子了。我们使用`maven` 工具来作为我们的编译系统和依赖管理工具。我会把例子的代码放到 [`github`](https://github.com/sockeqwe/annotationprocessing101/tree/master/factory)上。
首先，我必须要说的是，想要找到一个可以使用注解处理器去解决的简单问题来当作教程，并不是一件容易的事。这篇教程中，我们将实现一个非常简单的工厂模式（不是抽象工厂模式）。它只是为了给你简明的介绍注解处理器的API而已。所以这个问题的程序，并不是那么有用，也不是一个真实开发中的例子。再次声明，你能学到的只是注解处理器的相关内容，而不是设计模式。

我们要解决的问题是：我们要实现一个 pizza 店，这个 pizza 店提供给顾客两种 pizza （Margherita 和 Calzone），还有甜点 Tiramisu（提拉米苏）。
简单看一下这段代码：
`Meal.java`：

```java
package com.example.pizza;

public interface Meal {
	public float getPrice();
}
```

`MargheritaPizza.java`：

```java
package com.example.pizza;

public class MargheritaPizza implements Meal{
	@Override
	public float getPrice() {
		return 6.0f;
	}
}
```

`CalzonePizza.java`：

```java
package com.example.pizza;

public class CalzonePizza implements Meal{
	@Override
	public float getPrice() {
		return 8.5f;
	}
}
```

`Tiramisu.java`：

```java
package com.example.pizza;

public class Tiramisu implements Meal{
	@Override
	public float getPrice() {
		return 4.5f;
	}
}
```

顾客要在我们的 pizza 店购买食物的话，就得输入食物的名称：
`PizzaStore.java`：

```java
package com.example.pizza;

import java.util.Scanner;

public class PizzaStore {

	public Meal order(String mealName) {
		if (null == mealName) {
			throw new IllegalArgumentException("name of meal is null!");
		}
		if ("Margherita".equals(mealName)) {
			return new MargheritaPizza();
		}

		if ("Calzone".equals(mealName)) {
			return new CalzonePizza();
		}

		if ("Tiramisu".equals(mealName)) {
			return new Tiramisu();
		}

		throw new IllegalArgumentException("Unknown meal '" + mealName + "'");
	}

	private static String readConsole() {
		Scanner scanner = new Scanner(System.in);
		String meal = scanner.nextLine();
		scanner.close();
		return meal;
	}
	
	public static void main(String[] args) {
		System.out.println("welcome to pizza store");
		PizzaStore pizzaStore = new PizzaStore();
		Meal meal = pizzaStore.order(readConsole());
		System.out.println("Bill:$" + meal.getPrice());
	}
}
```

正如你所见，在`order()`方法中，我们有许多 if 条件判断语句。并且，如果我们添加一种新的 pizza 的话，我们就得添加一个新的 if 条件判断。但是等一下，使用注解处理器和工厂模式，我们可以让一个注解处理器生成这些 if 语句。如此一来，我们想要的代码就像这样子：
`PizzaStore.java`：

```java
package com.example.pizza;

import java.util.Scanner;

public class PizzaStore {

	private MealFactory factory = new MealFactory();
	
	public Meal order(String mealName) {
		return factory.create(mealName);
	}

	private static String readConsole() {
		Scanner scanner = new Scanner(System.in);
		String meal = scanner.nextLine();
		scanner.close();
		return meal;
	}
	
	public static void main(String[] args) {
		System.out.println("welcome to pizza store");
		PizzaStore pizzaStore = new PizzaStore();
		Meal meal = pizzaStore.order(readConsole());
		System.out.println("Bill:$" + meal.getPrice());
	}
}
```

`MealFactory` 类应该是这样的：
`MealFactory.java`：

```java
package com.example.pizza;

public class MealFactory {

	public Meal create(String id) {
		if (id == null) {
			throw new IllegalArgumentException("id is null!");
		}
		if ("Calzone".equals(id)) {
			return new CalzonePizza();
		}

		if ("Tiramisu".equals(id)) {
			return new Tiramisu();
		}

		if ("Margherita".equals(id)) {
			return new MargheritaPizza();
		}

		throw new IllegalArgumentException("Unknown id = " + id);
	}
}
```

## 5. @Factory Annotation

能猜到么，我们打算使用注解处理器生成`MealFactory`类。更一般的说，我们想要提供一个注解和一个处理器用来生成工厂类。
让我们看一下`@Factory`注解：
`Factory.java`：

```java
package com.example.apt;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.CLASS)
public @interface Factory {

	/**
	 * The name of the factory
	 */
	Class<?> type();

	/**
	 * The identifier for determining which item should be instantiated
	 */
	String id();
}
```

 

思想是这样的：我们注解那些食物类，使用`type()`表示这个类属于哪个工厂，使用`id()`表示这个类的具体类型。让我们将`@Factory`注解应用到这些类上吧：
`MargheritaPizza.java`：

```java
package com.example.pizza;

import com.example.apt.Factory;

@Factory(type=MargheritaPizza.class, id="Margherita")
public class MargheritaPizza implements Meal{

	@Override
	public float getPrice() {
		return 6.0f;
	}
}
```

`CalzonePizza.java`：

```java
package com.example.pizza;

import com.example.apt.Factory;

@Factory(type=CalzonePizza.class, id="Calzone")
public class CalzonePizza implements Meal{

	@Override
	public float getPrice() {
		return 8.5f;
	}
}
```

`Tiramisu.java`：

```java
package com.example.pizza;

import com.example.apt.Factory;

@Factory(type=Tiramisu.class, id="Tiramisu")
public class Tiramisu implements Meal{

	@Override
	public float getPrice() {
		return 4.5f;
	}
}
```

你可能会问，我们是不是可以只将`@Factory`注解应用到`Meal`接口上？答案是不行，因为注解是不能被继承的。即在`class X`上有注解，`class Y extends X`，那么`class Y`是不会继承`class X`上的注解的。在我们编写处理器之前，需要明确几点规则：

1. 只有类能够被`@Factory`注解，因为接口和虚类是不能通过`new`操作符实例化的。
2. 被`@Factory`注解的类必须提供一个默认的无参构造函数。否则，我们不能实例化一个对象。
3. 被`@Factory`注解的类必须直接继承或者间接继承`type`指定的类型。（或者实现它，如果`type`指定的是一个接口）
4. 被`@Factory`注解的类中，具有相同的`type`类型的话，这些类就会被组织起来生成一个工厂类。工厂类以`Factory`作为后缀，例如：`type=Meal.class`将会生成`MealFactory`类。
5. `id`的值只能是字符串，且在它的`type`组中必须是唯一的。

## 6. 注解处理器

我将会通过添加一段代码接着解释这段代码的方法，一步一步引导你。三个点号（`...`）表示省略那部分前面已经讨论过或者将在后面讨论的代码。目的就是为了让代码片段更具有可读性。前面已经说过，我们的完整代码将放到`github`上。OK，让我们开始编写我们的`FactoryProcessor`的框架吧：
`FactoryProcessor.java`：

```java
package com.example.apt;

import java.util.LinkedHashMap;
import java.util.LinkedHashSet;
import java.util.Map;
import java.util.Set;
import javax.annotation.processing.AbstractProcessor;
import javax.annotation.processing.Filer;
import javax.annotation.processing.Messager;
import javax.annotation.processing.ProcessingEnvironment;
import javax.annotation.processing.RoundEnvironment;
import javax.lang.model.SourceVersion;
import javax.lang.model.element.TypeElement;
import javax.lang.model.util.Elements;
import javax.lang.model.util.Types;

public class FactoryProcessor extends AbstractProcessor {

	private Types typeUtils;
	private Elements elementUtils;
	private Filer filer;
	private Messager messager;
	private Map<String, FactoryGroupedClasses> factoryClasses = 
			new LinkedHashMap<String, FactoryGroupedClasses>();

	@Override
	public synchronized void init(ProcessingEnvironment processingEnv) {
		super.init(processingEnv);
		typeUtils = processingEnv.getTypeUtils();
	    elementUtils = processingEnv.getElementUtils();
	    filer = processingEnv.getFiler();
	    messager = processingEnv.getMessager();
	}

	@Override
	public boolean process(Set<? extends TypeElement> arg0,
			RoundEnvironment arg1) {
		...
		return false;
	}

	@Override
	public Set<String> getSupportedAnnotationTypes() {
		Set<String> annotataions = new LinkedHashSet<String>();
	    annotataions.add(Factory.class.getCanonicalName());
	    return annotataions;
	}

	@Override
	public SourceVersion getSupportedSourceVersion() {
		return SourceVersion.latestSupported();
	}
}
```

在`getSupportedAnnotationTypes()`方法中，我们指定`@Factory`注解将被这个处理器处理。

## 7. Elements and TypeMirrors

在`init()`方法中，我们使用了以下类型：

- Elements：一个用来处理`Element`的工具类（后面详细说明）
- Types：一个用来处理`TypeMirror`的工具类（后面详细说明）
- Filer：正如这个类的名字所示，你可以使用这个类来创建文件

在注解处理器中，我们扫描 java 源文件，源代码中的每一部分都是`Element`的一个特定类型。换句话说：`Element`代表程序中的元素，比如说 包，类，方法。每一个元素代表一个静态的，语言级别的结构。在下面的例子中，我将添加注释来说明这个问题：

```java
package com.example;

public class Foo { // TypeElement

	private int a; // VariableElement
	private Foo other; // VariableElement

	public Foo() {} // ExecuteableElement

	public void setA( // ExecuteableElement
			int newA // TypeElement
	) {
	}
}
```

你得换个角度来看源代码。它只是结构化的文本而已。它不是可以执行的。你可以把它当作 你试图去解析的XML 文件。或者一棵编译中创建的抽象语法树。就像在 XML 解析器中，有许多DOM元素。你可以通过一个元素找到它的父元素或者子元素。
例如：如果你有一个代表`public class Foo`的`TypeElement`，你就可以迭代访问它的子结点：

```java
TypeElement fooClass = ... ;
for (Element e : fooClass.getEnclosedElements()){ // iterate over children
	Element parent = e.getEnclosingElement();  // parent == fooClass
}
```

如你所见，`Elements`代表源代码，`TypeElement`代表源代码中的元素类型，例如类。然后，`TypeElement`并不包含类的相关信息。你可以从`TypeElement`获取类的名称，但你不能获取类的信息，比如说父类。这些信息可以通过`TypeMirror`获取。你可以通过调用`element.asType()`来获取一个`Element`的`TypeMirror`。

（译注：关于`getEnclosedElements`, `getEnclosingElement` 的解释，参见[官方文档](http://download.oracle.com/technetwork/java/javase/6/docs/zh/api/javax/lang/model/element/Element.html#getEnclosedElements%28%29)）

## 8. Searching For @Factory

让我们一步一步来实现`process()`方法吧。首先我们扫描所有被`@Factory`注解的类：

```java
@Override
public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
	for (Element annotatedElement : roundEnv.getElementsAnnotatedWith(Factory.class)) {
		...
	}
	return false;
}
```

这里并没有什么高深的技术。`roundEnv.getElementsAnnotatedWith(Factory.class)` 返回一个被`@Factory`注解的元素列表。你可能注意到我避免说“返回一个被`@Factory`注解的类列表”。因为它的确是返回了一个`Element`列表。记住：`Element`可以是类，方法，变量等。所以，我们下一步需要做的是检查这个元素是否是一个类：

```java
@Override
public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
	for (Element annotatedElement : roundEnv.getElementsAnnotatedWith(Factory.class)) {
		if(annotatedElement.getKind() != ElementKind.CLASS) {
			...		
			}
	}
	return false;
}
```

为什么需要这样做呢？因为我们要确保只有**class**类型的元素被我们的处理器处理。前面我们已经学过，类是一种`TypeElement`元素。那我们为什么不使用`if (! (annotatedElement instanceof TypeElement))`来检查呢？这是错误的判断，因为接口也是一种`TypeElement`类型。所以在注解处理器中，你应该避免使用`instanceof`，应该用`ElementKind`或者配合`TypeMirror`使用`TypeKind`。

## 9. 错误处理

在`init()`方法中，我们也获取了一个`Messager`的引用。`Messager`为注解处理器提供了一种报告错误消息，警告信息和其他消息的方式。它不是注解处理器开发者的日志工具。`Messager`是用来给那些使用了你的注解处理器的第三方开发者显示信息的。在[官方文档](http://docs.oracle.com/javase/7/docs/api/javax/tools/Diagnostic.Kind.html)中描述了不同级别的信息。非常重要的是`Kind.ERROR`，因为这种消息类型是用来表明我们的注解处理器在处理过程中出错了。有可能是第三方开发者误使用了我们的`@Factory`注解（比如，使用`@Factory`注解了一个接口）。这个概念与传统的 java 应用程序有一点区别。传统的 java 应用程序出现了错误，你可以抛出一个异常。如果你在`process()`中抛出了一个异常，那 jvm 就会崩溃。注解处理器的使用者将会得到一个从 javac 给出的非常难懂的异常错误信息。因为它包含了注解处理器的堆栈信息。因此注解处理器提供了`Messager`类。它能打印漂亮的错误信息，而且你可以链接到引起这个错误的元素上。在现代的IDE中，第三方开发者可以点击错误信息，IDE会跳转到产生错误的代码行中，以便快速定位错误。
回到`process()`方法的实现。如果用户将`@Factory`注解到了一个非`class`的元素上，我们就抛出一个错误信息：

```java
@Override
public boolean process(Set<? extends TypeElement> annotations,
		RoundEnvironment roundEnv) {
	for (Element annotatedElement : roundEnv.getElementsAnnotatedWith(Factory.class)) {
		if(annotatedElement.getKind() != ElementKind.CLASS) {
			error(annotatedElement, "Only classes can be annotated with @%s",
		            Factory.class.getSimpleName());
			return true; // Exit processing
		}
    }
	return false;
}

private void error(Element e, String msg, Object... args) {
    messager.printMessage(
    	Diagnostic.Kind.ERROR,
    	String.format(msg, args),
    	e);
  }
```

为了能够获取`Messager`显示的信息，非常重要的是注解处理器必须不崩溃地完成运行。这就是我们在调用`error()`后执行`return true`的原因。如果我们在这里没有返回的话，`process()`就会继续运行，因为`messager.printMessage( Diagnostic.Kind.ERROR)`并不会终止进程。如果我们没有在打印完错误信息后返回的话，我们就可能会运行到一个空指针异常等等。就像前面所说的，如果我们继续运行`process()`，一旦有处理的异常在`process()`中被抛出，javac 就会打印注解处理器的空指针异常堆栈信息，而不是`Messager`显示的信息。

## 10. 数据模型

在我们继续检查被`@Factory`注解的类是否满足我们前面所说的五条规则之前，我们先介绍一个数据结构，它能让我们更方便的继续处理接下来的工作。有时候问题或者处理器看起来太过简单了，导致一些程序员倾向于用面向过程的方法编写整个处理器。但你知道吗？一个注解处理器仍然是一个 java 程序。所以我们应该使用面向对象，接口，设计模式以及任何你可能在其他普通Java程序中使用的技巧。
虽然我们的`FactoryProcessor`非常简单，但是我们仍然想将一些信息作为对象保存。在`FactoryAnnotationClass`中，我们保存被注解的类的数据，比如合法的类名以及`@Factory`注解本身的一些数据。所以，我们保存`TypeElement`和处理过的`@Factory`注解：
`FactoryAnnotationClass.java`：

```java
package com.example.apt;

import javax.lang.model.element.TypeElement;
import javax.lang.model.type.DeclaredType;
import javax.lang.model.type.MirroredTypeException;

public class FactoryAnnotatedClass {

	private TypeElement annotatedClassElement;
	private String qualifiedSuperClassName;
	private String simpleTypeName;
	private String id;

	public FactoryAnnotatedClass(TypeElement classElement) throws IllegalArgumentException {
		this.annotatedClassElement = classElement;
		Factory annotation = classElement.getAnnotation(Factory.class);
		id = annotation.id();

		if ("".equals(id)) {
			throw new IllegalArgumentException(
					String.format(
							"id() in @%s for class %s is null or empty! that's not allowed",
							Factory.class.getSimpleName(), 
							classElement.getQualifiedName().toString()));
		}

		// Get the full QualifiedTypeName
		try {
			Class<?> clazz = annotation.type();
			qualifiedSuperClassName = clazz.getCanonicalName();
			simpleTypeName = clazz.getSimpleName();
		} catch (MirroredTypeException mte) {
			DeclaredType classTypeMirror = (DeclaredType) mte.getTypeMirror();
			TypeElement classTypeElement = (TypeElement) classTypeMirror.asElement();
			qualifiedSuperClassName = classTypeElement.getQualifiedName().toString();
			simpleTypeName = classTypeElement.getSimpleName().toString();
		}
	}

	/**
	 * Get the id as specified in {@link Factory#id()}. return the id
	 */
	public String getId() {
		return id;
	}

	/**
	 * Get the full qualified name of the type specified in
	 * {@link Factory#type()}.
	 * 
	 * @return qualified name
	 */
	public String getQualifiedFactoryGroupName() {
		return qualifiedSuperClassName;
	}

	/**
	 * Get the simple name of the type specified in {@link Factory#type()}.
	 * 
	 * @return qualified name
	 */
	public String getSimpleFactoryGroupName() {
		return simpleTypeName;
	}

	/**
	 * The original element that was annotated with @Factory
	 */
	public TypeElement getTypeElement() {
		return annotatedClassElement;
	}
}
```

看起来有很多代码，但是最重要的代码在构造函数中，你可以看到如下的代码：

```java
Factory annotation = classElement.getAnnotation(Factory.class);
		id = annotation.id();

		if ("".equals(id)) {
			throw new IllegalArgumentException(
					String.format(
							"id() in @%s for class %s is null or empty! that's not allowed",
							Factory.class.getSimpleName(), 
							classElement.getQualifiedName().toString()));
		}
```

这里，我们获取`@Factory`注解，并检查`id`是否为空。如果`id`为空，我们将会抛出一个`IllegalArgumentException`异常。你可能感到疑惑的是，前面我们说了不要抛出异常，而是使用Messager。但这并不矛盾。我们在这里抛出一个内部异常，后面你将会看到我们在`process()`中捕获了这个异常。我们有两个这样做的理由：

1. 我想说明你应该仍然像普通的Java程序一样编码。抛出和捕获异常被认为是一个好的Java编程实践。
2. 如果我们想要在FactoryAnnotatedClass中正确地打印信息，我们也需要传入Messager对象，就像我们在错误处理一节中已经提到的，注解处理器必须成功结束，才能让`Messager`打印错误信息。如果我们想使用`Messager`打印一个错误信息，我们应该怎样通知`process()`发生了一个错误？最简单的方法，并且我认为了直观的方法，就是抛出一个异常然后让步`process()`捕获它。

接下来，我们将获取`@Fractory`注解中的`type`成员域。我们比较感兴趣的是合法的全名：

```java
// Get the full QualifiedTypeName
try {
	Class<?> clazz = annotation.type();
	qualifiedSuperClassName = clazz.getCanonicalName();
	simpleTypeName = clazz.getSimpleName();
} catch (MirroredTypeException mte) {
	DeclaredType classTypeMirror = (DeclaredType) mte.getTypeMirror();
	TypeElement classTypeElement = (TypeElement) classTypeMirror.asElement();
	qualifiedSuperClassName = classTypeElement.getQualifiedName().toString();
	simpleTypeName = classTypeElement.getSimpleName().toString();
}
```

这有点棘手，因为这里的类型是`java.lang.Class`。那意味着，这是一个真实的`Class`对象。因为注解处理器在编译 java 源码之前执行，所以我们必须得考虑两种情况：

1. 这个类已经被编译过了：这种情况是第三方 .jar 包含已编译的被`@Factory`注解 .class 文件。这种情况下，我们可以像`try` 代码块中所示那样直接获取Class。 (译注：因为`@Factory`的`@Retention`为`RetentionPolicy.CLASS`，所有被编译过的代码也会保留`@Factory`的注解信息)
2. 这个类还没有被编译：这种情况是我们尝试编译被`@Fractory`注解的源代码。这种情况下，直接获取`Class`会抛出`MirroredTypeException`异常。幸运的是，`MirroredTypeException`包含一个`TypeMirror`，它表示我们未被编译类。因为我们知道它一定是一个`Class`类型（我们前面有检查过），所以我们可以将它转换为`DeclaredType`， 然后获取`TypeElement`来读取合法名称。

好了，我们还需要一个叫`FactoryGroupedClasses`的数据结构，用来简单的组合所有的`FactoryAnnotatedClasses`到一起。

```java
package com.example.apt;

import java.io.IOException;
import java.util.LinkedHashMap;
import java.util.Map;

import javax.annotation.processing.Filer;
import javax.lang.model.util.Elements;

public class FactoryGroupedClasses {

	private String qualifiedClassName;

	private Map<String, FactoryAnnotatedClass> itemsMap = new LinkedHashMap<String, FactoryAnnotatedClass>();

	public FactoryGroupedClasses(String qualifiedClassName) {
		this.qualifiedClassName = qualifiedClassName;
	}

	public void add(FactoryAnnotatedClass toInsert)
			throws IdAlreadyUsedException {

		FactoryAnnotatedClass existing = itemsMap.get(toInsert.getId());
		if (existing != null) {
			throw new IdAlreadyUsedException(existing);
		}

		itemsMap.put(toInsert.getId(), toInsert);
	}

	public void generateCode(Elements elementUtils, Filer filer)
			throws IOException {
		...
	}
}
```

如你所见，它只是一个基本的`Map<String, FactoryAnnotatedClass>`，这个`map`用来映射`@Factory.id()`到`FactoryAnnotatedClass`。我们选择使用`Map`，是因为我们想确保每一个`id`都的唯一的。使用`map`查找，这可以很容易实现。`generateCode()`将会被调用来生成工厂在的代码（稍后讨论）。

## 11. 匹配准则

让我们来继续实现`process()`方法。接下来我们要检查被注解的类至少有一个公有构造函数，不是抽象类，继承了特定的类，以及是一个`public`类：

```java
@Override
public boolean process(Set<? extends TypeElement> annotations,
		RoundEnvironment roundEnv) {
	for (Element annotatedElement : roundEnv
			.getElementsAnnotatedWith(Factory.class)) {
		if (annotatedElement.getKind() != ElementKind.CLASS) {
			error(annotatedElement,
					"Only classes can be annotated with @%s",
					Factory.class.getSimpleName());
			return true; // Exit processing
		}

		// We can cast it, because we know that it of ElementKind.CLASS
		TypeElement typeElement = (TypeElement) annotatedElement;

		try {
			FactoryAnnotatedClass annotatedClass = new FactoryAnnotatedClass(
					typeElement); // throws IllegalArgumentException

			if (!isValidClass(annotatedClass)) {
				return true; // Error message printed, exit processing
			}
		} catch (IllegalArgumentException e) {
			// @Factory.id() is empty
			error(typeElement, e.getMessage());
			return true;
		}
		...
	}
	return false;
}

private boolean isValidClass(FactoryAnnotatedClass item) {

	// Cast to TypeElement, has more type specific methods
	TypeElement classElement = item.getTypeElement();

	if (!classElement.getModifiers().contains(Modifier.PUBLIC)) {
		error(classElement, "The class %s is not public.", classElement
				.getQualifiedName().toString());
		return false;
	}

	// Check if it's an abstract class
	if (classElement.getModifiers().contains(Modifier.ABSTRACT)) {
		error(classElement,
				"The class %s is abstract. You can't annotate abstract classes with @%",
				classElement.getQualifiedName().toString(),
				Factory.class.getSimpleName());
		return false;
	}

	// Check inheritance: Class must be childclass as specified in
	// @Factory.type();
	TypeElement superClassElement = elementUtils.getTypeElement(item
			.getQualifiedFactoryGroupName());
	if (superClassElement.getKind() == ElementKind.INTERFACE) {
		// Check interface implemented
		if (!classElement.getInterfaces().contains(
				superClassElement.asType())) {
			error(classElement,
					"The class %s annotated with @%s must implement the interface %s",
					classElement.getQualifiedName().toString(),
					Factory.class.getSimpleName(),
					item.getQualifiedFactoryGroupName());
			return false;
		}
	} else {
		// Check subclassing
		TypeElement currentClass = classElement;
		while (true) {
			TypeMirror superClassType = currentClass.getSuperclass();

			if (superClassType.getKind() == TypeKind.NONE) {
				// Basis class (java.lang.Object) reached, so exit
				error(classElement,
						"The class %s annotated with @%s must inherit from %s",
						classElement.getQualifiedName().toString(),
						Factory.class.getSimpleName(),
						item.getQualifiedFactoryGroupName());
				return false;
			}

			if (superClassType.toString().equals(
					item.getQualifiedFactoryGroupName())) {
				// Required super class found
				break;
			}

			// Moving up in inheritance tree
			currentClass = (TypeElement) typeUtils
					.asElement(superClassType);
		}
	}
	// Check if an empty public constructor is given
	for (Element enclosed : classElement.getEnclosedElements()) {
		if (enclosed.getKind() == ElementKind.CONSTRUCTOR) {
			ExecutableElement constructorElement = (ExecutableElement) enclosed;
			if (constructorElement.getParameters().size() == 0
					&& constructorElement.getModifiers().contains(
							Modifier.PUBLIC)) {
				// Found an empty constructor
				return true;
			}
		}
	}

	// No empty constructor found
	error(classElement,
			"The class %s must provide an public empty default constructor",
			classElement.getQualifiedName().toString());
	return false;
}
```

我们添加了一个`isValidClass()`方法，它检查是否我们所有的规则都被满足了：

- 类必须是`public`的：`classElement.getModifiers().contains(Modifier.PUBLIC)`
- 类不能是抽象的：`classElement.getModifiers().contains(Modifier.ABSTRACT)`
- 类必须是`@Factoy.type()`指定的类型的子类或者接口的实现：首先，我们使用`elementUtils.getTypeElement(item.getQualifiedFactoryGroupName())`来创建一个元素。没错，你可以创建一个`TypeElement`（使用`TypeMirror`），只要你知道合法的类名称。然后我们检查它是一个接口还是一个类：`superClassElement.getKind() == ElementKind.INTERFACE`。有两种情况：如果它是一个接口，就判断`classElement.getInterfaces().contains(superClassElement.asType())`。如果是类，我们就必须使用`currentClass.getSuperclass()`扫描继承树。注意，整个检查也可以使用`typeUtils.isSubtype()`来实现。
- 类必须有一个`public`的无参构造函数：我们遍历所有该类直接封装的元素`classElement.getEnclosedElements()`，然后检查`ElementKind.CONSTRUCTOR`，`Modifier.PUBLIC` 和`constructorElement.getParameters().size() == 0`

如果以上这些条件全都满足，则`isValidClass()`返回 `true`，否则，它打印一个错误信息然后返回 `false`。

## 11. 组合被注解的类

一旦我们检查`isValidClass()`成功，我们就继续添加`FactoryAnnotatedClass`到相应的`FactoryGroupedClasses`中，如下所示：

```java
public boolean process(Set<? extends TypeElement> annotations,
		RoundEnvironment roundEnv) {
	for (Element annotatedElement : roundEnv
			.getElementsAnnotatedWith(Factory.class)) {
			...
		try {
			FactoryAnnotatedClass annotatedClass = new FactoryAnnotatedClass(
					typeElement); // throws IllegalArgumentException

			if (!isValidClass(annotatedClass)) {
				return true; // Error message printed, exit processing
			}

			// Everything is fine, so try to add
			FactoryGroupedClasses factoryClass = factoryClasses
					.get(annotatedClass.getQualifiedFactoryGroupName());
			if (factoryClass == null) {
				String qualifiedGroupName = annotatedClass
						.getQualifiedFactoryGroupName();
				factoryClass = new FactoryGroupedClasses(qualifiedGroupName);
				factoryClasses.put(qualifiedGroupName, factoryClass);
			}

			// Throws IdAlreadyUsedException if id is conflicting with
			// another @Factory annotated class with the same id
			factoryClass.add(annotatedClass);
		} catch (IllegalArgumentException e) {
			// @Factory.id() is empty --> printing error message
			error(typeElement, e.getMessage());
			return true;
		} catch (IdAlreadyUsedException e) {
			FactoryAnnotatedClass existing = e.getExisting();
			// Already existing
			error(annotatedElement,
					"Conflict: The class %s is annotated with @%s with id ='%s' but %s already uses the same id",
					typeElement.getQualifiedName().toString(),
					Factory.class.getSimpleName(), existing
							.getTypeElement().getQualifiedName().toString());
			return true;
		}
	...
```

## 12. 代码生成

我们已经收集了所有被`@Factory`注解的类的信息，这些信息以`FactoryAnnotatedClass`的形式保存在`FactoryGroupedClass`中。现在我们可以为每一个工厂生成 java 文件了：

```java
public boolean process(Set<? extends TypeElement> annotations,
		RoundEnvironment roundEnv) {
		...
	try {
		for (FactoryGroupedClasses factoryClass : factoryClasses.values()) {
			factoryClass.generateCode(elementUtils, filer);
		}
	} catch (IOException e) {
		error(null, e.getMessage());
	}
	
	return true;
}
```

写 java 文件跟写其他文件完全一样。我们可以使用`Filer`提供的一个`Writer`对象来操作。我们可以用字符串拼接的方法写入我们生成的代码。幸运的是，Square公司（因为提供了许多非常优秀的开源项目二非常有名）给我们提供了[`JavaWriter`](https://github.com/square/javapoet/tree/javawriter_2)，这是一个高级的生成Java代码的库：

```java
package com.example.apt;

import java.io.IOException;
import java.io.Writer;
import java.util.EnumSet;
import java.util.LinkedHashMap;
import java.util.Map;

import javax.annotation.processing.Filer;
import javax.lang.model.element.Modifier;
import javax.lang.model.element.PackageElement;
import javax.lang.model.element.TypeElement;
import javax.lang.model.util.Elements;
import javax.tools.JavaFileObject;

import com.squareup.javawriter.JavaWriter;

public class FactoryGroupedClasses {

	/**
	 * Will be added to the name of the generated factory class
	 */
	private static final String SUFFIX = "Factory";

	private String qualifiedClassName;

	private Map<String, FactoryAnnotatedClass> itemsMap = new LinkedHashMap<String, FactoryAnnotatedClass>();

	public FactoryGroupedClasses(String qualifiedClassName) {
		this.qualifiedClassName = qualifiedClassName;
	}

	public void add(FactoryAnnotatedClass toInsert)
			throws IdAlreadyUsedException {

		FactoryAnnotatedClass existing = itemsMap.get(toInsert.getId());
		if (existing != null) {
			throw new IdAlreadyUsedException(existing);
		}

		itemsMap.put(toInsert.getId(), toInsert);
	}

	public void generateCode(Elements elementUtils, Filer filer)
			throws IOException {
		TypeElement superClassName = elementUtils
				.getTypeElement(qualifiedClassName);
		String factoryClassName = superClassName.getSimpleName() + SUFFIX;

		JavaFileObject jfo = filer
				.createSourceFile(qualifiedClassName + SUFFIX);
		Writer writer = jfo.openWriter();
		JavaWriter jw = new JavaWriter(writer);

		// Write package
		PackageElement pkg = elementUtils.getPackageOf(superClassName);
		if (!pkg.isUnnamed()) {
			jw.emitPackage(pkg.getQualifiedName().toString());
			jw.emitEmptyLine();
		} else {
			jw.emitPackage("");
		}

		jw.beginType(factoryClassName, "class", EnumSet.of(Modifier.PUBLIC));
		jw.emitEmptyLine();
		jw.beginMethod(qualifiedClassName, "create",
				EnumSet.of(Modifier.PUBLIC), "String", "id");

		jw.beginControlFlow("if (id == null)");
		jw.emitStatement("throw new IllegalArgumentException(\"id is null!\")");
		jw.endControlFlow();

		for (FactoryAnnotatedClass item : itemsMap.values()) {
			jw.beginControlFlow("if (\"%s\".equals(id))", item.getId());
			jw.emitStatement("return new %s()", item.getTypeElement()
					.getQualifiedName().toString());
			jw.endControlFlow();
			jw.emitEmptyLine();
		}
		jw.emitStatement("throw new IllegalArgumentException(\"Unknown id = \" + id)");
		jw.endMethod();

		jw.endType();

		jw.close();
	}
}
```

## 13. 处理循环

注解处理器可能会有多次处理过程。官方文档解释如下：

> Annotation processing happens in a sequence of rounds. On each round, a processor may be asked to process a subset of the annotations found on the source and class files produced by a prior round. The inputs to the first round of processing are the initial inputs to a run of the tool; these initial inputs can be regarded as the output of a virtual zeroth round of processing.

一个简单的例子：第一轮处理调用了注解处理器的`process()`方法。对应到我们工厂模式的例子：`FactoryProcessor`被初始化一次（不是每次循环都会新建处理器对象），但`process()`可以被多次调用，如果新生成了 java 文件。这听起来有点奇怪，是不？原因是，新生成的源代码文件中也可能包含有`@Factory`注解，这些文件也将会被`FactoryProcessor`处理。
对于我们的`PizzaStore`来说，将会有三轮处理：

| Round | Input                                    | Output           |
| ----- | ---------------------------------------- | ---------------- |
| 1     | CalzonePizza.java Tiramisu.javaMargheritaPizza.javaMeal.javaPizzaStore.java | MealFactory.java |
| 2     | MealFactory.java                         | — none —         |
| 3     | — none —                                 | — none —         |

我在这里解释处理循环还有另外一个原因。如果你仔细看我们的`FactoryProcessor`代码，你就会发现，我们把收集的数据保存到私有成员变量`Map<String, FactoryGroupedClasses> factoryClasses`。在第一轮循环中，我们检测到MagheritaPizza, CalzonePizza 和 Tiramisu 然后我们生成了MealFactory.java 文件。在第二轮循环中我们把 MealFactory.java 当作输入。由于 MealFactory.java 没有`@Factory`注解，所以没有数据被收集，我们预期不会有错误。但是我们得到了下面这个错误：
`Attempt to recreate a file for type com.hannesdorfmann.annotationprocessing101.factory.MealFactory`
这个问题在于，我们从来没有清空过`factoryClasses`。那意味着，在第二轮处理中，`process()`仍然保存着第一轮收集的数据，然后想创建跟第一轮已经创建的相同文件（MealFactory）， 这就导致了这个错误。在我们的例子中，我们知道，只有第一轮我们会检测被`@Factory`注解的类，因此我们可以简单的像下面这样子修正：

```java
public boolean process(Set<? extends TypeElement> annotations,
		RoundEnvironment roundEnv) {
	try {
		for (FactoryGroupedClasses factoryClass : factoryClasses.values()) {
			factoryClass.generateCode(elementUtils, filer);
		}

		// Clear to fix the problem
		factoryClasses.clear();

	} catch (IOException e) {
		error(null, e.getMessage());
	}
	...
}
```

我知道还可以用其他方法来解决这个问题。比如，我们可以设置一个 boolean 标志等等。关键点在于：我们要记住，注解处理器会经过多轮循环处理（每一轮都是通过调用`process()`方法），我们不能覆盖我们已经生成的代码文件。

## 14. 分离处理器和注解

如果你有看我们[`github`](https://github.com/sockeqwe/annotationprocessing101/tree/master/factory)上的工厂处理器代码。你就会发现，我们组织代码到两个模块中。我们之所以那样做，是因为我们想让我们工厂例子的使用者在他们在工程中只编译注解，包含处理器模块只是为了编译。这样做的原因是，在发布程序时注解及生成的代码会被打包到用户程序中，而注解处理器则不会（注解处理器对用户的程序运行是没有用的）。假如注解处理器中使用到了其他第三方库，那就会占用系统资源。如果你是一个 android 开发者，你可能会听说过 65K 方法限制（一个android 的 .dex 文件，最多只可以有 65K 个方法）。如果你在注解处理器中使用了 `Guava`，并且把注解和处理器打包在一个包中，这样的话，Android APK安装包中不只是包含`FactoryProcessor`的代码，而也包含了整个`Guava`的代码。`Guava`有大约20000个方法。所以分开注解和处理器是非常有意义的。

## 15. 实例化生成的类

你已经看到了，在这个`PizzaStore`的例子中，生成了`MealFactory`类，它和其他手写的 java 类没有任何区别。你需要手动实例化它（跟其他 java 对象一样）:

```
public class PizzaStore {

  private MealFactory factory = new MealFactory();

  public Meal order(String mealName) {
    return factory.create(mealName);
  }

  ...
}
```

当然，你也可以使用反射的方法来实例化。这篇文章的主题是注解处理器，所以就不作过多的讨论了。

## 16. 总结

我希望，你现在对注解处理器已经有比较深的印象了。我必须要再次强调的是：注解处理器是一个非常强大的工具，它可以帮助减少很多无聊代码的编写。我也想提醒的是，跟我的简单工厂例子比起来，注解处理器可以做更多复杂的工作。例如，泛型的类型擦除，因为注解处理器是发生在类型擦除（type erasure）之前的。就像你所看到的，你在写注解处理的时候，有两个普遍的问题你必须要处理：1. 如果你想要在其他类中使用 ElementUtils, TypeUtils 和 Messager，你必须把它们作为参数传给它们。2. 你必须做查询Elements的操作。就像之前提到的，处理Element就和解析XML或者HTML一样。对于HTML你可以是用jQuery，如果在注解处理器中，有类似于jQuery的库那真是非常方便的。

最后一段是作者的提醒，原文如下：

> Please note that parts of the code of FactoryProcessor has some edges and pitfalls. These “mistakes” are placed explicit by me to struggle through them as I explain common mistakes while writing annotation processors (like “Attempt to recreate a file”). If you start writing your own annotation processor based on FactoryProcessor DON’T copy and paste this pitfalls. Instead you should avoid them from the very beginning.