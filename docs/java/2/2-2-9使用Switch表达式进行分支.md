## 修改Switch语法

在 Java SE 14 中，您可以对`switch`关键字使用另一种更方便的语法：`switch`表达式。

有几件事激发了这种新语法。

1. switch标签之间的默认控制流行为是失败。这种语法容易出错，并会导致应用程序出现错误。
2. `switch`块被视为一个块。在只需要在一种特定情况下定义变量的情况下，这可能是一个障碍。
3. `switch`语句是一个语句。在前几节的示例中，变量在每个`case`中都被赋予了一个值。使它成为一个表达式可能会导致更好、更易读的代码。

上一节中介绍的语法，称为*switch 语句，*在 Java SE 14 中仍然可用，其语义没有改变。从 Java SE 14 开始，可以使用新的语法`switch`：*switch表达式*。

此语法修改switch标签的语法。假设您的应用程序中有以下*switch 语句*。

```java
int day = ...; // any day
int len = 0;
switch (day) {
    case MONDAY:
    case FRIDAY:
    case SUNDAY:
        len = 6;
        break;
    case TUESDAY:
        len = 7;
        break;
    case THURSDAY:
    case SATURDAY:
        len = 8;
        break;
    case WEDNESDAY:
        len = 9;
        break;
}
System.out.println("len = " + len);
```

使用*switch 表达式*语法，您现在可以按以下方式编写它。

```java
int day = ...; // any day
int len =
    switch (day) {
        case MONDAY, FRIDAY, SUNDAY -> System.out.println(6);
        case TUESDAY                -> System.out.println(7);
        case THURSDAY, SATURDAY     -> System.out.println(8);
        case WEDNESDAY              -> System.out.println(9);
    }
System.out.println("len = " + len);
```

switch标签的语法现在是`case L ->`. 如果标签匹配，则仅执行标签右侧的代码。此代码可以是单个表达式、块或 throw 语句。由于此代码是一个块，因此您可以在其中定义此特定块的本地变量。

此语法还支持每个 case 的多个常量，以逗号分隔，如前面的示例所示。

 

## 产生一个值

此 switch 语句可用作表达式。例如，可以通过以下方式使用 switch 语句重写上一节的示例。

```java
int quarter = ...; // any value

String quarterLabel =
    switch (quarter) {
        case 0  -> "Q1 - Winter";
        case 1  -> "Q2 - Spring";
        case 2  -> "Q3 - Summer";
        case 3  -> "Q3 - Summer";
        default -> "Unknown quarter";
    };
```

如果`case`块中只有一个语句，则该语句产生的值由`switch`表达式返回。

代码块的语法略有不同。传统上，`return`关键字用于表示代码块产生的值。不幸的是，这种语法在 switch 语句的情况下会导致歧义。让我们考虑以下示例。这段代码不能编译，它只是作为一个例子。

```java
// Be careful, this code does NOT compile!
public String convertToLabel(int quarter) {
    String quarterLabel =
        switch (quarter) {
            case 0  -> {
                System.out.println("Q1 - Winter");
                return "Q1 - Winter";
            };
            default -> "Unknown quarter";
        };
    }
    return quarterLabel;
}
```

在`quarter`等于 0的情况下执行的代码块需要返回一个值。它使用`return`关键字来表示这个值。如果仔细查看这段代码，您会发现有两个`return`语句：一个在`case`块中，另一个在方法块中。这就是歧义所在：人们可能想知道第一个的语义是什么`return`。是不是意味着程序用这个值退出了方法？或者它离开了`switch`声明？这种歧义导致可读性差和容易出错的代码。

已经创建了一种新语法来解决这种歧义：`yield`语句。前面例子的代码应该是这样写的。

```java
public String convertToLabel(int quarter) {
    String quarterLabel =
        switch (quarter) {
            case 0  -> {
                System.out.println("Q1 - Winter");
                yield "Q1 - Winter";
            };
            default -> "Unknown quarter";
        };
    }
    return quarterLabel;
}
```

该`yield`声明是可以在任何可以使用的语句`case`一个块`switch`声明。它带有一个值，该值成为封闭`switch`语句的值。

 

## 添加默认条款

默认子句允许您的代码处理选择器值与任何`case`常量都不匹配的情况。

switch 表达式的情况必须是详尽无遗的。对于所有可能的值，必须有一个匹配的开关标签。Switch 语句不需要详尽无遗。如果选择器目标不匹配任何 switch 标签，这个 switch 语句将不会做任何事情，默默地。这可能是您的应用程序中隐藏错误的地方，这是您想要避免的。

在大多数情况下，可以使用`default`子句实现详尽无遗；但是，如果`enum` `switch`表达式涵盖所有已知常量，则不需要添加此`default`子句。

还有一个案件需要处理。如果有人在枚举中添加枚举值，但忘记更新此枚举上的 switch 语句，会发生什么？为了处理这种情况，编译器会`default`在详尽的 switch 语句中为您添加一个子句。这个`default`子句在正常情况下永远不会被执行。只有添加了枚举值，才会抛出`IncompatibleClassChangeError`.

处理穷举是`switch`表达式的一个特性，传统`switch`语句没有提供它，它用于`switch`枚举值以外的其他情况。

 

## 在 Switch 表达式中编写冒号

`switch`表达式也可以使用一个传统的`case`用块`case L:`。在这种情况下，fall through 语义确实适用。使用该`yield`语句产生值。

```java
int quarter = ...; // any value

String quarterLabel =
    switch (quarter) {
        case 0 :  yield "Q1 - Winter";
        case 1 :  yield "Q2 - Spring";
        case 2 :  yield "Q3 - Summer";
        case 3 :  yield "Q3 - Summer";
        default: System.out.println("Unknown quarter");
                 yield "Unknown quarter";
    };
```

 

## 处理空值

到目前为止，`switch`语句不接受空选择器值。如果您尝试`switch`使用空值，您将获得一个`NullPointerException`.

Java SE 17 具有增强`switch`表达式以允许空值的预览功能，因此您可以预期这种情况会有所改变。