[你应该更新的Java知识之常用程序库（一）](http://www.blogbus.com/dreamhead-logs/226738702.html)

[你应该更新的Java知识之常用程序库（二）](http://www.blogbus.com/dreamhead-logs/226738756.html) 

[你应该更新的Java知识之构建工具](http://www.blogbus.com/dreamhead-logs/227427912.html)

[你应该更新的Java知识之Observer](http://www.blogbus.com/dreamhead-logs/231594181.html)

[你应该更新的Java知识之集合初始化](http://www.blogbus.com/dreamhead-logs/232899025.html)

[你应该更新的Java知识之集合操作](http://www.blogbus.com/dreamhead-logs/234113759.html)

[你应该更新的Java知识之惰性求值](http://www.blogbus.com/dreamhead-logs/234741366.html)

[你应该更新的Java知识之Optional](http://www.blogbus.com/dreamhead-logs/235329092.html)

[你应该更新的Java知识之Optional高级用法](http://www.blogbus.com/dreamhead-logs/235334714.html)

在传统的Java里，为了表示一个集合，我们常常会写出这样的代码：

```java
public class People {
    private List people;

    public void setPeople(List people) {
        this.people = people;
    }

    public List getPeople() {
       return this.people;
   }
     ...
}
```

 严格说来，这样的代码存在缺陷。虽然貌似List被封装到People里，但实际上，这个List的引用却暴露在外面，这个类的用户可以轻松地拿到List的引用，随意修改。所以，下面是一种更严谨的写法：

```java
public class People {
    private List people;

    public void setPeople(List people) {
         this.people.addAll(people);
    }

    @SuppressWarnings("unchecked")
    public List getPeople() {
        return (List)this.people.clone();
    } 

    ...
}
```

 这里的做法基本思路是，做一个副本，保证内部引用（这里的people）不会传播到外面。但在实际编写代码的时候，大多数人都不会这么做，能否意识到这样的问题只是一个方面，这样的代码写起来，也要比原来的那种写法麻烦得多。按照许多公司的做法，这种要求只能放到代码规范里，但无论如何，在程序世界里，人为规定永远是最容易忽略的约定。

 不过，在真实世界中，即便是我们写的只是上面的最简单那种形式，却很少出现问题。原因何在呢？因为大多数情况下，我们编写这样程序的时候，会有一种隐形的约定，这个“List”是不变的。我们设置（set）完这个List之后，基本上不会留着它的引用在做任何操作；而得到（get）它之后，也基本上不会去修改它。

 在这种情况下，我们使用的实际上是一个不变的List。既然是一个不变的List，那不如就更直接地把它表现出来。

 Guava为我们提供了不变集合的概念，对应着各种具体类型，有ImmutableList、ImmutableSet，还有ImmutableMap。从名字上，我们不难看出它们的用法。

 Guava不变集合的做法并不是另起炉灶，而是遵循了Java已有集合框架的约定。比如，通过查阅文档，我们不难发现，ImmutableList就是一个List，只不过使用这个“List”，只不过，当我们尝试调用诸如add、set之类试图修改集合的方法时，它会抛出异常。正是有了这样的基础，不变集合可以和很多已有的类库、框架配合在一起。

 有了这个基础，我们把不变集合的概念应用于之前的代码，它就是下面这个样子：

```java
public class People {
    private ImmutableList people;

    public void setPeople(ImmutableList people) {
        this.people = people;
    }

    public ImmutableList getPeople() {
        return this.people;
    } 
    ...
}
```

这样一来，代码依然很简单，但是，意义却与从前完全不一样了。我们不必再为可能存在的隐忧顾虑了：一旦元素放到集合里，就不可能修改它了，因为它是不可变的。

对于使用这段代码的人来说，getter自不必说，如往常一样使用，setter也不费力。只是当做字面量使用的集合，我们已经在《你应该更新的Java知识之集合初始化》中讨论过了。如果要适配于现有程序库，把一个已有的List转成ImmutableList也不复杂，一个简单的copyOf方法就可以实现

```java
List existingPeople = ... 
ImmutableList immutablePeople = copyOf(existingPeople);
```

为了让讨论更完整，这里不得不提到另外一个不变接口的选择：Iterable。它是从Java 5开始进入JDK的一个接口：

```java
public interface Iterable  {
    Iterator iterator();
}
```

作为一个Java程序员，我们对Iterator简直再熟悉不过了，有了Iterator，我们就可以遍历一个集合。所以，在一些情况下，我们也可以使用Iterable表示一个不变集合。大多数Java已有的集合类都实现了这个接口。

既然它是JDK里的东西，为什么不把它优先推荐呢？原因有几个。最重要的一个原因在于我们熟知的Iterator，它有一个方法叫做remove，可能大多数Java程序员已经习惯性地忽略了这个方法，但它就在哪里，它是一个可以“改变”集合的方法，所以，从语义上说，它不是一个很好的选择。另外，从已有的代码习惯来说，很多程序员还是很喜欢用List、Set作为接口，所以，ImmutableList从心理上来说，更接近已有的习惯。剩下的一个点似乎不那么重要，有些代码真的需要使用到特定类型的接口。不过，就大多数代码而言，我们只是要得到的一个集合，做一些操作，而这些操作我们在《你应该更新的Java知识之集合操作》中做了一些讨论。

在函数式编程中，不变是提升程序稳定性一个很重要的概念。既然我们大多数情况下实际用到的是不变集合，那就不妨直接把它表现出来。

> 原文链接：http://www.blogbus.com/dreamhead-logs/236028457.html