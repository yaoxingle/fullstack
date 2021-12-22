## 来自Object类的方法

java.lang包中的Object类。位于类层次结构树的顶部。 每个类都是Object类的直接或间接的后代。 您使用或编写的每个类都继承Object实例的方法。 您不需要使用这些方法中的任何一个，但是，如果您选择这样做，您可能需要使用特定于您的类的代码重写它们。 本节中讨论的从Object继承的方法有:  

- [`protected Object clone() throws CloneNotSupportedException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#clone()): 创建并返回此对象的副本。
- [`public boolean equals(Object obj)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#equals(java.lang.Object)): 表示其他某个对象是否“等于”这个对象。
- [`protected void finalize() throws Throwable`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#finalize()): 当垃圾收集器确定不再有对该对象的引用时，由垃圾收集器在该对象上调用
- [`public final Class getClass()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#getClass()): 返回对象的运行时类。
- [`public int hashCode()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#hashCode())：返回对象的哈希码值。
- [`public String toString()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#toString()): 返回对象的字符串表示形式。

请注意，从 Java SE 9 开始，该[`finalize()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#finalize())方法已被弃用。强烈建议不要覆盖此方法。

Object的notify()、notifyAll()和wait()方法都在同步程序中独立运行的线程的活动中发挥作用，这将在后面的小节中讨论，这里不再讨论。 其中有五种方法:  

- [`public final void notify()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#notify())
- [`public final void notifyAll()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#notifyAll())
- [`public final void wait()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#wait())
- [`public final void wait(long timeout)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#wait(long))
- [`public final void wait(long timeout, int nanos)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#wait(long,int))

> 注意：许多这些方法有一些微妙的方面，尤其是克隆方法。

 

## toString() 方法

您应该始终考虑重写类中的toString()方法。  

Object的toString()方法返回对象的String表示形式，这对于调试非常有用。 对象的String表示完全依赖于对象，这就是为什么您需要在类中重写toString()。  

你可以使用toString()和System.out.println()来显示一个对象的文本表示，比如Book的实例:  

```java
System.out.println(firstBook.toString());
```

对于正确覆盖的[`toString()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#toString())方法，它将打印一些有用的东西，如下所示：

```java
ISBN: 0201914670; The Swing Tutorial; A Guide to Constructing GUIs, 2nd Edition
```



## equals() 方法

equals()方法比较两个对象是否相等，如果相等则返回true。 Object类中提供的equals()方法使用标识操作符(==)来确定两个对象是否相等。 对于原始数据类型，这将给出正确的结果。 然而，对于对象来说，则不是这样。 Object提供的equals()方法测试对象引用是否相等——也就是说，比较的对象是否完全是同一个对象。  

要测试两个对象是否等价(包含相同的信息)，必须重写equals()方法。 下面是一个Book类重写equals()的例子:  

```java
public class Book {
    String ISBN;
    
    public String getISBN() { 
        return ISBN;
    }
    
    public boolean equals(Object obj) {
        if (obj instanceof Book)
            return ISBN.equals((Book)obj.getISBN()); 
        else
            return false;
    }
}
```

考虑这个测试`Book`类的两个实例是否相等的代码：

```java
// Swing Tutorial, 2nd edition
Book firstBook  = new Book("0201914670");
Book secondBook = new Book("0201914670");
if (firstBook.equals(secondBook)) {
    System.out.println("objects are equal");
} else {
    System.out.println("objects are not equal");
}
```

该程序显示对象是相等的，即使`firstBook`并`secondBook`引用两个不同的对象。它们被认为是相等的，因为比较的对象包含相同的 ISBN 号。

如果标识操作符不适合您的类，则应该始终重写equals()方法。  

> 注意：如果您覆盖[`equals()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#equals(java.lang.Object))，您也必须覆盖[`hashCode()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#hashCode())。

 

## hashCode() 方法

hashCode()返回的值是对象的哈希码，这是一个由哈希算法生成的整数值。  

根据定义，如果两个对象相等，它们的哈希码也必须相等。 如果你重写equals()方法，你改变了两个对象相等的方式，对象的hashCode()实现不再有效。 因此，如果重写equals()方法，还必须重写hashCode()方法。  

 

## getClass() 方法

你不能重写getClass()。  

如果一个类或它的一个超类实现了Cloneable接口，您可以使用clone()方法从现有对象创建一个副本。 要创建一个克隆，你可以这样写:  

```java
void printClassName(Object obj) {
    System.out.println("The object's" + " class is " +
        obj.getClass().getSimpleName());
}
```

java.lang包中的Class类。 有大量的方法(50多个)。 例如，您可以测试该类是否是注释(isAnnotation())、接口(isInterface())或枚举(isEnum())。 您可以看到对象的字段是什么(getFields())或它的方法是什么(getMethods())，等等。  

## clone() 方法

如果一个类或其超类之一实现了该[`Cloneable`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Cloneable.html)接口，则可以使用该[`clone()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#clone())方法从现有对象创建副本。要创建克隆，请编写：

```java
aCloneableObject.clone();
```

对象对该方法的实现检查是否调用clone()的对象实现了Cloneable接口。 如果对象没有，该方法抛出一个CloneNotSupportedException异常。 异常处理将在异常一节中介绍。 目前，您需要知道必须将clone()声明为  

```java
protected Object clone() throws CloneNotSupportedException
```

或者

```java
public Object clone() throws CloneNotSupportedException
```

如果你要写一个clone()方法来覆盖Object中的方法。  

如果调用clone()的对象实现了Cloneable接口，那么object对clone()方法的实现将创建一个与原始对象相同类的对象，并初始化新对象的成员变量，使其与原始对象的相应成员变量具有相同的值。  

使你的类可克隆的最简单的方法是在你的类声明中添加实现cloneable。 然后您的对象可以调用clone()方法。  

对于某些类，Object的clone()方法的默认行为可以很好地工作。 然而，如果一个对象包含一个外部对象的引用，比如ObjExternal，你可能需要重写clone()来获得正确的行为。 否则，一个对象在ObjExternal中所做的改变也将在它的克隆中可见。 这意味着原始对象和它的克隆对象不是独立的——要解耦它们，你必须覆盖clone()，这样它才能克隆对象和ObjExternal。 然后原始对象引用ObjExternal，克隆对象引用一个ObjExternal的克隆对象，这样这个对象和它的克隆对象是真正独立的。  

 

## finalize() 方法

Object类提供了一个回调方法finalize()，当对象变成垃圾时可以调用它。 对象的finalize()实现什么也不做—您可以重写finalize()来执行清理，比如释放资源。  

finalize()方法可以由系统自动调用，但是在调用它的时候，或者即使调用它，都是不确定的。 因此，您不应该依赖此方法为您进行清理。 例如，如果您在执行I/O后没有在代码中关闭文件描述符，而您希望finalize()为您关闭它们，则可能会耗尽文件描述符。  

从Java SE 9开始，finalize()方法已被弃用。 现在强烈不建议重写此方法。 如果你需要清理一些资源，你可以通过实现AutoCloseable接口来完成。 这一点将在Java I/O一节中详细讨论。  