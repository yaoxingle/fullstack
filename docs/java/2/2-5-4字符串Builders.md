## StringBuilder 类

[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)对象与[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)对象类似，只是它们可以被修改。在内部，这些对象被视为包含字符序列的可变长度数组。在任何时候，序列的长度和内容都可以通过方法调用来改变。

除非字符串构建器在更简单的代码（参见本节末尾的示例程序）或更好的性能方面提供优势，否则应始终使用字符串。在 Java SE 9 之前，如果您需要连接大量字符串，附加到 StringBuilder 对象可能更有效。字符串连接已在 Java SE 9 中进行了优化，使连接比[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)附加更高效。

 

## 长度和容量

[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)类，如[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)类，具有[`length()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#length())返回在构建器的字符序列的长度的方法。

与字符串不同，每个字符串生成器也有一个容量，即已分配的字符空间数。[`capacity()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#capacity())方法返回的容量总是大于或等于长度（通常大于），并且会根据需要自动扩展以适应字符串生成器的添加。

您可以使用[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)该类的以下构造函数：

- [`StringBuilder()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#())：创建一个容量为 16（16 个空元素）的空字符串构建器。
- [`StringBuilder(CharSequence cs)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#(java.lang.CharSequence)): 构造一个字符串builder，其中包含与指定的字符相同的字符[`CharSequence`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/CharSequence.html)，外加[`CharSequence`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/CharSequence.html).
- [`StringBuilder(int initCapacity)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#(int))：创建一个具有指定初始容量的空字符串构建器。
- [`StringBuilder(String s)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#(java.lang.String))：创建一个字符串builder，其值由指定的字符串初始化，加上字符串后面的 16 个额外的空元素。

例如，下面的代码

```java
// creates empty builder, capacity 16
StringBuilder sb = new StringBuilder();
// adds 9 character string at beginning
sb.append("Greetings");
```

将产生一个长度为 9、容量为 16 的字符串生成器：

![`StringBuilder` 的长度和容量](2-5-4字符串 Builders.assets/04_stringbuilder.png)

`StringBuilder` 的长度和容量

该[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)类有相关的长度和容量的一些方法[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)类没有：

- [`void setLength(int newLength)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#setLength(int)): 设置字符序列的长度。如果`newLength`小于[`length()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#length())，则字符序列中的最后一个字符将被截断。如果`newLength`大于[`length()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#length())，`null`则在字符序列的末尾添加字符。
- [`void ensureCapacity(int minCapacity)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#ensureCapacity(int))：确保容量至少等于指定的最小值。

许多操作（例如，[`append()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#append(java.lang.Object))、[`insert()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#insert(int,java.lang.Object))、 或[`setLength()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#setLength(int))）可以增加字符串构建器中字符序列的长度，以便结果[`length()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#length())大于当前的[`capacity()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#capacity())。发生这种情况时，容量会自动增加。

 

## StringBuilder操作

StringBuilder上的主要操作在String中不可用，它们是append()和insert()方法，它们是重载的，以便接受任何类型的数据。 每个参数都将其参数转换为一个字符串，然后将该字符串的字符追加或插入到字符串构建器中的字符序列中。 append方法总是将这些字符添加到现有字符序列的末尾，而insert方法则将这些字符添加到指定的点。  

以下是[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)该类的一些方法。

- 您可以使用方法将任何原始类型或对象附加到字符串构建器[`append()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#append(java.lang.Object))。在执行追加操作之前，数据将转换为字符串。
- 该[`delete(int start, int end)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#delete(int,int))方法删除的 char 序列中from `start`to `end - 1`（包括）[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)的子序列。
- 您可以使用方法删除`char`at 索引。`index`[`deleteCharAt(int index)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#deleteCharAt(int))
- 您可以`offset`使用其中一种[`insert(int offset)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#insert(int,java.lang.Object))方法在给定位置插入任何原始类型或对象。这些方法将要插入的元素作为第二个参数。在插入操作发生之前，数据被转换为字符串。
- 您可以使用方法[`replace(int start, int end, String s)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#replace(int,int,java.lang.String))和替换字符[`setCharAt(int index, char c)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#setCharAt(int,char))。
- 您可以使用 方法反转此字符串生成器中的字符序列[`reverse()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#reverse())。
- 您可以使用该[`toString()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#toString())方法在构建器中返回一个包含字符序列的字符串。

> 注意：您可以使用任何[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)方法上[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)由第一字符串生成器与转换为字符串对象[`toString()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#toString())的方法[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)类。然后使用[`StringBuilder(String str)`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#(java.lang.String))构造函数将字符串转换回字符串构建器。

 

## StringBuilder 在行动

`StringDemo`在标题为“字符串”的部分中列出的程序是一个程序示例，如果使用 a[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)而不是 a会更有效[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)。

`StringDemo`反转回文。这里再一次是它的清单：

```java
public class StringDemo {
    public static void main(String[] args) {
        String palindrome = "Dot saw I was Tod";
        int len = palindrome.length();
        char[] tempCharArray = new char[len];
        char[] charArray = new char[len];
        
        // put original string in an 
        // array of chars
        for (int i = 0; i < len; i++) {
            tempCharArray[i] = 
                palindrome.charAt(i);
        } 
        
        // reverse array of chars
        for (int j = 0; j < len; j++) {
            charArray[j] =
                tempCharArray[len - 1 - j];
        }
        
        String reversePalindrome =
            new String(charArray);
        System.out.println(reversePalindrome);
    }
}
```



运行程序会产生以下输出：

```shell
doT saw I was toD
```



为了完成字符串反转，程序将字符串转换为字符数组（第一次`for`循环），将数组反转为第二个数组（第二次`for`循环），然后再转换回字符串。

如果将回文字符串转换为字符串生成器，则可以使用类中的[`reverse()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html#reverse())方法[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)。它使代码更简单，更易于阅读：

```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        String palindrome = "Dot saw I was Tod";
         
        StringBuilder sb = new StringBuilder(palindrome);
        
        sb.reverse();  // reverse it
        
        System.out.println(sb);
    }
}
```



运行这个程序会产生相同的输出：

```shell
doT saw I was toD
```



请注意，[`println()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/PrintStream.html#println(int))打印字符串生成器，如下所示：

```java
System.out.println(sb);
```



因为`sb.toString()`被隐式调用，就像[`println`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/PrintStream.html#println(int))调用中的任何其他对象一样。

> 注意：还有一个与[`StringBuffer`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuffer.html)该类完全相同的[`StringBuilder`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)类，只是它的方法是同步的，因此是线程安全的。除非你绝对需要一个线程安全的类，否则你不需要使用[`StringBuffer`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuffer.html).