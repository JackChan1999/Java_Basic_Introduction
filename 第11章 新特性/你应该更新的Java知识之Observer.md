作为一个Java程序员，不熟悉设计模式简直不好意思和人打招呼，而Observer模式可以说是位列最常用的设计模式之列，虽然有时候在具体代码里，它不一定叫这个名字，比如改头换面叫个Listener，但模式就是这个模式。

手工实现一个Observer也不是多复杂的一件事，只是因为这个设计模式实在太常用了，Java就把它放到了JDK里面：[Observable](http://docs.oracle.com/javase/6/docs/api/java/util/Observable.html)和[Observer](http://docs.oracle.com/javase/6/docs/api/java/util/Observer.html)，从JDK 1.0里，它们就一直在那里。从某种程度上说，它简化了Observer模式的开发，至少我们不用再手工维护自己的Observer列表了。

不过，如前所述，JDK里的Observer从1.0就在那里了，直到Java 7，它都没有什么改变，就连通知的参数还是Object类型。要知道，Java 5就已经泛型了。Java 5是一次大规模的语法调整，许多程序库从那开始重新设计了API，使其更简洁易用。当然，那些不做应对的程序库，多半也就过时了。这也就是这里要讨论知识更新的原因所在。

今天，对于普通的应用，如果要使用Observer模式该如何做呢？答案是[Guava](https://code.google.com/p/guava-libraries/)的[EventBus](http://docs.guava-libraries.googlecode.com/git-history/release/javadoc/com/google/common/eventbus/EventBus.html)。如你所见，它的名字并没有直接告诉你它是一个Observer，但这有什么关系呢，Listener不也是这样。

首先，我们声明一个Observer：
```java
public class EventObserver {
 @Subscribe public void onMessage(Message message) {
   ...
 }
}
```
你会发现，这个类并没有继承任何接口，只是在用来响应通知的方法上声明了一个@Subscribe。

使用EventBus很简单，先声明一个
```java
EventBus eventBus = new EventBus();
```
然后，把我们写好的Observer注册进去：
```java
eventBus.register(new EventObserver());
```
当要通知Observer时，我们只要这样即可：
```java
eventBus.post(message);
```
这里，我们并没有告诉EventBus，我们要处理的是一个Message类型，只是在EventObserver的onMessage方法的接口声明上使用了这个类型而已。但是，当我们把消息发送出去的时候，它会根据类型进行匹配，保证我们的消息正确地发送到对应的地方。

相比于JDK原有的实现，这个实现会更简单。EventObserver不再需要存在一个继承体系中，而继承总是一种枷锁，把我们套牢在一个体系之中：

- 我们不必遵循一个特定的名字，比如Observer的[update](http://docs.oracle.com/javase/6/docs/api/java/util/Observer.html#update(java.util.Observable,%20java.lang.Object))，而这里的名字onMessage是我们自己起的。
- 我们不必遵循特定的类型，比如update方法中作为被观察对象Observable和作为参数的Object，而是根据我们自己的需求选择的类型。

这种变换让静态类型的Java语言，有了一些动态类型的特质，也让程序更加灵活。这种灵活性多半要归功于Annotation，它在很大程度上影响了Java的程序设计风格。

除了标准的EventBus，Guava还提供了另外一个[AsyncEventBus](http://docs.guava-libraries.googlecode.com/git-history/release/javadoc/com/google/common/eventbus/AsyncEventBus.html)，从名字就可以看出，这是一个异步的EventBus，也就是说，消息扔给它之后，会立即返回，至于Observer什么时候处理，那就是它的事情了。

如果你想更多地了解EventBus，那Guava的[官方文档](https://code.google.com/p/guava-libraries/wiki/EventBusExplained)是个不错的去处。