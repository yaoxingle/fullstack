您可以使用基础 JDK 工具和命令来创建和构建应用程序。以下部分描述了可用于创建和构建应用程序的工具和命令：

 

## 介绍javac

[javac](https://docs.oracle.com/en/java/javase/16/docs/specs/man/javac.html) - 读取 Java 类和接口定义并将它们编译成字节码和class 文件

```shell
javac [options] [sourcefiles]
```

***options\*** 命令行选项。

***sourcefiles\*** 要编译的一个或多个源文件（如 MyClass.java）或处理注释（如 MyPackage.MyClass）。

 

## 描述

The javac command reads class and interface definitions, written in the Java programming language, and compiles them into bytecode class files.  The javac command can also process annotations in Java source files and classes.

JDK 9中引入了一个启动器环境变量JDK_JAVAC_OPTIONS，它将其内容放在javac的命令行前面。  

有两种方法可以将源代码文件名传递给`javac`：

- 对于一些源文件，您可以在命令行上列出文件名。
- 对于一些源文件，您可以在javac命令行上使用@filename选项来包含一个列出源文件名的文件。

源代码文件名必须具有.java后缀，类文件名必须具有.class后缀，并且源文件和类文件都必须具有标识类的根名称。 例如，一个名为MyClass的类将被写在一个名为MyClass.java的源文件中:  

```java
class MyClass {
    public static void main(String[] args) {
        System.out.println("this is my class");
    }
}
```

并编译成一个字节码类文件，名为`MyClass.class`：

```shell
javac MyClass.java
```

```shell
MyClass.java
MyClass.class
```

内部类定义产生额外的类文件。这些类文件的名称结合了内部和外部类名，例如 MyClass$MyInnerClass.class。

您应该将源文件排列在反映其包树的目录树中。 例如:  

- **Linux和MacOS**：如果所有的源文件都在/workspace中，那么将com.mysoft.mypack.MyClass的源代码放在/workspace/com .mysoft /mypack/ myclass .java中。  。
- Windows：如果所有的源文件都在\workspace中，那么就把com.mysoft.mypack. myclass的源代码放在\工作空间\com\mysoft\mypack\MyClass.java中  。

默认情况下，编译器将每个类文件作为其源文件放在相同的目录中。 您可以使用标准选项中描述的-d选项指定一个单独的目标目录。   

## 编程接口

javac命令支持由javax.tools包中的类和接口定义的新的Java Compiler API。  

 

## 隐式加载的源文件

 要编译一组源文件，编译器可能需要隐式地加载其他源文件。 这些文件目前不需要进行注释处理。 默认情况下，当注释处理发生并编译任何隐式加载的源文件时，编译器会给出警告。-implicit选项提供了一种抑制警告的方法。  

## 使用 JDK_JAVAC_OPTIONS 环境变量

JDK_JAVAC_OPTIONS环境变量的内容，由空白或空白字符(\n， \t， \r，或\f)分隔，作为参数列表传递给javac的命令行参数前面。  

环境变量的编码要求与系统上的javac命令行相同。 处理JDK_JAVAC_OPTIONS环境变量内容的方式与在命令行中指定的方式相同。  

单引号或双引号可用于括起包含空白字符的参数。 只需删除这对引号，就可以保留左引号和第一个匹配的右引号之间的所有内容。 如果没有找到匹配的报价，启动程序将中止并显示一条错误消息。 支持@files，因为它们是在命令行中指定的。 但是，和@files一样，不支持使用通配符:  

```shell
export JDK_JAVAC_OPTIONS='@"C:\white spaces\argfile"'

export JDK_JAVAC_OPTIONS='"@C:\white spaces\argfile"'

export JDK_JAVAC_OPTIONS='@C:\"white spaces"\argfile'
```



### javac 选项概述

编译器有一组标准选项，以及当前开发环境支持的交叉编译选项。 编译器还具有一组非标准选项，这些选项是特定于当前虚拟机和编译器实现的，但将来可能会更改。 非标准选项以-X开头。 不同的javac选项集将在以下部分中描述:  

- 标准选项
- javac 的交叉编译选项
- 额外选项

### 标准选项

`@filename` 从文件中读取选项和文件名。为了缩短或简化`javac`命令，您可以指定一个或多个包含 javac 命令参数的文件（`-J`选项除外）。这使您可以在任何操作系统上创建任何长度的 javac 命令。

`-Akey[=value]` 指定传递给注释处理器的选项。这些选项不被`javac`直接解释，但可供各个处理器使用。键值应该是一个或多个由点分隔的标识符`.`。

`--add-modules module,module` 指定除初始模块之外要解析的根模块，或者如果模块是 ALL-MODULE-PATH，则指定模块路径上的所有模块。

`--boot-class-path path`或`-bootclasspath path` 覆盖引导类文件的位置。

`--class-path path`、`-classpath path`或`-cp path` 指定在哪里可以找到用户类文件和注释处理器。这个类路径覆盖了`CLASSPATH`环境变量中的用户类路径：

- 如果未指定`--class-path`、 -classpath 或`-cp`，则用户类路径是当前目录。
- 如果`-sourcepath`未指定该选项，则还会在用户类路径中搜索源文件。
- 如果`-processorpath`未指定该选项，则还会在类路径中搜索注释处理器。

-d directory设置类文件的目标目录。 如果类是包的一部分，那么javac将类文件放在反映包名的子目录中，并根据需要创建目录。 例如:  

- **Linux 和 macOS**：如果指定`-d /home/myclasses`并调用类`com.mypackage.MyClass`，则类文件为`/home/myclasses/com/mypackage/MyClass.class`.
- **Windows**：如果您指定`-d C:\myclasses`并调用类`com.mypackage.MyClass`，则类文件为`C:\myclasses\com\mypackage\MyClass.class`.

`-deprecation` 显示不推荐使用的成员或类的每次使用或覆盖的描述。如果没有 -deprecation 选项，javac 会显示使用或覆盖已弃用成员或类的源文件的摘要。-deprecation 选项是 -Xlint:deprecation 的简写。

`--enable-preview` 启用预览语言功能。与 -source 或 --release 结合使用。

`-encoding encoding` 指定源文件使用的字符编码，例如 EUC-JP 和 UTF-8。如果未指定 -encoding 选项，则使用平台默认编码。

`-endorseddirs directories` 覆盖认可标准路径的位置。

`-extdirs directories` 覆盖已安装扩展的位置。目录变量是以冒号分隔的目录列表。在指定目录中的每个 JAR 文件中搜索类文件。找到的所有 JAR 文件都成为类路径的一部分。

`-g` 生成所有调试信息，包括局部变量。默认情况下，只生成行号和源文件信息。

`-g:[lines, vars, source]` 仅生成由逗号分隔的关键字列表指定的调试信息种类。有效的关键字是：

```
`lines` - Line number debugging information.

`vars` - Local variable debugging information.

`source` - Source file debugging information.
```

`-g:none` 不生成调试信息。

`-h directory` 指定放置生成的本机头文件的位置。

当您指定此选项时，将为包含本机方法或具有使用 java.lang.annotation.Native 批注注解的一个或多个常量的每个类生成本机头文件。如果类是包的一部分，则编译器会将本机头文件放在反映包名称的子目录中，并根据需要创建目录。

`--help, –help or -?` 打印标准选项的概要。

`--help-extra or -X` 打印额外选项的帮助。

`-implicit:[none, class]` 指定是否为隐式引用的文件生成类文件：

`-implicit:class` 自动生成类文件。

`-implicit:none` 禁止生成类文件。

如果未指定此选项，则默认自动生成类文件。在这种情况下，如果在进行注解处理时生成了任何类文件，编译器就会发出警告。`-implicit`显式设置选项时不会发出警告。请参阅搜索类型。

`-Joption` 将选项传递给运行时系统，其中选项是`java`命令中描述的 Java 选项之一。例如，`-J-Xms48m`将启动内存设置为 48 MB。

`--limit-modules module,module*` 限制可观察模块的范围。

`--module module-name or -m module-name` 仅编译指定的模块并检查时间戳。

`--module-path path or -p path` 指定在哪里可以找到应用程序模块。

`--module-source-path module-source-path` 指定在何处查找多个模块的输入源文件。

`--module-version version` 指定正在编译的模块的版本。

`-nowarn` 禁用警告消息。此选项的操作与 -Xlint:none 选项相同。

`-parameters` 生成元数据以反映方法参数。将构造函数和方法的形式参数名称存储在生成的类文件中，以便反射 API 中的方法 java.lang.reflect.Executable.getParameters 可以检索它们。

`-proc:[none, only]` 控制是否完成注释处理和编译。`-proc:none`意味着在没有注释处理的情况下进行编译。-proc:only 表示只做注解处理，不做任何后续编译。

`-processor class1[,class2,class3...]` 要运行的注释处理器的名称。这绕过了默认发现过程。

`--processor-module-path path` 指定用于查找注释处理器的模块路径。

`--processor-path path`或`-processorpath path` 指定在哪里可以找到注释处理器。如果未使用此选项，则会在类路径中搜索处理器。

`-profile profile` 检查所使用的 API 在指定的配置文件中是否可用。

`--release release` 针对特定 VM 版本的公共、受支持和记录的 API 进行编译。

指定用于放置生成的源文件的目录。如果类是包的一部分，则编译器会将源文件放在反映包名称的子目录中，并根据需要创建目录。例如：

- **Linux 和 macOS**：如果您指定`-s /home/mysrc`并调用类`com.mypackage.MyClass`，则将源文件放入`/home/mysrc/com/mypackage/MyClass.java`.
- **Windows**：如果您指定`-s C:\mysrc`并调用该类`com.mypackage.MyClass`，则将源文件放入`C:\mysrc\com\mypackage\MyClass.java`。

`-source release` Specifies the version of source code accepted. The following values for release are allowed:

`-source 15` The compiler accepts code with features introduced in Java SE 15.

`-source 16` The default value. The compiler accepts code with features introduced in Java SE 16.

`--source-path path` or `-sourcepath path` Specifies where to find input source files. This is the source code path used to search for class or interface definitions. As with the user class path, source path entries are separated by colons (`:`) on Linux and macOS and semicolons (`;`) on Windows. They can be directories, JAR archives, or ZIP archives. If packages are used, then the local path name within the directory or archive must reflect the package name.

`--system jdk | none` Overrides the location of system modules.

`-target release` Generates class files for a specific VM version.

`--upgrade-module—path path` Overrides the location of upgradeable modules.

`-verbose` 输出关于编译器正在做什么的消息。消息包括有关加载的每个类和编译的每个源文件的信息。

`--version`或`-version` 打印版本信息。

`-Werror` 出现警告时终止编译。

 

## javac 的交叉编译选项

 默认情况下，对于JDK 9之前的版本，类是根据javac命令附带的平台的引导类进行编译的。 但是javac还支持交叉编译，在交叉编译中，类是根据不同Java平台实现的引导类进行编译的。 交叉编译时使用-bootclasspath和-extdirs选项很重要。  

## 额外选项

`--add-exports module/package=other-module(,other-module)*` 当 other-module 的值为 ALL-UNNAMED 时，指定要被视为从其定义模块导出到其他模块或所有未命名模块的包。

`--add-reads module=other-module(,other-module)*` 指定给定模块所需的附加模块。

`--default-module-for-created-files module-name` 如果没有指定或推断，则为注释处理器创建的文件指定后备目标模块。

`-Djava.endorsed.dirs=dirs` 覆盖认可的标准路径的位置。

`-Djava.ext.dirs=dirs` 覆盖已安装扩展的位置。

`--doclint-format [html4|html5]` 指定文档注释的格式。

`--patch-module module=file(:file)*` 使用 JAR 文件或目录中的类和资源覆盖或扩充模块。

`-Xbootclasspath:path` 覆盖引导类文件的位置。

`-Xbootclasspath/a:path` 向引导类路径添加后缀。

`-Xbootclasspath/p:path` 向引导程序类路径添加前缀。

`-Xdiags:[compact, verbose]` 选择诊断模式。

`-Xdoclint` 启用对 javadoc 注释中问题的推荐检查。

`-Xdoclint:(all|none|[-]group)[/access]` 启用或禁用特定的检查组。

group 可以具有以下值之一：

```
    accessibility
    html
    missing
    reference
    syntax
```

access 指定`-Xdoclint`选项检查的类和成员的最低可见性级别。它可以具有以下值之一（按最不可见到最不可见的顺序）：

```
    public
    protected
    package
    private
```

默认访问级别为`private`。

有关这些检查组的更多信息，请参阅`-Xdoclint`javadoc 命令的选项。`-Xdoclint`默认情况下，javac 命令中禁用该选项。

例如，以下选项检查具有 protected 和更高访问级别（包括 protected 和 public）的类和成员（以及所有检查组）：

```shell
-Xdoclint:all/protected
```



```shell
-Xdoclint:all,-html/package
```



`-Xdoclint/package:[-]packages(,[-]package)*` 启用或禁用特定包中的检查。每个包要么是包的限定名称，要么是包名称前缀，后跟一个句点和星号 (.*)，它扩展到给定包的所有子包。每个包都可以以连字符 (-) 为前缀，以禁用对指定包的检查。

`-Xlint` 启用所有推荐的警告。在此版本中，建议启用所有可用警告。

`-Xlint:[-]key(,[-]key)*` 提供要启用或禁用的警告，用逗号 (,) 分隔。在键前面加上连字符 (-) 以禁用指定的警告。

支持的键值为：

```text
        all: Enables all warnings.
        auxiliaryclass: Warns about an auxiliary class that’s hidden in a source file, and is used from other files.
        cast: Warns about the use of unnecessary casts.
        classfile: Warns about the issues related to classfile contents.
        deprecation: Warns about the use of deprecated items.
        dep-ann: Warns about the items marked as deprecated in javadoc but without the @Deprecated annotation.
        divzero: Warns about the division by the constant integer 0.
        empty: Warns about an empty statement after if.
        exports: Warns about the issues regarding module exports.
        fallthrough: Warns about the falling through from one case of a switch statement to the next.
        finally: Warns about finally clauses that do not terminate normally.
        module: Warns about the module system-related issues.
        opens: Warns about the issues related to module opens.
        options: Warns about the issues relating to use of command line options.
        overloads: Warns about the issues related to method overloads.
        overrides: Warns about the issues related to method overrides.
        path: Warns about the invalid path elements on the command line.
        processing: Warns about the issues related to annotation processing.
        rawtypes: Warns about the use of raw types.
        removal: Warns about the use of an API that has been marked for removal.
        requires-automatic: Warns developers about the use of automatic modules in requires clauses.
        requires-transitive-automatic: Warns about automatic modules in requires transitive.
        serial: Warns about the serializable classes that do not provide a serial version ID. Also warns about access to non-public members from a serializable element.
        static: Warns about accessing a static member using an instance.
        try: Warns about the issues relating to the use of try blocks (that is, try-with-resources).
        unchecked: Warns about the unchecked operations.
        varargs: Warns about the potentially unsafe vararg methods.
        none: Disables all warnings.
```

`-Xmaxerrs number` 设置要打印的最大错误数。

`-Xmaxwarns number` 设置要打印的最大警告数。

`-Xpkginfo:[always, legacy, nonempty]` 指定 javac 命令使用以下选项之一从 package-info.java 文件生成 package-info.class 文件的时间和方式：

```text
    always - Generates a package-info.class file for every package-info.java file. This option may be useful if you use a build system such as Ant, which checks that each .java file has a corresponding .class file.
    legacy - Generates a package-info.class file only if package-info.java contains annotations. This option doesn't generate a package-info.class file if package-info.java contains only comments.
    nonempty - Generates a package-info.class file only if package-info.java contains annotations with RetentionPolicy.CLASS or RetentionPolicy.RUNTIME.
```

`-Xplugin:name args` 指定要运行的插件的名称和可选参数。

`-Xprefer:[source, newer]` 使用以下选项之一指定在为隐式编译的类找到源文件和类文件时要读取的文件：

```
`-Xprefer:newer` - Reads the newer of the source or class files for a type (default).

`-Xprefer:source` - Reads the source file. Use `-Xprefer:source` when you want to be sure that any annotation processors can access annotations declared with a retention policy of SOURCE.
```

`-Xprint` 出于调试目的打印指定类型的文本表示。这不会执行注释处理或编译。输出的格式可能会改变。

`-XprintProcessorInfo` 打印有关要求处理器处理哪些注释的信息。

`-XprintRounds` 打印有关初始和后续注释处理轮次的信息。

`-Xstdout filename` 将编译器消息发送到指定文件。默认情况下，编译器消息转到 System.err。

 

## 命令行参数文件

参数文件可以包含任何组合的javac选项和源文件名。 文件中的参数可以用空格或新行字符分隔。 如果文件名包含嵌入的空格，则将整个文件名放在双引号中。  

参数文件中的文件名相对于当前目录，而不是相对于参数文件的位置。 通配符*在这些列表中是不允许的(例如用于指定*.java)。 不支持使用@来递归地解释文件。 不支持-J选项，因为它们被传递到启动器，而启动器不支持参数文件。  

当执行javac命令时，传入带有@前导字符的每个参数文件的路径和名称。 当javac命令遇到以@开头的参数时，它将该文件的内容展开到参数列表中。  

 

## 例子

### 使用 javac @filename 的例子

**单参数文件** 您可以使用名为 argfile 的单参数文件来保存所有 javac 参数：

```shell
javac @argfile
```

这个参数文件可以包含下面两个参数文件示例中所示的两个文件的内容。  

您可以创建两个参数文件:一个用于javac选项，另一个用于源文件名。 注意，下面的列表没有连续的行字符。 创建一个名为options的文件，包含以下内容:  

**Linux 和 macOS：**

```shell
-d classes
-g
-sourcepath /java/pubs/ws/1.3/src/share/classes
```

**Windows**

```shell
-d classes
-g
-sourcepath C:\java\pubs\ws\1.3\src\share\classes
```

创建一个名为 classes 的文件，其中包含以下内容：

```shell
MyClass1.java
MyClass2.java
MyClass3.java
```

然后，运行 javac 命令，如下所示：

```shell
javac @options @classes
```

**带路径** 的参数文件参数文件可以有路径，但文件中的任何文件名都是相对于当前工作目录的（不是路径 1 或路径 2）：

```shell
javac @path1/options @path2/classes
```



### 使用 -Xlint keys的示例

**cast** 对不必要和多余的强制转换发出警告，例如：

```shell
String s = (String) "Hello!"
```

**classfile** 警告与类文件内容相关的问题。

**deprecation** 警告有关已弃用项目的使用。例如：

```shell
java.util.Date myDate = new java.util.Date();
int currentDay = myDate.getDay();
```

`java.util.Date.getDay`自 JDK 1.1 起，该方法已被弃用。

**dep-ann** 对使用 @deprecated Javadoc 注释记录但没有 @Deprecated 注释的项目发出警告，例如：

```shell
/**
  * @deprecated As of Java SE 7, replaced by {@link #newMethod()}
  */
public static void deprecatedMethod() { }
public static void newMethod() { }
```

**divzero** 警告除以常数整数 0，例如：

```shell
int divideByZero = 42 / 0;
```

**empty** 在 if 语句之后警告空语句，例如：

```shell
class E {
    void m() {
         if (true) ;
    }
}
```

**fallthrough** 检查开关块是否有**漏网**情况，并为发现的任何情况提供警告消息。Fall-through case 是 switch 块中的 case，而不是块中的最后一个 case，其代码不包含 break 语句，允许代码执行从那个 case 到下一个 case。例如，此 switch 块中 case 1 标签后面的代码不以 break 语句结尾：

```shell
switch (x) {
case 1:
  System.out.println("1");
  // No break statement here.
case 2:
  System.out.println("2");
}
```

如果在编译这段代码时使用了-Xlint:fallthrough选项，那么编译器就会发出一个警告，提示可能发生的情况，并带有问题情况的行号。  **finally** 警告不能正常完成的 finally 子句，例如：

```shell
public static int m() {
  try {
     throw new NullPointerException();
  }  catch (NullPointerException(); {
     System.err.println("Caught NullPointerException.");
     return 1;
   } finally {
     return 0;
   }
  }
```

在这个例子中，编译器会为finally块生成一个警告。 当int方法被调用时，它返回一个值为0。 当try块退出时，finally块执行。 在这个例子中，当控制转移到catch块时，int方法退出。 然而，finally块必须执行，所以它被执行，即使控制被转移到方法之外。  

**options** 警告与使用命令行选项相关的问题。请参阅 javac 的交叉编译选项。

****overrides** ** 警告与方法覆盖相关的问题。例如，考虑以下两个类：

```java
public class ClassWithVarargsMethod {
  void varargsMethod(String... s) { }
}

public class ClassWithOverridingMethod extends ClassWithVarargsMethod {
   @Override
   void varargsMethod(String[] s) { }
}
```

编译器会生成类似于以下内容的警告：

```shell
warning: [override] varargsMethod(String[]) in ClassWithOverridingMethod 
overrides varargsMethod(String...) in ClassWithVarargsMethod; overriding
method is missing '...'
```



当编译器遇到varargs方法时，它将varargs形参转换为数组。 在方法ClassWithVarargsMethod中。 编译器会翻译varargs的形参String… 字符串[]s，一个匹配方法classwiththoverridingmethod . varargsmethod的形式参数的数组。 因此，可以编译这个示例。  
**path** 警告无效的路径元素和命令行上不存在的路径目录(关于类路径、源路径和其他路径)。 这样的警告不能用@SuppressWarnings注释来抑制。 例如:  

- **Linux and macOS**: `javac -Xlint:path -classpath /nonexistentpath Example.java`
- **Windows**: `javac -Xlint:path -classpath C:\nonexistentpath Example.java`

**processing** 警告与注释处理相关的问题。当您有一个带有注释的类，并且您使用的注释处理器无法处理该类型的异常时，编译器会生成此警告。例如，下面是一个简单的注解处理器：

```java
import java.util.*;
import javax.annotation.processing.*;
import javax.lang.model.*;
import javaz.lang.model.element.*;

@SupportedAnnotationTypes("NotAnno")
public class AnnoProc extends AbstractProcessor {
  public boolean process(Set<? extends TypeElement> elems, RoundEnvironment renv){
     return true;
  }

  public SourceVersion getSupportedSourceVersion() {
     return SourceVersion.latest();
   }
}
```

```java
@interface Anno { }

@Anno
class AnnosWithoutProcessors { }
```

以下命令编译注释处理器`AnnoProc`，然后针对源文件运行此注释处理器`AnnosWithoutProcessors.java`：

```shell
javac AnnoProc.java
javac -cp . -Xlint:processing -processor AnnoProc -proc:only AnnosWithoutProcessors.java
```

当编译器针对源文件运行注解处理器时`AnnosWithoutProcessors.java`，它会生成以下警告：

```shell
warning: [processing] No processor claimed any of these annotations: Anno
```

要解决此问题，您可以将类中定义和使用的注释`AnnosWithoutProcessors`从重命名`Anno`为`NotAnno`。

**rawtypes** 警告原始类型上未经检查的操作。以下语句生成 rawtypes 警告：

```shell
void countElements(List l) { ... }
```

以下示例不会生成 rawtypes 警告：

```shell
void countElements(List<?> l) { ... }
```

List 是一个原始类型。但是，`List<?>`是无界通配符参数化类型。因为 List 是一个参数化接口，所以总是指定它的类型参数。在这个例子中，`List`形式参数被指定为一个无界通配符`?`作为它的形式类型参数，这意味着该`countElements`方法可以接受`List`接口的任何实例化。

**serial** 警告有关在可序列化类上缺少 serialVersionUID 定义。例如：

```shell
public class PersistentTime implements Serializable
{
  private Date time;
 
   public PersistentTime() {
     time = Calendar.getInstance().getTime();
   }
 
   public Date getTime() {
     return time;
   }
}
```

编译器生成以下警告：

```shell
warning: [serial] serializable class PersistentTime has no definition of
serialVersionUID
```

如果一个可序列化的类没有显式地声明一个名为serialVersionUID的字段，那么序列化运行时环境将根据类的各个方面为该类计算一个默认的serialVersionUID值，如Java对象序列化规范所述。 但是，强烈建议所有可序列化的类都显式地声明serialVersionUID值，因为计算serialVersionUID值的默认过程对类细节高度敏感，这些细节可能因编译器实现的不同而不同。 因此，这可能会在反序列化期间导致意外的invalidclassexception异常。 为了保证跨不同Java编译器实现的serialVersionUID值一致，可序列化类必须声明一个显式的serialVersionUID值。  

**static** 警告有关使用静态变量的问题，例如：

```java
class XLintStatic {
    static void m1() { }
    void m2() { this.m1(); }
}
```

编译器生成以下警告：

```shell
warning: [static] static method should be qualified by type name,
XLintStatic, instead of by an expression
```

要解决此问题，您可以调用静态方法 m1，如下所示：

```shell
XLintStatic.m1();
```

或者，您可以从方法 m1 的声明中删除 static 关键字。

**try** 警告与使用 try 块相关的问题，包括 try-with-resources 语句。例如，由于未使用在 try 块中声明的资源 ac，因此为以下语句生成警告：

```shell
try ( AutoCloseable ac = getResource() ) {    // do nothing}
```

**unchecked** 提供 Java 语言规范强制要求的未检查转换警告的更多详细信息，例如：

```shell
List l = new ArrayList<Number>();
List<String> ls = l;       // unchecked warning
```

在类型擦除期间，类型`ArrayList<Number>`和分别`List<String>`变为`ArrayList`和`List`。

该`ls`命令具有参数化类型`List<String>`。将引用的列表`l`分配给 ls 时，编译器会生成未经检查的警告。在编译时，编译器和 JVM 无法确定是否`l`引用了`List<String>`类型。在这种情况下，`l`不引用`List<String>`类型。结果，发生了堆污染。

当发生堆污染情况`List`对象`l`，其静态类型`List<Number>`，被分配给另一个`List`对象，`ls`即具有不同的静态类型，`List<String>`。但是，编译器仍然允许这种分配。它必须允许此分配保持与不支持泛型的 Java SE 版本的向后兼容性。因为类型擦除，中 `List<Number>`和`List<String>`都成为`List`。因此，编译器允许将`l`原始类型为的对象分配`List`给对象 ls。

**varargs** 警告变量参数 (varargs) 方法的不安全使用，特别是那些包含不可具体化参数的方法，例如：

```java
public class ArrayBuilder {
  public static <T> void addToList (List<T> listArg, T... elements) {
    for (T x : elements) {
      listArg.add(x);
    }
  }
}
```



不可具体化的类型是其类型信息在运行时不完全可用的类型。编译器为方法的定义生成以下警告`ArrayBuilder.addToList`：

```shell
warning: [varargs] Possible heap pollution from parameterized vararg type T
```



当编译器遇到 varargs 方法时，它会将 varargs 形式参数转换为数组。但是，Java 编程语言不允许创建参数化类型的数组。在方法中`ArrayBuilder.addToList`，编译器将 varargs 形参`T... elements`转换为形参`T[]`元素，一个数组。但是，由于类型擦除，编译器将 varargs 形式参数转换为`Object[]`元素。因此，存在堆污染的可能性。

### 通过提供命令行参数进行编译的示例

要像提供命令行参数一样进行编译，请使用以下语法：

```shell
JavaCompiler javac = ToolProvider.getSystemJavaCompiler();
```



该示例将诊断信息写入标准输出流，并返回 javac 命令在从命令行调用时给出的退出代码。您可以使用`javax.tools.JavaCompiler`界面中的其他方法来处理诊断、控制读取和写入文件的位置等。

### 编译多个源文件的示例

这个例子编译`Aloha.java`，`GutenTag.java`，`Hello.java`，和`Hi.java`源的问候包文件。

**Linux 和 macOS：**

```shell
% javac greetings/*.java
% ls greetings
Aloha.class         GutenTag.class      Hello.class         Hi.class
Aloha.java          GutenTag.java       Hello.java          Hi.java
```

**Windows：**

```shell
C:\>javac greetings\*.java
C:\>dir greetings
Aloha.class         GutenTag.class      Hello.class         Hi.class
Aloha.java          GutenTag.java       Hello.java          Hi.java
```

### 指定用户类路径的示例

更改上一个示例中的一个源文件后，重新编译它：

**Linux 和 macOS：**

```shell
pwd
/examples
javac greetings/Hi.java
```

**Windows：**

```shell
C:\>cd
\examples
C:\>javac greetings\Hi.java
```

因为问候。 Hi引用了greetings包中的其他类，编译器需要找到这些其他类。 前面的例子是可行的，因为默认的用户类路径是包含包目录的目录。 如果您想重新编译这个文件而不关心您所在的目录，那么通过设置CLASSPATH将示例目录添加到用户类路径中。 这个例子使用了-classpath选项。  

**Linux 和 macOS：**

```shell
javac -classpath /examples /examples/greetings/Hi.java
```

**Windows：**

```shell
C:\>javac -classpath \examples \examples\greetings\Hi.java
If you change greetings.Hi to use a banner utility, then that utility also needs to be accessible through the user class path.
```

**Linux 和 macOS：**

```shell
javac -classpath /examples:/lib/Banners.jar /examples/greetings/Hi.java
```

**Windows：**

```shell
C:\>javac -classpath \examples;\lib\Banners.jar \examples\greetings\Hi.java
```

要执行 greetings 包中的类，程序需要访问 greetings 包，以及 greetings 类使用的类。

**Linux 和 macOS：**

```shell
java -classpath /examples:/lib/Banners.jar greetings.Hi
```

**Windows：**

```shell
C:\>java -classpath \examples;\lib\Banners.jar greetings.Hi
```



## 注释处理

javac命令提供了对注释处理的直接支持，取代了对单独的注释处理命令apt的需要。注释处理器的API定义在javax.annotation.processing和javax.lang.model包和子包中。  

**注释处理的工作原理**

除非使用-proc:none选项禁用注释处理，否则编译器将搜索所有可用的注释处理器。 可以使用-processorpath选项指定搜索路径。 如果没有指定路径，则使用用户类路径。 处理器是通过名为META-INF/services/javax.annotation.processing的服务提供者配置文件来定位的。 处理器上的搜索路径。 这些文件应该包含要使用的任何注释处理器的名称，每行列出一个。 或者，可以使用-processor选项显式指定处理器。 在命令行上扫描源文件和类以确定存在哪些注释之后，编译器查询处理器以确定它们处理哪些注释。 当找到匹配项时，将调用处理器。 处理器可以声明它处理的注释，在这种情况下，不再尝试为这些注释找到任何处理器。 在声明所有的注释之后，编译器不搜索其他处理器。  

如果任何处理器生成新的源文件，则会发生另一轮的注释处理:扫描任何新生成的源文件，并像以前一样处理注释。 在前几轮中调用的任何处理器也会在随后的所有轮中调用。 这个过程一直持续到没有新的源文件生成为止。 在没有生成新源文件的循环发生后，最后一次调用注释处理器，让它们有机会完成任何剩余的工作。 最后，除非使用-proc:only选项，否则编译器将编译原始文件和所有生成的源文件。  

**搜索类型**

要编译源文件，编译器通常需要有关类型的信息，但类型定义不在命令行指定的源文件中。 编译器需要源文件中使用、扩展或实现的每个类或接口的类型信息。 这包括源文件中没有明确提到的类和接口，但它们通过继承提供了信息。  

例如，当您创建java.awt的子类时。 Window，你也在使用Window的祖先类:java.awt。 容器,java.awt。 组件和java . lang . object。 当编译器需要类型信息时，它会搜索定义该类型的源文件或类文件。 编译器首先在引导和扩展类中搜索类文件，然后在用户类路径中搜索类文件(默认情况下是当前目录)。 通过设置CLASSPATH环境变量或使用-classpath选项来定义用户类路径。  

如果您设置了-sourcepath选项，那么编译器将在指定的路径中搜索源文件。 否则，编译器在用户类路径中搜索类文件和源文件。 你可以使用-bootclasspath和-extdirs选项指定不同的引导或扩展类。  

成功的类型搜索可以生成类文件、源文件或两者。 如果两者都找到了，那么可以使用-Xprefer选项来指示编译器使用哪一个。 如果指定了newer，则编译器使用两个文件中较新的。 如果指定了source，编译器使用源文件。 默认值是更新的。  

如果类型搜索在源文件中找到所需类型，无论是它本身，还是-Xprefer选项设置的结果，编译器都将读取源文件以获得所需的信息。 默认情况下，编译器也编译源文件。 您可以使用-implicit选项来指定行为。 如果没有指定类文件，则不会为源文件生成类文件。 如果指定了class，则为源文件生成类文件。  

在注释处理完成之前，编译器可能不会发现对某些类型信息的需要。 当在源文件中找到类型信息而没有指定-implicit选项时，编译器会发出警告，表示正在编译该文件，而没有进行注释处理。 要禁用该警告，可以在命令行上指定该文件(以便进行注释处理)，或者使用-implicit选项指定是否应该为这样的源文件生成类文件。  