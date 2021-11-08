## 使用 Switch 语句控制程序流程

该`switch`语句是 Java 语言中可用的五个控制流语句之一。它允许任意数量的执行路径。`switch`语句接受选择器变量作为参数，并使用该变量的值来选择将要执行的路径。

您必须在以下类型中选择选择器变量的类型：

- `byte`、`short`、`char`和`int`原始数据类型
- `Character`, `Byte`, `Short`, 和`Integer`包装器类型
- 枚举类型
- `String`类型。

值得注意的是，下面的基本类型不能用于您选择器变量类型：`boolean`，`long`，`float`，和`double`。

让我们看一个`switch`语句的第一个例子。

```java
int quarter = ...; // any value

String quarterLabel = null;
switch (quarter) {
    case 0: quarterLabel = "Q1 - Winter"; 
            break;
    case 1: quarterLabel = "Q2 - Spring"; 
            break;
    case 2: quarterLabel = "Q3 - Summer"; 
            break;
    case 3: quarterLabel = "Q3 - Summer"; 
            break;
    default: quarterLabel = "Unknown quarter";
};
```

`switch`语句的主体称为`switch`块。在声明`switch`块可以与一个或多个标记`case`或`default`标签。该`switch`语句计算其表达式，然后执行匹配`case`标签后面的所有语句。

您可能已经注意到`break`关键字的使用。每个`break`语句都终止封闭`switch`语句。控制流从`switch`块后面的第一个语句继续。这些`break`语句是必要的，因为没有它们，`switch`块中的语句就会失败。匹配`case`标签之后的所有语句都会依次执行，而不管后续`case`标签的表达式，直到`break`遇到一个语句。

以下代码使用 fall through 来填充`futureMonths`列表。

```java
int month = 8;
List<String> futureMonths = new ArrayList<>();

switch (month) {
    case 1:  futureMonths.add("January");
    case 2:  futureMonths.add("February");
    case 3:  futureMonths.add("March");
    case 4:  futureMonths.add("April");
    case 5:  futureMonths.add("May");
    case 6:  futureMonths.add("June");
    case 7:  futureMonths.add("July");
    case 8:  futureMonths.add("August");
    case 9:  futureMonths.add("September");
    case 10: futureMonths.add("October");
    case 11: futureMonths.add("November");
    case 12: futureMonths.add("December");
             break;
    default: break;
}
```

从技术上讲，最后的`break`不是必需的，因为 flow falls 在`switch`语句中退出。建议使用`break`以便修改代码更容易且不易出错。

`default`部分处理未由其中一个`case`部分明确处理的所有值。

下面的代码示例展示了一个语句如何可以有多个`case`标签。代码示例计算特定月份的天数：

```java
int month = 2;
int year = 2021;
int numDays = 0;

switch (month) {
    case 1: case 3: case 5:   // January March May
    case 7: case 8: case 10:  // July August October
    case 12:
        numDays = 31;
        break;
    case 4: case 6:   // April June
    case 9: case 11:  // September November
        numDays = 30;
        break;
    case 2: // February
        if (((year % 4 == 0) && 
             !(year % 100 == 0))
             || (year % 400 == 0))
            numDays = 29;
        else
            numDays = 28;
        break;
    default:
        System.out.println("Invalid month.");
        break;
}
```

这段代码有一条用于多个情况的`case`语句。

 

## 在 Switch 语句和 If-then-else 语句之间进行选择

决定是使用`if-then-else`语句还是`switch`语句是基于可读性和语句正在测试的表达式。一个`if-then-else`语句可以基于值或条件的范围测试表达式，而`switch`语句测试仅基于单个整数，枚举值或表达式`String`对象。

例如，可以使用`switch`语句编写以下代码。

```java
int month = ...; // any month
if (month == 1) {
    System.out.println("January");
} else if (month == 2) {
    System.out.println("February");
} ... // and so on
```

另一方面，以下内容不能用`switch`语句编写，因为`switch`语句不支持 type 标签`boolean`。

```java
int temperature = ...; // any temperature
if (temperature < 0) {
    System.out.println("Water is ice");
} else if (temperature < 100){
    System.out.println("Water is liquid, known as water");
} else {
    System.out.println("Water is vapor");
}
```

 

## 使用字符串作为Case标签的类型

在 Java SE 7 及更高版本中，您可以`String`在`switch`语句的表达式中使用对象。以下代码示例根据`String`指定月份的值显示月份数。

```java
String month = ...; // any month
int monthNumber = -1;

switch (month.toLowerCase()) {
    case "january":
        monthNumber = 1;
        break;
    case "february":
        monthNumber = 2;
        break;
    case "march":
        monthNumber = 3;
        break;
    case "april":
        monthNumber = 4;
        break;
    case "may":
        monthNumber = 5;
        break;
    case "june":
        monthNumber = 6;
        break;
    case "july":
        monthNumber = 7;
        break;
    case "august":
        monthNumber = 8;
        break;
    case "september":
        monthNumber = 9;
        break;
    case "october":
        monthNumber = 10;
        break;
    case "november":
        monthNumber = 11;
        break;
    case "december":
        monthNumber = 12;
        break;
    default: 
        monthNumber = 0;
        break;
}
```

将表达式中的 与与每个标签关联`String`的`switch`表达式进行比较，`case`就好像`String.equals()`正在使用该方法一样。为了让这个例子接受任何月份而不考虑大小写，月份被转换为小写（使用`toLowerCase()`方法），并且所有与`case`标签关联的字符串都是小写的。

 

## 空选择器变量

一个`switch`语句的选择器变量可以是一个对象，所以这个对象可以为空。您应该保护您的代码免受空选择器变量的影响，因为在这种情况下，switch 语句将抛出一个`NullPointerException`.