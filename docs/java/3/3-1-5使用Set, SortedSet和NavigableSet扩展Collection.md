## 探索 Set 接口

Set接口没有为Collection接口带来任何新方法。 集合框架为您提供了Set接口的一个普通实现:HashSet。 在内部，HashSet包装了作为HashSet委托的HashMap实例，稍后将介绍这个类。  

正如您已经看到的，Set给Collection带来的是它禁止重复。 在List接口上失去的是元素没有按照特定的顺序存储。 当您将它们添加到集合中时，您将以相同的顺序遍历它们的可能性非常小。  

您可以在以下示例中看到这一点：

```java
List<String> strings = List.of("one", "two", "three", "four", "five", "six");
Set<String> set = new HashSet<>();
set.addAll(strings);
set.forEach(System.out::println);
```

运行此代码将产生以下结果：

```text
six
four
one
two
three
five
```

 当迭代它们的元素时，Set的一些实现给出了相同的顺序，但由于不能保证这一点，您的代码不应该依赖于它。  

## 使用 SortedSet 扩展Set

Set的第一个扩展是SortedSet接口。 SortedSet接口按照某种比较逻辑对其元素进行排序。 集合框架为您提供了SortedSet的一个实现，称为TreeSet。  

正如您已经看到的，要么在构建TreeSet时提供比较器，要么为放在TreeSet中的元素实现Comparable接口。 如果两者都做了，那么比较器优先。  

SortedSet接口为Set添加了新方法。  

- [`first()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/TreeSet.html#first())和[`last()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/TreeSet.html#last())返回集合中最小和最大的元素
- [`headSet(toElement)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/TreeSet.html#headSet(E))并[`tailSet(fromElement)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/TreeSet.html#tailSet(E))返回包含小于`toElement`或大于`fromElement`元素的子集
- [`subSet(fromElement, toElement)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/TreeSet.html#subSet(E,E))为您提供`fromElement`和`toElement`之间的元素子集。

toElement和fromelelement不一定是主集合的元素。 如果它们是，那么toElement不包含在结果中，而fromelelement是，遵循通常的约定。  

考虑以下示例：

```java
SortedSet<String> strings = new TreeSet<>(Set.of("a", "b", "c", "d", "e", "f"));
SortedSet<String> subSet = strings.subSet("aa", "d");
System.out.println("sub set = " + subSet);
```

运行此代码将为您提供以下信息：

```text
sub set = [b, c]
```

这些方法返回的三个子集是主集合上的视图。 没有复制，这意味着您对这些子集所做的任何更改都将反映在集合中，反之亦然。  

您可以通过这些子集向主集中删除或添加元素。 但是有一点你需要记住。 这三个子集记住了它们所建立的限制。 出于一致性的原因，通过超出其限制的子集添加元素是不合法的。 例如，如果你使用headSet并试图添加一个大于toElement的元素，那么你将得到一个IllegalArgumentException。  

 

## 使用 NavigableSet 扩展 SortedSet

Java SE 6引入了SortedSet的扩展，并添加了更多的方法。 结果是，TreeSet类被改造为实现NavigableSet。 因此，您可以为这两个接口使用同一个类。  

NavigableSet重载了一些方法。

- [`headSet()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#headSet(E)), [`headSet()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#tailSet(E)), 和[`headSet()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#subSet(E))可能需要进一步的`boolean`参数来指定是否将限制（`toElement`或`fromElement`）包含在结果子集中。

添加了其他方法。

- [`ceiling(element)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#ceiling(E))，和[`floor(element)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#floor(E))返回小于或等于提供的最大元素，或大于或等于提供的最小元素`element`。如果没有这样的元素，则`null`返回
- [`floor(element)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#lower(E))，和[`floor(element)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#higher(E))返回小于或大于所提供的最小元素`element`。如果没有这样的元素，则`null`返回。
- [`pollFirst()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#pollFirst()),和[`pollLast()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#pollLast())返回并删除集合的最低或最大元素。

此外，[`NavigableSet`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html)还允许您按降序迭代其元素。有两种方法可以做到这一点。

- 您可以调用[`descendingIterator()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html#descendingIterator()): 它为您提供一个[`Iterator`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Iterator.html)按降序遍历集合的正则。
- 您也可以调用descendingSet()。你得到的是另一个NavigableSet，那是这个集合上的一个视图，这让你认为你有相同的集合，以相反的顺序排序。

以下示例演示了这一点。

```java
NavigableSet<String> sortedStrings = new TreeSet<>(Set.of("a", "b", "c", "d", "e", "f"));
System.out.println("sorted strings = " + sortedStrings);
NavigableSet<String> reversedStrings = sortedStrings.descendingSet();
System.out.println("reversed strings = " + reversedStrings);
```

运行此代码将为您提供以下信息：

```text
sorted strings = [a, b, c, d, e, f]
reversed strings = [f, e, d, c, b, a]
```