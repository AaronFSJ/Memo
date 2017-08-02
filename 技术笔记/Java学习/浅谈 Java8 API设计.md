# 浅谈Java 8 API设计 #
任何写Java代码的人都是API设计师！无论编码者是否与他人共享代码，代码仍然被使用：要么其他人或他们自己使用，要么两者皆有。因此，对于所有的Java开发人员来说，了解良好API设计的基础很重要。

一个好的API设计需要仔细思考和大量的经验。幸运的是，我们可以从其他更聪明的人，如Ference Mihaly——正是他的[博客](https://theamiableapi.com/2012/01/16/java-api-design-checklist/)启发我写了这篇Java 8 API附录——那里得到学习。在设计Speedment API时，我们非常依赖于他列出的接口清单。（我建议大家不妨读一读他的指南。）

从一开始就做到这一点很重要，因为一旦API发布，就会成为使用API的人坚实的基石。正如Joshua Bloch曾经说过的：“公共API，就像钻石一样永恒久远。你有机会把它做正确的话，就应该竭尽全力去做。”

一个精心设计的API结合了两个世界的精华，既是坚实而精确的基石，又具有高度的实施灵活性，最终让API设计师和API使用者受益。

至于为什么要使用接口清单？正确地获取API（即定义Java类集合的可见部分）比编写构成API背后实际工作的实现类要困难得多。它是一个真的很少有人掌握的艺术。使用接口清单允许读者避免最明显的错误，成为更好的程序员和节省大量的时间。

强烈建议API设计者将自己置于客户端代码的角度，并从简单性，易用性和一致性方面优化这个视图——而不是考虑实际的API实现。同时，他们应该尽量隐藏尽可能多的实现细节。

## 不要用返回Null来表示一个空值##

可以证明，不一致的null处理（导致无处不在的NullPointerException）是历史上Java应用程序错误最大的唯一来源。一些开发人员将引入null概念当作是计算机科学领域犯的最糟糕的错误之一。幸运的是，减轻Java null处理问题的第一步是在Java 8中引入了Optional类。确保将返回值为空的方法返回Optional，而不是null。

这向API使用者清楚地表明了该方法可能返回值，也可能不返回值。不要因为性能原因的诱惑使用null而不使用Optional。反正Java 8的转义分析将优化掉大多数Optional对象。避免在参数和字段中使用Optional。

你可以这样写

```Java
public Optional<String> getComment() {
    return Optional.ofNullable(comment);
}
```

而不要这样写
```java
public String getComment() {
    return comment; // comment is nullable
}
```
## 不要将数组作为API的传入参数和返回值 ##

当Java 5中引入Enum概念时，出现了一个重大的API错误。我们都知道Enum类有一个名为values()的方法，用来返回所有Enum不同值的数组。现在，因为Java框架必须确保客户端代码不能更改Enum的值（例如，通过直接写入数组），因此必须得为每次调用value()方法生成内部数组的副本。

这导致了较差的性能和较差的客户端代码可用性。如果Enum返回一个不可修改的List，该List可以重用于每个调用，那么客户端代码可以访问更好和更有用的Enum值的模型。在一般情况下，如果API要返回一组元素，考虑公开Stream。这清楚地说明了结果是只读的（与具有set()方法的List相反）。

它还允许客户端代码容易地收集另一个数据结构中的元素或在运行中对它们进行操作。此外，API可以在元素变得可用时（例如，从文件，套接字或从数据库中拉入），延迟生成元素。同样，Java 8改进的转义分析将确保在Java堆上创建实际最少的对象。

也不要使用数组作为方法的输入参数，因为——除非创建数组的保护性副本——使得有可能另一个线程在方法执行期间修改数组的内容。

你可以这样写
```java
public Stream<String> comments() {
    return Stream.of(comments);
}
```
而不要这样写
```Java
public String[] comments() {
    return comments; // Exposes the backing array!
}
```
## 考虑添加静态接口方法以提供用于对象创建的单个入口点##

避免允许客户端代码直接选择接口的实现类。允许客户端代码创建实现类直接创建了一个更直接的API和客户端代码的耦合。它还使得API的基本功能更强，因为现在我们必须保持所有的实现类，就像它们可以从外部观察到，而不仅仅只是提交到接口。

考虑添加静态接口方法，以允许客户端代码来创建（可能为专用的）实现接口的对象。例如，如果我们有一个接口Point有两个方法int x() 和int y() ，那么我们可以显示一个静态方法Point.of( int x，int y) ，产出接口的（隐藏）实现。

所以，如果x和y都为零，那么我们可以返回一个特殊的实现类PointOrigoImpl（没有x或y字段），否则我们返回另一个保存给定x和y值的类PointImpl。确保实现类位于另一个明显不是API一部分的另一个包中（例如，将Point接口放在com.company。product.shape中，将实现放在com.company.product.internal.shape中）。

你可以这样写
```Java
Point point = Point.of(1,2);
```
而不要这样写
```java
Point point = new PointImpl(1,2);
```
## 青睐功能性接口和Lambdas的组合优于继承##

出于好的原因，对于任何给定的Java类，只能有一个超类。此外，在API中展示抽象或基类应该由客户端代码继承，这是一个非常大和有问题的API 功能。避免API继承，而考虑提供静态接口方法，采用一个或多个lambda参数，并将那些给定的lambdas应用到默认的内部API实现类。

这也创造了一个更清晰的关注点分离。例如，并非继承公共API类AbstractReader和覆盖抽象的空的handleError（IOException ioe），我们最好是在Reader接口中公开静态方法或构造器，接口使用Consumer <IOException>并将其应用于内部的通用ReaderImpl。

你可以这样写
```java
Reader reader = Reader.builder()
    .withErrorHandler(IOException::printStackTrace)
    .build();
    ```
而不要这样写
```Java
Reader reader = new AbstractReader() {
    @Override
    public void handleError(IOException ioe) {
        ioe. printStackTrace();
    }
};
```
## 确保你将@FunctionalInterface注解添加到功能性接口##

使用@FunctionalInterface注解标记的接口，表示API用户可以使用lambda实现接口，并且还可以通过防止抽象方法随后被意外添加到API中来确保接口对于lambdas保持长期使用。

你可以这样写
```java
@FunctionalInterface
public interface CircleSegmentConstructor {
    CircleSegment apply(Point cntr, Point p, double ang);
    // abstract methods cannot be added
}
```
而不要这样写
```java
public interface CircleSegmentConstructor {
    CircleSegment apply(Point cntr, Point p, double ang);
    // abstract methods may be accidently added later
}
```
## 避免使用功能性接口作为参数的重载方法##

如果有两个或更多的具有相同名称的函数将功能性接口作为参数，那么这可能会在客户端侧导致lambda模糊。例如，如果有两个Point方法add(Function<Point, String> renderer) 和add(Predicate<Point> logCondition)，并且我们尝试从客户端代码调用point.add(p -> p + “ lambda”) ，那么编译器会无法确定使用哪个方法，并产生错误。相反，请根据具体用途考虑命名方法。

你可以这样写
```Java
public interface Point {
    addRenderer(Function<Point, String> renderer);
    addLogCondition(Predicate<Point> logCondition);
}
```
而不要这样写
```java
public interface Point {
    add(Function<Point, String> renderer);
    add(Predicate<Point> logCondition);
}
```
## 避免在接口中过度使用默认方法##

默认方法可以很容易地添加到接口，有时这是有意义的。例如，想要一个对于任何实现类都期望是相同的并且在功能上要又短又“基本”的方法，那么一个可行的候选项就是默认实现。此外，当扩展API时，出于向后兼容性的原因，提供默认接口方法有时是有意义的。

众所周知，功能性接口只包含一个抽象方法，因此当必须添加其他方法时，默认方法提供了一个安全舱口。然而，通过用不必要的实现问题来污染API接口以避免API接口演变为实现类。如果有疑问，请考虑将方法逻辑移动到单独的实用程序类和/或将其放置在实现类中。

你可以这样写
```java
public interface Line {
    Point start();
    Point end();
    int length();
}
```
而不要这样写
```java
public interface Line {
    Point start();
    Point end();
    default int length() {
        int deltaX = start().x() - end().x();
        int deltaY = start().y() - end().y();
    return (int) Math.sqrt(
        deltaX * deltaX + deltaY * deltaY
        );
    }
}
```
## 确保在执行之前进行API方法的参数不变量检查##

在历史上，人们一直草率地在确保验证方法输入参数。因此，当稍后发生结果错误时，真正的原因变得模糊并隐藏在堆栈跟踪下。确保在实现类中使用参数之前检查参数的空值和任何有效的范围约束或前提条件。不要因性能原因而跳过参数检查的诱惑。

JVM能够优化掉冗余检查并产生高效的代码。好好利用Objects.requireNonNull()方法。参数检查也是实施API约定的一个重要方法。如果不想API接受null但是却做了，用户会感到困惑。

你可以这样写
```java
public void addToSegment(Segment segment, Point point) {
    Objects.requireNonNull(segment);
    Objects.requireNonNull(point);
    segment.add(point);
}```
而不要这样写
```java
public void addToSegment(Segment segment, Point point) {
    segment.add(point);
}```
不要简单地调用Optional.get()

Java 8的API设计师犯了一个错误，在他们选择名称Optional.get()的时候，其实应该被命名为Optional.getOrThrow()或类似的东西。调用get()而没有检查一个值是否与Optional.isPresent()方法同在是一个非常常见的错误，这个错误完全否定了Optional原本承诺的null消除功能。考虑在API的实现类中使用任一Optional的其他方法，如map()，flatMap()或ifPresent()，或者确保在调用get()之前调用isPresent()。

你可以这样写
```java
Optional<String> comment = // some Optional value
String guiText = comment
  .map(c -> "Comment: " + c)
  .orElse("");
```
而不要这样写
```java
Optional<String> comment = // some Optional value
String guiText = "Comment: " + comment.get();
```
## 考虑在不同的API实现类中分行调用接口##

最后，所有API都将包含错误。当接收来自于API用户的堆栈跟踪时，如果将不同的接口分割为不同的行，相比于在单行上表达更为简洁，而且确定错误的实际原因通常更容易。此外，代码可读性将提高。

你可以这样写
```java
Stream.of("this", "is", "secret")
  .map(toGreek())
  .map(encrypt())
  .collect(joining(" "));
  ```
而不要这样写
```java
Stream.of("this", "is", "secret").map(toGreek()).map(encrypt()).collect(joining(" "));
```
