# 使用 Record 为不可变数据建模

Java 语言为您提供了多种方式创建不可变类。可能最直接的方法是创建一个带有 final 字段的 final 类和一个构造函数来初始化这些字段。下面是一个这样的类的例子。

```java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

现在您已经编写了这些元素，您需要为您的字段添加访问器。您还将添加一个[`toString()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#toString())方法，并且可能一起添加[`equals()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#equals(java.lang.Object))与一个[`hashCode()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#hashCode())方法。手工编写所有这些非常乏味且容易出错，幸运的是，您的 IDE 可以为您生成这些方法。

如果您需要将此类的实例从一个应用程序传送到另一个应用程序，通过网络或文件系统发送它们，您还可以考虑使此类可序列化。如果这样做，您可能需要添加一些有关如何序列化此类的实例的信息。JDK 为您提供了几种控制序列化的方法。

最后，您的`Point`类可能有 100 行长，大部分填充了由您的 IDE 生成的代码，只是为了对需要写入文件的两个整数的不可变聚合进行建模。

已经在JDK中添加了一些Recode来改变这一点。Recode只用一行代码就能提供所有这些信息。你所需要做的就是声明一份状态record;其余的由编译器生成。

## 调用Record帮组

Record在这里可以帮助您使此代码更简单。从 Java SE 14 开始，您可以编写以下代码。

```java
public record Point(int x, int y) {}
```

这行代码为您创建了以下元素。

1. 它是一个具有两个字段的不可变类：`x`and `y`，类型为`int`。
2. 它有一个规范的构造函数来初始化这两个字段。
3. [`toString()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#toString())，[`equals()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#equals(java.lang.Object))和[`hashCode()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#hashCode())方法已经为您提供了默认行为创建由编译器对应于什么样的IDE就会产生。如果需要，您可以通过添加您自己的这些方法实现来修改此行为。
4. 它可以实现[`Serializable`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/Serializable.html)接口，以便您可以`Point`通过网络或文件系统将 的实例发送到其他应用程序。record序列化和反序列化的方式遵循本教程末尾介绍的一些特殊规则。

Recode使创建不可变数据聚合变得更加简单，无需任何 IDE 的帮助。它降低了出现错误的风险，因为每次您修改Recode的组件时，编译器都会自动为您更新[`equals()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#equals(java.lang.Object))和[`hashCode()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#hashCode())方法。

## Recode类

record是用`record`关键字而不是`class`关键字声明的类。让我们宣布以下record。

```java
public record Point(int x, int y) {}
```

当您创建record时，编译器为您创建的类是最终类。

这个类扩展了[`java.lang.Record`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Record.html)类。所以你的record不能扩展任何类。

一个record可以实现任意数量的接口。

## 声明record的组成部分

紧跟在record名称之后的块是`(int x, int y)`。它声明了名为`Point`的的record*组件*。对于record的每个组件，编译器创建一个与该组件同名的私有 final 字段。您可以在record中声明任意数量的组件。

在这个例子中，编译器创建两个int类型的私有final变量`int`：`x`和`y`，对于于您已声明这两个组件。

除了这些字段，编译器为每个组件生成一个*访问*器。这个访问器是一个与组件同名的方法，并返回它的值。在这个`Point`record的情况下，两个生成的方法如下。

```java
public int x() {
    return this.x;
}

public int y() {
    return this.y;
}
```

如果此实现适用于您的应用程序，那么您无需添加任何内容。不过，您可以定义自己的访问器方法。在您需要返回特定字段的防御性副本的情况下，它可能很有用。

编译器为您生成的最后一个元素是类中[`toString()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#toString()),[`equals()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#equals(java.lang.Object))和[`hashCode()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#hashCode())方法的覆盖[`Object`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html)。如果需要，您可以定义自己的这些方法的覆盖。

## 您无法添加到record的内容

您不能将三件事添加到record中：

1. 您不能在record中声明任何实例字段。您不能添加与组件不对应的任何实例字段。
2. 您不能定义任何字段初始值设定项。
3. 您不能添加任何实例初始值设定项。

您可以使用初始值设定项和静态初始值设定项创建静态字段。

## 使用其规范构造函数构造record

编译器还会为您创建一个构造函数，称为*规范构造函数*。此构造函数将record的组件作为参数并将它们的值复制到record类的字段中。

在某些情况下，您需要覆盖此默认行为。让我们检查两个用例：

1. 您需要验证您的record状态
2. 您需要制作一个可变组件的防御性副本。

## 使用简洁构造函数

您可以使用两种不同的语法来重新定义record的规范构造函数。您可以使用简洁构造函数或标准构造函数本身。

假设您有以下record。

```java
public record Range(int start, int end) {}
```

对于该名称的record，可以预期`end`大于`start`. 您可以通过在record中编写紧凑构造函数来添加验证规则。

```java
public record Range(int start, int end) {

    public Range {
        if (end <= start) {
            throw new IllegalArgumentException("End cannot be lesser than start");
        }
    }
}
```

简洁规范构造函数不需要声明其参数块。

请注意，如果您选择此语法，则不能直接分配record的字段，例如`this.start = start`- 这是由编译器添加的代码为您完成的。但是您可以为参数分配新值，这会导致相同的结果，因为编译器生成的代码随后会将这些新值分配给字段。

```java
public Range {
    // set negative start and end to 0
    // by reassigning the compact constructor's
    // implicit parameters
    if (start < 0)
        start = 0;
    if (end < 0)
        end = 0;
}
```

复制

## 使用标准构造函数

如果您更喜欢非简洁形式，例如因为您不想重新分配参数，您可以自己定义标准构造函数，如下例所示。

```java
public record Range(int start, int end) {

    public Range(int start, int end) {
        if (end <= start) {
            throw new IllegalArgumentException("End cannot be lesser than start");
        }
        if (start < 0) {
            this.start = 0;
        } else {
            this.start = start;
        }
        if (end > 100) {
            this.end = 10;
        } else {
            this.end = end;
        }
    }
}
```

在这种情况下，您编写的构造函数需要为record的字段赋值。

如果您的record的组件不是一成不变的，您应该考虑在规范构造函数和访问器中制作它们的防御性副本。

## 定义任何构造函数

您还可以向record添加任何构造函数，只要此构造函数调用record的规范构造函数即可。语法与使用另一个构造函数调用构造函数的经典语法相同。对于任何类，调用`this()`必须是构造函数的第一条语句。

让我们检查以下`State`record。它定义在三个组件上：

1. 这个州的名字
2. 这个州的首都名称
3. 城市名称列表，可能为空。

我们需要存储一份城市列表的防御副本，以确保它不会从该record之外被修改。这可以通过使用紧凑形式重新定义规范构造函数来完成，该形式将参数重新分配给防御副本。

拥有一个不占用任何城市的构造函数在您的应用程序中很有用。这可以是另一个构造函数，它只接受州名和首都名。第二个构造函数必须调用规范构造函数。

然后，您可以将城市作为可变参数传递，而不是传递城市列表。为此，您可以创建第三个构造函数，该构造函数必须使用正确的列表调用规范构造函数。

```java
public record State(String name, String capitalCity, List<String> cities) {

    public State {
        // List.copyOf returns an unmodifiable copy,
        // so the list assigned to `cities` can't change anymore
        cities = List.copyOf(cities);
    }

    public State(String name, String capitalCity) {
        this(name, capitalCity, List.of());
    }

    public State(String name, String capitalCity, String... cities) {
        this(name, capitalCity, List.of(cities));
    }

}
```

请注意，该[`List.copyOf()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#copyOf(java.util.Collection))方法不接受它作为参数获取的集合中的空值。

## 获取record状态

您不需要向record添加任何访问器，因为编译器会为您完成这些操作。一条record的每个组件都有一个访问器方法，该方法具有该组件的名称。

`Point`本教程第一部分的record有两个访问器方法：`x()`和`y()`返回相应组件的值。

不过，在某些情况下，您需要定义自己的访问器。例如，假设`State`上一节中的record`cities`在构造期间没有创建列表的不可修改的防御副本- 那么它应该在访问器中这样做以确保调用者不能改变其内部状态。您可以在`State`record中添加以下代码以返回此防御性副本。

```java
public List<String> cities() {
    return List.copyOf(cities);
}
```

## 序列化record

如果您的record类实现了record，则record可以被序列化和反序列化[`Serializable`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/Serializable.html)。不过也有限制。

1. 您可以用来替换默认序列化过程的系统都不适用于record。创建[`writeObject()`](https://docs.oracle.com/en/java/javase/17/docs/specs/serialization/output.html#the-writeobject-method)和[`readObject()`](https://docs.oracle.com/en/java/javase/17/docs/specs/serialization/input.html#the-readobject-method)方法没有效果，也没有实现[`Externalizable`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/Externalizable.html)。
2. record可以用作代理对象来序列化其他对象。一个[`readResolve()`](https://docs.oracle.com/en/java/javase/17/docs/specs/serialization/input.html#the-readresolve-method)方法可以返回一条record。[`writeReplace()`](https://docs.oracle.com/en/java/javase/17/docs/specs/serialization/output.html#the-writereplace-method)也可以在record中添加 a 。
3. 反序列化record*总是*调用规范构造函数。因此，在反序列化record时，您可能会在此构造函数中添加的所有验证规则都将被强制执行。

这使得record成为在应用程序中创建数据传输对象的非常好的选择。

## 在真实用例中使用record

record是一个通用的概念，您可以在许多上下文中使用它。

第一个是在应用程序的对象模型中携带数据。您可以将record用于它们的设计目的：充当不可变的数据载体。

因为您可以声明本地record，所以您还可以使用它们来提高代码的可读性。

让我们考虑以下用例。您有两个建模为record的实体：`City`和`State`。

```java
public record City(String name, State state) {}
```

```java
public record State(String name) {}
```

假设您有一个城市列表，您需要计算城市数量最多的州。您可以使用 Stream API 首先构建州的直方图，其中包含每个州拥有的城市数量。该直方图由 a 建模[`Map`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Map.html)。

```java
List<City> cities = List.of();

Map<State, Long> numberOfCitiesPerState =
    cities.stream()
          .collect(Collectors.groupingBy(
                   City::state, Collectors.counting()
          ));
```

获取此直方图的最大值是以下通用代码。

```java
Map.Entry<State, Long> stateWithTheMostCities =
    numberOfCitiesPerState.entrySet().stream()
                          .max(Map.Entry.comparingByValue())
                          .orElseThrow();
```

最后一段代码是技术性的；它没有任何商业意义；因为是使用[`Map.Entry`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Map.Entry.html)实例来建模直方图的每个元素。

使用本地record可以大大改善这种情况。下面的代码创建了一个新的record类，它聚合了一个州和这个州的城市数量。它有一个构造函数，将 的实例[`Map.Entry`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Map.Entry.html)作为参数，将键值对流映射到record流。

因为需要按城市数量比较这些聚合体，所以可以添加一个工厂方法来提供这个比较器。代码变成如下。

```java
record NumberOfCitiesPerState(State state, long numberOfCities) {

    public NumberOfCitiesPerState(Map.Entry<State, Long> entry) {
        this(entry.getKey(), entry.getValue());
    }

    public static Comparator<NumberOfCitiesPerState> comparingByNumberOfCities() {
        return Comparator.comparing(NumberOfCitiesPerState::numberOfCities);
    }
}

NumberOfCitiesPerState stateWithTheMostCities =
    numberOfCitiesPerState.entrySet().stream()
                          .map(NumberOfCitiesPerState::new)
                          .max(NumberOfCitiesPerState.comparingByNumberOfCities())
                          .orElseThrow();
```

您的代码现在以有意义的方式提取最大值。您的代码更具可读性、更易于理解且不易出错，并且从长远来看更易于维护。