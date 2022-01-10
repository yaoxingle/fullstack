# Unchecked Exceptions ——争议

因为Java编程语言不需要方法来捕获或指定未经检查的异常(RuntimeException、Error及其子类)，程序员可能会倾向于编写只抛出未经检查的异常的代码，或者让所有的异常子类都继承自RuntimeException。这两种快捷方式都允许程序员在编写代码时不用担心编译器错误，也不用费心去指定或捕获任何异常。虽然这对程序员来说似乎很方便，但它回避了catch的意图或指定了需求，并且可能会给其他使用您的类的人带来问题。

为什么设计人员决定强制一个方法指定所有在其作用域内可以抛出的uncaught checked 异常?方法可以抛出的任何异常都是该方法公共编程接口的一部分。调用方法的人必须知道方法可能抛出的异常，这样他们才能决定如何处理这些异常。这些异常与方法的参数和返回值一样，都是该方法编程接口的一部分。

下一个问题可能是:“如果能更好的文档化方法的API，包括它可能抛出的异常，那么为什么不也指定运行时异常呢?”运行时异常表示由编程问题导致的问题，因此，不能合理地期望API客户机代码从这些问题中恢复或以任何方式处理它们。这类问题包括算术异常，如除零;指针异常，例如试图通过空引用访问对象;还有索引异常，例如试图通过索引过大或过小来访问数组元素。

运行时异常可能发生在程序的任何地方，在典型的异常中，异常可能非常多。必须在每个方法声明中添加运行时异常会降低程序的清晰度。因此，编译器不需要您捕获或指定运行时异常（尽管您可以）。

当用户错误地调用方法时，通常会抛出RuntimeException。例如，一个方法可以检查它的一个参数是否不正确地为空。如果参数为空，该方法可能会抛出NullPointerException，这是一个未检查的异常。

一般来说，不要抛出RuntimeException或创建RuntimeException的子类，因为您不想麻烦地指定您的方法可以抛出的异常。

以下是底线准则:如果客户端可以合理地预期从异常中恢复，那么将其设置为checked 异常。如果客户端无法从异常中恢复，则将其设置为未检查异常。

 

## 异常的有点

现在您知道什么是异常以及如何使用它们，是时候了解在您的程序中使用异常的有点了。

### 优势 1：将错误处理代码与“常规”代码分开

异常提供了将异常情况发生时的处理细节与程序的主要逻辑分开的方法。在传统的编程中，错误检测、报告和处理通常会导致意大利面条式代码的混乱。例如，考虑这里将整个文件读入内存的伪代码方法。

```java
readFile {
    open the file;
    determine its size;
    allocate that much memory;
    read the file into memory;
    close the file;
}
```



乍一看，这个函数似乎很简单，但它忽略了以下所有潜在的错误。

- 如果无法打开文件会怎样？
- 如果无法确定文件的长度会怎样？
- 如果无法分配足够的内存会怎样？
- 如果读取失败会发生什么？
- 如果无法关闭文件会怎样？

为了处理这种情况，`readFile`函数必须有更多的代码来进行错误检测、报告和处理。下面是该函数可能是什么样子的示例。

```java
errorCodeType readFile {
    initialize errorCode = 0;
    
    open the file;
    if (theFileIsOpen) {
        determine the length of the file;
        if (gotTheFileLength) {
            allocate that much memory;
            if (gotEnoughMemory) {
                read the file into memory;
                if (readFailed) {
                    errorCode = -1;
                }
            } else {
                errorCode = -2;
            }
        } else {
            errorCode = -3;
        }
        close the file;
        if (theFileDidntClose && errorCode == 0) {
            errorCode = -4;
        } else {
            errorCode = errorCode and -4;
        }
    } else {
        errorCode = -5;
    }
    return errorCode;
}
```

这里有太多的错误检测、报告和返回，原来的七行代码在混乱中丢失了。更糟糕的是，代码的逻辑流程也丢失了，因此很难判断代码是否在做正确的事情：如果函数未能分配足够的内存，文件是否真的被关闭了？在编写方法三个月后修改方法时，更难确保代码继续做正确的事情。许多程序员通过简单地忽略它来解决这个问题——当他们的程序崩溃时会报告错误。

异常使您能够编写代码的主要流程并处理其他地方的异常情况。如果`readFile`函数使用异常而不是传统的错误管理技术，它看起来更像下面这样。

```java
readFile {
    try {
        open the file;
        determine its size;
        allocate that much memory;
        read the file into memory;
        close the file;
    } catch (fileOpenFailed) {
       doSomething;
    } catch (sizeDeterminationFailed) {
        doSomething;
    } catch (memoryAllocationFailed) {
        doSomething;
    } catch (readFailed) {
        doSomething;
    } catch (fileCloseFailed) {
        doSomething;
    }
}
```

请注意，异常不会让您省去检测、报告和处理错误的工作，但它们确实可以帮助您更有效地组织工作。

### 优势 2：在调用堆栈中向上传播错误

异常的第二个优点是能够将错误报告向上传播到方法的调用堆栈。假设该`readFile`方法是主程序进行的一系列嵌套方法调用中的第四个方法：`method1`调用`method2`、调用`method3`、最终调用`readFile`。

```java
method1 {
    call method2;
}

method2 {
    call method3;
}

method3 {
    call readFile;
}
```

还假设这`method1`是唯一对 中可能发生的错误感兴趣的方法`readFile`。传统的错误通知技术强制`method2`并`method3`传播`readFile`向上调用堆栈返回的错误代码，直到错误代码最终到达`method1`——这是唯一对它们感兴趣的方法。

```java
method1 {
    errorCodeType error;
    error = call method2;
    if (error)
        doErrorProcessing;
    else
        proceed;
}

errorCodeType method2 {
    errorCodeType error;
    error = call method3;
    if (error)
        return error;
    else
        proceed;
}

errorCodeType method3 {
    errorCodeType error;
    error = call readFile;
    if (error)
        return error;
    else
        proceed;
}
```

回想一下，Java 运行时环境通过调用堆栈向后搜索以查找对处理特定异常感兴趣的任何方法。一个方法可以躲避其中抛出的任何异常，从而允许调用堆栈更远的方法来捕获它。因此，只有关心错误的方法才需要担心检测错误。

```java
method1 {
    try {
        call method2;
    } catch (exception e) {
        doErrorProcessing;
    }
}

method2 throws exception {
    call method3;
}

method3 throws exception {
    call readFile;
}
```

然而，正如伪代码所示，躲避异常需要中间人方法方面的一些努力。任何可以在方法中抛出的已检查异常都必须在其`throws`子句中指定。

### 优势三：对错误类型进行分组和区分

因为程序中抛出的所有异常都是对象，所以对异常进行分组或分类是类层次结构的自然结果。Java 平台中一组相关异常类的一个例子是定义在[`java.io`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/package-summary.html)-[`IOException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/IOException.html)及其后代中的那些。[`IOException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/IOException.html)是最普遍的，代表执行 I/O 时可能发生的任何类型的错误。它的后代代表更具体的错误。例如，[`FileNotFoundException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/FileNotFoundException.html)表示无法在磁盘上定位文件。

一个方法可以编写可以处理非常具体的异常的特定处理程序。该[`FileNotFoundException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/FileNotFoundException.html)类有没有后代，所以下面的处理器只能处理一个类型的异常。

```java
catch (FileNotFoundException e) {
    ...
}
```

通过在 catch 语句中指定任何异常的超类，方法可以根据其组或一般类型捕获异常。例如，要捕获所有 I/O 异常，不管它们的特定类型如何，异常处理程序指定一个[`IOException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/IOException.html)参数。

```java
catch (IOException e) {
    ...
}
```

此处理程序将能够捕获所有 I/O 异常，包括[`FileNotFoundException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/FileNotFoundException.html)、[`EOFException`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/EOFException.html)等。您可以通过查询传递给异常处理程序的参数来找到有关所发生情况的详细信息。例如，使用以下命令打印堆栈跟踪。

```java
catch (IOException e) {
    // Output goes to System.err.
    e.printStackTrace();
    // Send trace to stdout.
    e.printStackTrace(System.out);
}
```

您甚至可以使用这里的处理程序设置一个异常处理程序来处理任何异常。

```java
// A (too) general exception handler
catch (Exception e) {
    ...
}
```

Exception类接近Throwable类层次结构的顶部。因此，这个处理程序将捕捉许多其他异常，除了那些处理程序打算捕捉的异常。例如，如果您想让程序做的只是为用户输出错误消息，然后退出，那么您可能希望以这种方式处理异常。

然而，在大多数情况下，您希望异常处理程序尽可能具体。原因是，处理程序必须做的第一件事是确定发生了什么类型的异常，然后才能决定最佳的恢复策略。实际上，由于不捕获特定的错误，处理程序必须适应任何可能性。过于通用的异常处理程序会捕获和处理程序员没有预料到的异常，并且处理程序也没有打算处理这些异常，从而使代码更容易出错。

如前所述，您可以创建异常组并以通用的方式处理异常，也可以使用特定的异常类型来区分异常并以精确的方式处理异常。 

## 总结

程序可以使用异常来指示发生了错误。要抛出异常，使用throw语句并为其提供一个异常对象(Throwable的后代)，以提供有关发生的特定错误的信息。抛出未捕获的检查异常的方法必须在其声明中包含throws子句。

程序可以通过组合使用try、catch和finally块来捕获异常。

- 该`try`块标识可能发生异常的代码块。
- 该`catch`块标识一个代码块，称为异常处理程序，可以处理特定类型的异常。
- 该`finally`块标识保证执行的代码块，并且是在 try 块中包含的代码之后关闭文件、恢复资源和以其他方式进行清理的正确位置。

try语句应该至少包含一个catch块或一个finally块，并且可以有多个catch块。

异常对象的类指示抛出的异常类型。异常对象可以包含有关错误的进一步信息，包括错误消息。通过异常链接，一个异常可以指向引起它的异常，而引起它的异常又可以指向引起它的异常，以此类推。