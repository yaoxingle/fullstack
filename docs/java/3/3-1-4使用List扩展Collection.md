## 探索List接口

[`List`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html)接口为普通集合带来了两个新功能。

- 迭代列表元素的顺序始终相同，并且遵循元素添加到此列表的顺序。
- 列表的元素有一个索引。

 

## 选择List接口的实现

虽然Collection接口在Collections框架中没有特定的实现(它依赖于其子接口的实现)，但List接口有2:ArrayList和LinkedList。 正如您可能猜到的，第一个构建在内部数组上，第二个构建在双链表上。  

这些实现中是否有一种比另一种更好? 如果您不确定选择哪一个，那么最好的选择可能是ArrayList。  

在60年代计算被发明的时候链表是正确的，但现在已经不适用了，链表在插入和删除操作上优于数组的能力被现代硬件、CPU缓存和指针追踪大大削弱了。 遍历ArrayList的元素要比遍历LinkedList的元素快得多，这主要是因为指针追逐和CPU缓存未命中。  

在某些情况下，链表仍然比数组快。 双链表可以比ArrayList更快地访问第一个和最后一个元素。 这就是LinkedList优于ArrayList的主要用例。 因此，如果您的应用程序需要后进先出(LIFO，本教程稍后将介绍)堆栈，或先进先出(FIFO，稍后将介绍)等待队列，那么选择链接列表可能是您的最佳选择。  

另一方面，如果您计划遍历列表中的元素，或根据它们的索引随机访问它们，那么ArrayList可能是最好的选择。  

 

## 使用索引访问元素

List接口为Collection接口带来了几个索引处理方法。  

### 访问单个对象

- [`add(index, element)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#add(int,E)): 在`index`处插入给定的对象，如果有剩余元素则调整索引
- [`get(index)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#get(int)): 返回给定的`index`对象 
- [`set(index, element)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#set(int,E)): 用新元素替换给定索引处的元素
- [`remove(index)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#remove(int)): 删除给定的`index`元素，调整剩余元素的索引。

调用这些方法仅适用于有效索引。如果给定的索引无效，则会引发[`IndexOutOfBoundsException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/IndexOutOfBoundsException.html)异常。

### 查找对象的索引

方法[`indexOf(element)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#indexOf(java.lang.Object))和[`lastIndexOf(element)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#lastIndexOf(java.lang.Object))返回列表中给定元素的索引，如果未找到该元素，则返回 -1。

### 获取子列表

在[`subList(start, end)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#subList(int,int))返回由索引之间的所有元素的列表`start`和`end - 1`。如果索引无效，[`IndexOutOfBoundsException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/IndexOutOfBoundsException.html)则会抛出异常。

请注意，返回的列表是主列表上的视图。因此，对子列表的任何修改操作都会反映在主列表上，反之亦然。

例如，您可以使用以下模式清除列表的一部分内容：

```java
List<String> strings = new ArrayList<>(List.of("0", "1", "2", "3", "4", "5"));
System.out.println(strings);
strings.subList(2, 5).clear();
System.out.println(strings);
```

运行此代码会得到以下结果：

```text
[0, 1, 2, 3, 4, 5]
[0, 1, 5]
```

### 插入集合

此列表的最后一个模式是关于在给定索引处插入一个集合：[`addAll(int index, Collection collection)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#addAll(int,java.util.Collection))。

 

## 对List的元素进行排序

List以已知的顺序保持其元素。这是与普通集合的主要区别。所以对列表的元素进行排序是有意义的。这就是JDK 8中List接口中添加sort()方法的原因。

在 Java SE 7 及更早版本中，您可以[`List`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html)通过调用[`Collections.sort()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collections.html#sort(java.util.List))和传递您的列表作为参数来对您的元素进行排序，如果需要，还可以使用比较器。

从Java SE 8开始，您可以直接在列表中调用sort()，并将比较器作为参数传递。 这个方法没有不带任何参数的重载。 用空比较器调用它会假设你的List的元素实现了Comparable，如果不是这样，你会得到一个ClassCastException。  

如果您不喜欢调用方法将参数为空(您是对的!)，您仍然可以使用Comparator.naturalOrder()调用它来实现相同的结果。  

## 迭代List元素

List接口为您提供了另一种使用ListIterator迭代其元素的方法。 您可以通过调用listIterator()来获得这样的迭代器。 你可以不带参数调用这个方法，或者传递一个整数索引给它。 在这种情况下，迭代将从这个索引开始。  

该[`ListIterator`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ListIterator.html)界面扩展了[`Iterator`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Iterator.html)您已经知道的常规。它添加了几种方法。

- [`hasPrevious()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ListIterator.html#hasPrevious())and [`previous()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ListIterator.html#previous()): 按降序而不是升序迭代
- [`nextIndex()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ListIterator.html#nextIndex())and [`previousIndex()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ListIterator.html#previousIndex()): 返回元素的索引
- [`set(element)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ListIterator.html#set(E)): 更新next()或previous()返回的最后一个元素。 如果这两个方法都没有在该迭代器上调用，则会引发IllegalStateException异常。  

让我们看看这个[`set()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ListIterator.html#set(E))方法的实际效果：

```java
List<String> numbers = Arrays.asList("one", "two", "three");
for (ListIterator<String> iterator = numbers.listIterator(); iterator.hasNext();) {
    String nextElement = iterator.next();
    if (Objects.equals(nextElement, "two")) {
        iterator.set("2");
    }
}
System.out.println("numbers = " + numbers);
```

运行此代码将为您提供以下结果：

```text
numbers = [one, 2, three]
```