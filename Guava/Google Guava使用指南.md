#### Guava简介

Google Guava很优秀，大有取代Apache Commons之势。闲话少叙，直接上Guava的终极目标，用原汁原味的英文来说就是**“Our goal is for you to write less code and for the code you do write to be simpler, cleaner, and more readable”**，Guava中主要包括**Basic Utilities，Collections，Caches，Functional Idioms，Concurrency，Strings，Primitives Support，Ranges，I/O等等**，API参考文档[点我](http://google.github.io/guava/releases/snapshot/api/docs/)，Guava在Github上仓库地址[点我](https://github.com/google/guava)

#### Guava使用示例

##### Preconditions

可以用在方法的开始或者是构造函数的开始，对于不合法的校验可以快速报错

```
package cn.codepub.guava.demo;

import static com.google.common.base.Preconditions.checkArgument;
import static com.google.common.base.Preconditions.checkNotNull;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/28 20:12
 * </p>
 * <p>
 * ClassName:PreconditionsDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class PreconditionsDemo {
    public static void main(String[] args) {
        new Car(null);//Exception in thread "main" java.lang.NullPointerException
        //Exception in thread "main" java.lang.IllegalArgumentException: speed (0.0) must be positive
        new Car("Audi").drive(0);
    }

}
class Car {
    private String name;

    public Car(String name) {
        this.name = checkNotNull(name);//NPE Null Pointer Exception
    }
    public void drive(double speed) {
        checkArgument(speed > 0.0, "speed (%s) must be positive", speed);
    }
}

```

##### MoreObjects.toStringHelper()

让你可以更优雅的覆写Object.toString()方法

```
package cn.codepub.guava.demo;

import com.google.common.base.MoreObjects;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/28 20:26
 * </p>
 * <p>
 * ClassName:MoreObjectsDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class MoreObjectsDemo {
    private String name;
    private String userId;
    private String petName;
    private String sex;

    @Override
    public String toString() {
        //prints:MoreObjectsDemo{name=testName, userId=NO1, petName=PIG}
        return MoreObjects.toStringHelper(this).add("name", "testName").add("userId", "NO1").add("petName", "PIG").omitNullValues().toString();
        //prints:MoreObjectsDemo{name=testName, userId=NO1, petName=PIG}
        //return MoreObjects.toStringHelper(this).add("name", "testName").add("userId", "NO1").add("petName", "PIG").toString();
    }
    public static void main(String[] args) {
        System.out.println(new MoreObjectsDemo());
    }
}

```

##### 用Stopwatch代替System.nanoTime()

简单点说就是Stopwatch使用纳秒计时，使用该类度量时间比使用`System.nanoTime()`好，好处在哪呢？基于性能和测试来说，它可以作为另一种替代时间源；根据`System.nanoTime()`的说明，返回的值没有绝对意义，只能解释为相对于另一个返回的时间戳。Stopwatch是一个更有效的抽象,因为它只公开这些相对的值,而不是绝对值。可以通过其提供的`start`和`stop`方法得到。

```
package cn.codepub.guava.demo;

import com.google.common.base.Stopwatch;

import java.util.concurrent.TimeUnit;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/28 20:53
 * </p>
 * <p>
 * ClassName:StopWatchDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class StopWatchDemo {
    public static void main(String[] args) {
        //The Construct Stopwatch is default access permissions

        Stopwatch stopwatch = Stopwatch.createUnstarted();
        //start
        stopwatch.start();
        System.out.println("You can do something!");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        stopwatch.stop();
        long nanos = stopwatch.elapsed(TimeUnit.NANOSECONDS);
        System.out.println(nanos);//1000076976
    }
}

```

##### CharMatcher

```
package cn.codepub.guava.demo;

import com.google.common.base.CharMatcher;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/28 21:31
 * </p>
 * <p>
 * ClassName:CharMatcherDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class CharMatcherDemo {
    private static final CharMatcher ID_MATCHER = CharMatcher.DIGIT.or(CharMatcher.is('-'));

    public static void main(String[] args) {
        String userID = "123454-333";
        String s = ID_MATCHER.retainFrom(userID);
        System.out.println(s);//123454-333
        s = ID_MATCHER.retainFrom("1    test    11-222");
        System.out.println(s);//111-222
    }
}

```

##### String Joining

```
package cn.codepub.guava.demo;

import com.google.common.base.Joiner;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/28 21:43
 * </p>
 * <p>
 * ClassName:StringJoiningDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class StringJoiningDemo {
    private static final Joiner JOINER = Joiner.on(",").skipNulls();
    //set replace string
    private static final Joiner JOINER_USE_FOR_NULL = Joiner.on(",").useForNull("replace");

    public static void main(String[] args) {
        String join = JOINER.join("Kurt", "Kevin", null, "Chris");
        System.out.println(join);//Kurt, Kevin, Chris
        join = JOINER_USE_FOR_NULL.join("Kurt", "Kevin", null, "Chris");
        System.out.println(join);
    }
}

```

##### String Splitting

使用指定的分隔符对字符串进行拆分，默认对空白符不做任何处理，并且不会静默的丢弃末尾分隔符，如果需要处理的话要显式调用`trimResults()`，`omitEmptyStrings()`

```
package cn.codepub.guava.demo;

import com.google.common.base.Splitter;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/28 21:57
 * </p>
 * <p>
 * ClassName:SplitterDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class SplitterDemo {
    public static void main(String[] args) {
        Iterable<String> split = Splitter.on(',').trimResults().omitEmptyStrings()
        .split("foo, ,bar,quux,blue,");
        for (String s : split) {
            System.out.print(s + "---");//foo---bar---quux---blue---
        }
        System.out.println();
        String[] split1 = "foo, ,bar,quux,blue,".split(",");
        for (String s : split1) {
            System.out.print(s + "---");//foo--- ---bar---quux---blue---
        }
    }

}

```

##### Optional

```
package cn.codepub.guava.demo;

import java.util.Optional;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/28 22:24
 * </p>
 * <p>
 * ClassName:OptionalDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class OptionalDemo {
    public static void main(String[] args) {
        //Creating an Optional<T>
        //Optional.of(notNull);
        //Optional.empty();
        //Optional.ofNullable(maybeNull);
        String test = "test";
        // if test == null will throws NPE
        Optional<String> s = Optional.of(test);
        System.out.println(s.get());//test

        Optional<Object> empty = Optional.empty();
        System.out.println(empty);//Optional.empty

        Optional<Object> o = Optional.ofNullable(null);
        System.out.println(o);//Optional.empty
    }

}

```

##### Hashing

```
package cn.codepub.guava.demo;

import com.google.common.base.Charsets;
import com.google.common.hash.HashCode;
import com.google.common.hash.Hashing;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/29 16:24
 * </p>
 * <p>
 * ClassName:HashingDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class HashingDemo {
    public static void main(String[] args) {
        byte sex = 1;
        Person person = new Person(100, "eric", "wang", 1L, sex);
        System.out.println(person.hashCode());//1935260882
    }
}
class Person {
    private int age;
    private String firstName;
    private String lastName;
    private long id;
    private byte sex;

    public Person(int age, String firstName, String lastName, long id, byte sex) {
        this.age = age;
        this.firstName = firstName;
        this.lastName = lastName;
        this.id = id;
        this.sex = sex;
    }

    public int getAge() {
        return this.age;
    }

    public String getFirstName() {
        return this.firstName;
    }

    public String getLastName() {
        return this.lastName;
    }

    public long getId() {
        return this.id;
    }

    public byte getSex() {
        return this.sex;
    }

    @Override
    public int hashCode() {
        HashCode hashCode = Hashing.murmur3_128().newHasher().putInt(this.getAge()).putLong(this.getId())
        .putString(this.getFirstName(), Charsets.UTF_8).putString(this.getLastName(), Charsets.UTF_8)
        .putByte(this.getSex()).hash();
        return hashCode.hashCode();
    }
}

```

##### Caching

```
package cn.codepub.guava.demo;

import com.google.common.cache.*;
import com.sun.corba.se.impl.orbutil.graph.Graph;

import java.security.Key;
import java.util.concurrent.TimeUnit;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/29 16:54
 * </p>
 * <p>
 * ClassName:CachingDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class CachingDemo {
    LoadingCache<Key, Graph> cache = CacheBuilder.newBuilder().maximumSize(10000)
    .expireAfterWrite(10, TimeUnit.MINUTES).removalListener(new RemovalListener<Object, Object>() {
        @Override
        public void onRemoval(RemovalNotification<Object, Object> removalNotification) {
            //implements your listener
        }
    }).build(new CacheLoader<Key, Graph>() {
        @Override
        public Graph load(Key key) throws Exception {
            return null;
        }
    });
}

```

##### 链式调用

Guava中提供了大量方法，让我们可以使用链式调用的方式从而减少代码量。简单来说方法链一般适合对一个对象进行连续操作（集中在一句代码）。一定程度上可以减少代码量，缺点是它占用了函数的返回值。如果不需要使用到函数返回值的话，建议大家在封装自己的代码库的时候可以使用这种方式，提供一个简单的demo如下：

```
package cn.codepub.guava.demo;

/**
 * <p>
 * Created with IntelliJ IDEA. 2015/11/29 16:00
 * </p>
 * <p>
 * ClassName:ChainEncapsulationDemo
 * </p>
 * <p>
 * Description:TODO
 * </P>
 *
 * @author Wang Xu
 * @version V1.0.0
 * @since V1.0.0
 */
public class ChainEncapsulationDemo {
    private String message = "";

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public static void main(String[] args) {
        ChainEncapsulationDemo chainEncapsulationDemo = new ChainEncapsulationDemo();
        chainEncapsulationDemo = chainEncapsulationDemo.method1().method2().method3();
        System.out.println(chainEncapsulationDemo.getMessage());

    }

    public ChainEncapsulationDemo method1() {
        this.setMessage(this.getMessage() + "add method1 ...\n");
        return this;
    }


    public ChainEncapsulationDemo method2() {
        this.setMessage(this.getMessage() + "add method2 ...\n");
        return this;
    }

    public ChainEncapsulationDemo method3() {
        this.setMessage(this.getMessage() + "add method3 ...");
        return this;
    }
}
```

##### Apache VS. Guava

关于使用Apache Commons还是Guava的讨论看[这里](http://stackoverflow.com/questions/4542550/what-are-the-big-improvements-between-guava-and-apache-equivalent-libraries)