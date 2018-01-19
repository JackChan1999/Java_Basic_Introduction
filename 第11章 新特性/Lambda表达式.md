## Lambda表达式

Lambda表达式本质上是一种匿名方法，将代码块作为函数参数，函数式接口，相当于一个匿名方法，主要作用是代替匿名内部类的烦琐语法。

Lambda表达式有三部分组成

- 形参列表。可以自动类型推导，可以省略参数类型。只有一个参数，可以省略形参列表的()
- 箭头（->）
- 代码块{}，代码块只有一条语句，可以省略{}；代码块只有一条return语句，可以省略return关键字。

只有一个抽象方法的接口（函数式接口），都可以使用Lambda表达式的写法。

```java
public interface MyListener{
    String doSomething(String a, int b);
}
MyListener listener = (String a, int b)->{
    String ret = a + b;
  	return ret;
}
// 自动类型推断
MyListener listener = (a, b)->{
    String ret = a + b;
  	return ret;
}
```

## 函数式接口

函数式接口代表只包含一个抽象方法的接口，函数式接口可以包含多个默认方法，类方法，但只能声明一个抽象方法。

Lambda表达式的结果就是被当成对象。

