## 介绍javadoc

[javadoc](https://docs.oracle.com/en/java/javase/16/docs/specs/man/javadoc.html) - 从 Java 源文件生成 API 文档的 HTML 页面

 

## 概要

```shell
javadoc [options] [packagenames] [sourcefiles] [@files]
```



`options` 指定命令行选项，以空格分隔。

`packagenames` 要记录的包的名称，以空格分隔，例如`java.lang java.lang.reflect java.awt`. 如果您还想记录子包，请使用该`-subpackages`选项来指定包。

默认情况下，`javadoc`在当前目录和子目录中查找指定的包。使用该`-sourcepath`选项指定要查找包的目录列表。

`sourcefiles` 要记录的 Java 源文件的名称，以空格分隔，例如`Class.java Object.java Button.java`. 默认情况下，`javadoc`在当前目录中查找指定的类。但是，您可以指定类文件的完整路径并使用通配符，例如`/home/src/java/awt/Graphics*.java`. 您还可以指定相对于当前目录的路径。

@files包含javadoc工具选项列表、包名和源文件名(按任意顺序)的文件名。   

## 描述

javadoc工具解析一组Java源文件中的声明和文档注释，并生成相应的HTML页面，这些页面(默认情况下)描述公共类和受保护类、嵌套类(但不是匿名内部类)、接口、构造函数、方法和字段。 您可以使用javadoc工具生成API文档，或者一组源文件的实现文档。  

 

您可以在整个包、单个源文件或两者上运行javadoc工具。 在记录整个包时，可以使用-subpackages选项递归地遍历目录及其子目录，或者传入一个显式的包名列表。 当您记录单个源文件时，传入一个Java源文件名列表。  

 

## 一致性

标准的doclet不验证文档注释的内容是否一致，也不尝试纠正文档注释中的任何错误。 建议任何运行javadoc的人在生成不符合规范的输出或包含可执行内容(如JavaScript)的输出时注意可能会出现的问题。 标准的doclet确实提供了docclint特性来帮助开发人员检测文档注释中的常见问题; 但也建议使用任何适当的一致性和其他检查工具检查生成的输出。   

## javadoc 的选项

以下核心 javadoc 选项等效于相应的`javac`选项。有关使用这些选项的详细说明，请参阅标准选项：

```
--add-modules

-bootclasspath

--class-path, -classpath, or -cp

--enable-preview

-encoding

-extdirs

--limit-modules

--module

--module-path or -p

--module-source-path

--release

-source

--source-path or -sourcepath

--system

--upgrade-module-path
```

以下选项是核心 javadoc 选项，不等同于相应的 javac 选项：

`-breakiterator` 用 BreakIterator 计算第一句话。第一句话被到包、类或成员摘要和字母索引中。BreakIterator 类用于确定除英语之外的所有语言的句子的结尾：

```
English default sentence-break algorithm — Stops at a period followed by a space or an HTML block tag, such as <P>.

Breakiterator sentence-break algorithm — Stops at a period, question mark, or exclamation point followed by a space when the next word starts with a capital letter. 
This is meant to handle most abbreviations (such as "The serial no. is valid", but will not handle "Mr. Smith"). 
The -breakiterator option doesn’t stop at HTML tags or sentences that begin with numbers or symbols. The algorithm stops at the last period in ../filename, even when embedded in an HTML tag.
```

`-doclet class` 使用备用 doclet 生成输出。使用完全限定的名称。此 doclet 定义内容并格式化输出。如果未使用 -doclet 选项，则 javadoc 工具使用标准 doclet 生成默认 HTML 格式。此类必须包含 start(Root) 方法。此起始类的路径由 -docletpath 选项定义。

`-docletpath path` 指定在何处查找 doclet 类文件（使用 -doclet 选项指定）及其依赖的任何 JAR 文件。如果起始类文件在 JAR 文件中，则此选项指定该 JAR 文件的路径。您可以指定绝对路径或相对于当前目录的路径。如果`classpathlist`包含多个路径或 JAR 文件，则它们`:`在 Linux 和 macOS 上应该用冒号 ( )分隔，`;`在 Windows上应该用分号 ( )分隔。当 doclet 起始类已经在搜索路径中时，此选项不是必需的。

`-exclude pkglist` 无条件地从由 形成的列表中排除指定的包及其子包`-subpackages`。它排除那些包，即使它们本来会被某些较早或较晚的`-subpackages`选项包含在内。

以下示例将包括`java.io`、`java.util`和`java.math`（以及其他），但将排除根植于 java.net 和 java.lang 的包。请注意，这些示例不包括 java.lang.ref，它是 的子包`java.lang`：

**Linux 和 macOS：**

```shell
javadoc -sourcepath /home/user/src -subpackages java -exclude java.net:java.lang
```



**Windows：**

```shell
javadoc -sourcepath \user\src -subpackages java -exclude java.net:java.lang
```



`--expand-requires value` 指示 javadoc 工具扩展要记录的模块集。默认情况下，仅记录在命令行上明确给出的模块。支持以下值：

传递性：另外包括这些模块的所有必需的传递性依赖项。

all：包括所有依赖项。

`-help or --help` 显示联机帮助，其中列出了所有 javadoc 和 doclet 命令行选项。

`--help-extra or -X` 打印非标准选项和退出的概要。

`-Jflag` 将标志直接传递给运行 javadoc 工具的 Java 运行时环境 (JRE)。例如，如果你必须确保系统留出32 MB的内存中处理生成的文档，那么你会打电话-Xmx选项如下：`javadoc -J-Xmx32m -J-Xms32m com.mypackage`。请注意，这`-Xms`是可选的，因为它仅设置初始内存的大小，这在您知道所需的最小内存量时很有用。

J 和标志之间没有空格。

使用该`-version`选项报告用于运行 javadoc 工具的 JRE 版本：

```shell
javadoc -J-version

openjdk version "16" 2021-03-16
OpenJDK Runtime Environment (build 16+36-2231)
OpenJDK 64-Bit Server VM (build 16+36-2231, mixed mode, sharing)
```



`-locale name` 指定 javadoc 工具在生成文档时使用的语言环境。参数是语言环境的名称，如 java.util.Locale 文档中所述，例如`en_US`（英语、美国）或`en_US_WIN`（Windows 变体）。

指定区域设置会导致该`javadoc`工具选择该区域设置的资源文件以用于消息，例如导航栏中的字符串、列表和表格的标题、帮助文件内容、文件中的注释`stylesheet.css`等。它还指定按字母顺序排序的列表的排序顺序，以及确定第一个句子结尾的句子分隔符。-locale 选项不确定文档类的源文件中指定的文档注释文本的语言环境。

`-package` 仅显示包、受保护和公共类和成员。

`-private` 显示所有类和成员。

`-protected` 仅显示受保护的和公共的类和成员。这是默认设置。

`-public` 仅显示公共类和成员。

`-quiet` 关闭消息，只显示警告和错误，以便于查看。它还抑制版本字符串。

`--show-members value` 指定记录哪些成员（字段或方法），其中 value 可以是以下任何一项：

```
protected: The default value is protected.

public: Shows only public values.

package: Shows public, protected, and package members.

private: Shows all members.
```

`--show-module-contents value` 指定模块声明的文档粒度，其中 value 可以是 api 或 all。

`--show-packages value` 指定记录哪些模块包，可以导出值的位置或所有包。

`--show-types value` 指定记录哪些类型（类、接口等），其中 value 可以是以下任何一种：

```
protected: The default value. Shows public and protected types.

public: Shows only public values.

package: Shows public, protected, and package types.

private: Shows all types.
```

`-subpackages subpkglist` 从指定包中的源文件并在其子包中递归生成文档。此选项在向源代码添加新子包时很有用，因为它们会自动包含在内。每个包参数是`javax.swing`不需要包含源文件的任何顶级子包（例如 java）或完全限定的包（例如）。在所有操作系统上，参数用冒号分隔。不允许使用外卡。使用`-sourcepath`指定到哪里可以找到包。此选项不处理位于源树中但不属于包的源文件。

例如，以下命令为名为 java 的包`javax.swing`及其所有子包生成文档：

**Linux 和 macOS：**

```shell
javadoc -d docs -sourcepath /home/user/src -subpackages java:javax.swing
```



**Windows：**

```shell
javadoc -d docs -sourcepath \user\src -subpackages java:javax.swing
```



`-verbose` 在 javadoc 工具运行时提供更详细的消息。如果没有该`-verbose`选项，则会出现有关加载源文件、生成文档（每个源文件一条消息）和排序的消息。-verbose 选项导致打印附加消息，这些消息指定解析每个 Java 源文件的毫秒数。

`--version` 打印版本信息。

 

## 扩展选项

以下扩展的 javadoc 选项等效于相应的 javac 选项。有关使用这些选项的详细说明，请参阅 javac 中的额外选项：--add-exports

```
--add-reads

--patch-module

-Xmaxerrs

-Xmaxwarns
```

以下扩展的 javadoc 选项不等同于相应的 javac 选项：

`-Xmodule:module-name` 指定正在编译的类所属的模块。

`-Xold` 调用旧的 javadoc 工具。

 

## 标准 Doclet 选项

以下选项由标准 doclet 提供。

`--add-stylesheet file` 为生成的文档添加额外的样式表文件。此选项可以使用一次或多次以指定文档中包含的其他样式表。

命令行示例：

```shell
javadoc --add-stylesheet new_stylesheet_1.css --add-stylesheet new_stylesheet_2.css pkg_foo 
```

`--allow-script-in-comments` 在选项和注释中允许 JavaScript

`-author` 在生成的文档中包含 @author 文本。

`-bottom html-code` 指定要放置在每个输出文件底部的文本。文本位于页面底部，下方导航栏下方。文本可以包含 HTML 标记和空格，但如果包含，则必须用引号将文本括起来。对文本中的任何内部引号使用转义字符。

`-charset name` 指定此文档的 HTML 字符集。该名称应该是 IANA 注册表字符集中指定的首选 MIME 名称。

例如：

```shell
javadoc -charset "iso-8859-1" mypackage
```

此命令在每个生成的页面的头部插入以下行：

```shell
<META http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
```



META 标记在 HTML 标准（4197265 和 4137321），HTML 文档表示中进行了描述。

`-d directory` 指定 javadoc 工具保存生成的 HTML 文件的目标目录。如果省略 -d 选项，则文件将保存到当前目录。目录值可以是绝对的，也可以是相对于当前工作目录的。目标目录是在 javadoc 工具运行时自动创建的。

**Linux 和 macOS：**例如，以下命令为包生成文档`com.mypackage`并将结果保存在`/user/doc/`目录中：

```shell
javadoc -d /user/doc/ com.mypackage
```

**Windows：**例如，以下命令生成包的文档`com.mypackage`并将结果保存在`\user\doc\`目录中：

```shell
javadoc -d \user\doc\ com.mypackage
```

`-docencoding name` 指定生成的 HTML 文件的编码。该名称应该是 IANA 注册表字符集中指定的首选 MIME 名称。

三个选项可用于 javadoc 编码命令。-encoding 选项用于对javadoc 工具读取的文件进行编码，而`-docencoding`和`-charset`选项用于对工具写入的文件进行编码。在三个可用选项中，在单个编码命令中最多只使用输入和输出编码选项。如果在命令中同时指定输入和输出编码选项，它们的值必须相同。如果您既未指定输出选项，则该工具默认为输入编码。

例如：

```shell
javadoc -docencoding "iso-8859-1" mypackage
```



`-docfilessubdirs` 递归 doc-file 子目录。

`-doctitle html-code` 指定要放置在概览摘要文件顶部附近的标题。标题标签中指定的文本作为居中的一级标题放置在顶部导航栏的正下方。标题标记可以包含 HTML 标记和空格，但如果包含，您必须将标题用引号引起来。标题标签内的额外引号必须被转义。例如，javadoc -header "<b>My Library</b><br>v1.0" com.mypackage。

`-excludedocfilessubdir name` 排除具有给定名称的任何 doc 文件子目录。启用文档文件目录的深层。子目录和所有内容将递归到目标。例如，目录 doc-files/example/images 及其所有内容。还有一个选项可以排除子目录。

`-footer html-code` 指定放置在每个输出文件底部的页脚文本。html-code 值位于下方导航栏的右侧。html-code 值可以包含 HTML 标记和空格，但如果包含，则 html-code 值必须用引号引起来。对页脚中的任何内部引号使用转义字符。

`--frames` 在生成的输出中启用帧的使用（默认）。

`-group namep1:p2` 在概览页面中将指定的包组合在一起。

`-header html-code` 指定要放置在每个输出文件顶部的标题文本。标题位于上部导航栏的右侧。标题可以包含 HTML 标签和空格，但如果包含，标题必须用引号引起来。对标题中的内部引号使用转义字符。例如，javadoc -header "<b>My Library</b><br>v1.0" com.mypackage。

`-helpfile filename` 包括链接到顶部和底部导航栏中帮助链接的文件。如果没有此选项，javadoc 工具将创建一个帮助文件 help-doc.html，该文件在 javadoc 工具中进行了硬编码。此选项可让您覆盖默认值。文件名可以是任何名称，不限于 help-doc.html。 javadoc 工具会相应地调整导航栏中的链接。例如：

**Linux 和 macOS：**

```shell
javadoc -helpfile /home/user/myhelp.html java.awt
```

**Windows：**

```shell
javadoc -helpfile C:\user\myhelp.html java.awt.
```

`-html4` Generates HTML 4.0.1 output. HTML 5 output is the default.

`-html5` Generates HTML 5 output (default).

`--javafx or -javafx` Enables JavaFX functionality.

-keywords 为每个类生成的文件添加HTML关键字<META>标签。 这些标签可以帮助搜索引擎寻找<META>标签找到页面。 大多数搜索引擎在整个互联网上搜索<META>标签，因为页面可能会误用它们。 公司提供的搜索引擎限制他们自己的网站可以通过查看<META>标签受益。 <META>标记包括类的完全限定名称以及字段和方法的非限定名称。 没有包含构造函数，因为它们与类名相同。 例如，类String以以下关键字开头:  

```shell
<META NAME="keywords" CONTENT="java.lang.String class">
<META NAME="keywords" CONTENT="CASE_INSENSITIVE_ORDER">
<META NAME="keywords" CONTENT="length()">
<META NAME="keywords" CONTENT="charAt()">
```

`-link url` 创建指向现有 javadoc 生成的外部引用类文档的链接。URL 参数是包含外部 javadoc 生成的文档的目录的绝对或相对 URL。您可以`-link`在指定的 javadoc 工具运行中指定多个选项以链接到多个文档。package-list 或 element-list 文件必须在这个 url 目录中（否则，使用该`-linkoffline`选项）。

当您使用该`javadoc`工具记录包时，它使用该`package-list`文件来确定 API 中声明的包。当您为模块生成 API 文档时，该`javadoc`工具使用该`element-list`文件来确定 API 中声明的模块和包。

javadoc 工具从适当的列表文件中读取名称，然后链接到该 URL 处的包或模块。当 javadoc 工具运行时，URL 值被到`<A HREF>`创建的链接中。因此， url 必须是目录的 URL，而不是文件的 URL。您可以使用 url 的绝对链接使您的文档链接到任何网站上的文档，或者您可以使用相对链接仅链接到相对位置。

如果使用相对链接，则传入的值应该是从目标目录（使用 -d 选项指定）到包含链接到的包的目录的相对路径。指定绝对链接时，通常使用 HTTP 链接。但是，如果要链接到没有 Web 服务器的文件系统，则可以使用文件链接。仅当想要访问生成的文档的每个人共享相同的文件系统时才使用文件链接。在所有情况下，在所有操作系统上，使用斜杠作为分隔符，无论 URL 是绝对的还是相对的，以及 https:、http: 或 file:，如 URL 备忘录：统一资源定位器中指定的那样。

```
-link https://<host>/<directory>/<directory>/.../<name>
-link http://<host>/<directory>/<directory>/.../<name>
-link file://<host>/<directory>/<directory>/.../<name>
-link <directory>/<directory>/.../<name>
-linkoffline url1 url2
```

此选项是选项的变体`-link`。它们都为外部引用的类创建指向 javadoc 生成的文档的链接。您可以`-linkoffline`在指定的 javadoc 工具运行中指定多个选项。

在以下情况下使用该`-linkoffline`选项：

- 链接到 javadoc 工具无法通过 Web 连接访问的 Web 文档。
- `package-list`外部文档的or element-list 文件要么不可访问，要么不存在于 URL 位置，但确实存在于不同的位置，并且可以由`package-list`or`element-list`文件（通常是本地）指定。

如果 url1 只能在万维网上访问，则该`-linkoffline`选项将取消 javadoc 工具必须具有 Web 连接才能生成文档的限制。

该`-linkoffline`选项的另一个用途是作为更新文档的解决方法。在完整的包或模块集上运行 javadoc 工具后，您可以在更小的更改包或模块集上再次运行 javadoc 工具，以便可以将更新的文件插入回原始集。

例如，该`-linkoffline`选项采用两个参数。第一个是将字符串嵌入到`<a href>`链接中，第二个告诉 javadoc 工具在哪里可以找到包列表或元素列表文件。

url1 或 url2 值是包含要链接到的外部 javadoc 生成的文档的目录的绝对或相对 URL。当相对时，该值应该是从目标目录（使用 -d 选项指定）到所链接的包的根目录的相对路径。在 -link 选项中查看 url。

-linksource为每个源文件创建一个HTML版本(带有行号)，并从标准HTML文档中为它们添加链接。 链接是为声明在源文件中的类、接口、构造函数、方法和字段创建的。 否则，就不会创建链接，比如默认构造函数和生成的类。  

此选项公开所包含源文件中的所有私有实现细节，包括私有类、私有字段和私有方法体，而不管-public、-package、-protected和-private选项。 除非您也使用-private选项，否则并不是所有的私有类或接口都可以通过链接访问。  

每个链接都出现在其声明中的标识符名称上。 例如，Button类的源代码链接应该在单词Button上:  

```shell
public class Button extends Component implements Accessible
```

Button类中getLabel方法的源代码链接在单词getLabel上:  

```shell
public String getLabel()
```

`--main-stylesheet file`或`-stylesheetfile file` 指定替代样式表文件的路径，该文件包含生成的文档中使用的 CSS 样式的定义。此选项可让您覆盖默认值。如果未指定该选项，javadoc 工具将创建并使用默认样式表。文件名可以是任何名称，不限于`stylesheet.css`. 该`--main-stylesheet`选项是优选的形式。

命令行示例：

```shell
javadoc --main-stylesheet main_stylesheet.css pkg_foo
```

`-nocomment` 抑制整个评论正文，包括主要描述和所有标签，并且只生成声明。此选项允许您重用最初用于不同目的的源文件，以便您可以在新项目的早期阶段生成框架 HTML 文档。

`-nodeprecated` 防止在文档中生成任何已弃用的 API。这与 -nodeprecatedlist 选项的作用相同，并且不会在整个文档的其余部分生成任何已弃用的 API。当您不想被弃用的代码分心时，这在编写代码时很有用。

`-nodeprecatedlist` 阻止生成包含已弃用 API 列表 (deprecated-list.html) 和导航栏中指向该页面的链接的文件。javadoc 工具在整个文档的其余部分继续生成已弃用的 API。当您的源代码不包含已弃用的 API 并且您希望使导航栏更干净时，这很有用。

`--no-frames` 在生成的输出中禁用帧的使用。

`-nohelp` 在每个输出页面的顶部和底部的导航栏中省略 HELP 链接。

`-noindex` 从生成的文档中省略索引。默认情况下生成索引。

`-nonavbar` 防止生成导航栏、页眉和页脚，它们通常位于生成页面的顶部和底部。-nonavbar 选项对 -bottom 选项没有影响。-nonavbar 选项在您只对内容感兴趣并且不需要导航时很有用，例如当您将文件转换为 PostScript 或 PDF 仅用于打印时。

`-noqualifier name1:name2...` 从输出中排除限定符列表。从出现类或接口名称的地方删除包名称。

- 以下示例省略了所有包限定符：`-noqualifier`all。
- 以下示例省略了 java.lang 和 java.io 包限定符：`-noqualifier java.lang:java.io`.
- 以下示例省略了以 java 和 com.sun 子包开头的包限定符，但不包括 javax: `-noqualifier java.*:com.sun.*`。

如果由于先前的行为而出现包限定符，则可以适当缩短名称。无论是否`-noqualifier`使用该选项，此规则均有效。

`-nosince` 从生成的文档中省略与 @since 标签关联的部分。

`-notimestamp` 隐藏时间戳，该时间戳隐藏在每个页面顶部附近生成的 HTML 中的 HTML 注释中。当您想在两个源代码库上运行 javadoc 工具并获取它们之间的差异时，-notimestamp 选项很有用，因为它可以防止时间戳导致差异（否则每个页面上都会有差异）。时间戳包括 javadoc 工具版本号。

`-notree` 从生成的文档中省略类和接口层次结构页面。这些是您使用导航栏中的“树”按钮访问的页面。默认情况下生成层次结构。

`--override-methods (detail|summary)` 在详细信息或摘要部分记录覆盖方法。

`-overview filename` 指定 javadoc 工具应从文件名指定的源文件中检索概述文档的文本，并将其放置在概述页面 (overview-summary.html) 上。用文件名指定的相对路径是相对于当前工作目录的。

虽然您可以为文件名值使用任何您想要的名称并将其放置在您想要作为路径的任何位置，但通常将其命名为overview.html 并将其放置在包含最顶层包目录的目录中的源代码树中。在此位置，记录包时不需要路径，因为 -sourcepath 选项指向此文件。

**Linux 和 macOS：**例如，如果 java.lang 包的源代码树是`/src/classes/java/lang/`，那么您可以将概览文件放在`/src/classes/overview.html`.

**Windows：**例如，如果 java.lang 包的源代码树是`\src\classes\java\lang\`，那么您可以将概览文件放在`\src\classes\overview.html`

仅当您将两个或多个包名称传递给 javadoc 工具时，才会创建概览页面。概览页面上的标题由 设置`-doctitle`。

`-serialwarn` 为丢失的`@serial`标签生成编译时警告。默认情况下，javadoc 不生成串行警告。使用此选项显示串行警告，这有助于正确记录默认的可序列化字段和 writeExternal 方法。

`-sourcetab tablength` 指定每个选项卡在源中使用的空格数。

`-splitindex` 将索引文件拆分为多个文件，按字母顺序，每个字母一个文件，以及一个用于以非字母符号开头的任何索引条目的文件。

`-tag name:locations:header` 指定单参数自定义标签。对于`javadoc`拼写检查标签名称的工具，重要的`-tag`是为源代码中存在的每个自定义标签包含一个选项，禁用（使用 X）那些在当前运行中未输出的标签。冒号`:`始终是分隔符。该`-tag`选项以粗体输出标记标题，标题，下一行是来自其单个参数的文本。与任何块标记类似，参数文本可以包含内联标记，这些标记也会被解释。输出类似于标准的单参数标签，例如`@return`和@author 标签。省略标题值会导致标记名成为标题。

`-taglet class` 指定用于为该标签生成文档的 taglet 的完全限定名称。使用类值的完全限定名称。这个 taglet 还定义了自定义标签具有的文本参数的数量。taglet 接受这些参数，处理它们并生成输出。

Taglets 对于块或内联标签很有用。它们可以有任意数量的参数并实现自定义行为，例如使文本加粗、设置项目符号、将文本写入文件或启动其他进程。Taglets 只能决定一个标签应该出现在哪里以及以什么形式出现。所有其他决定均由 doclet 做出。taglet 不能做一些事情，比如从包含的类列表中删除类名。但是，它可以执行副作用，例如将标签的文本打印到文件或触发另一个进程。使用 -tagletpath 选项指定 taglet 的路径。以下示例在生成的页面中的参数之后和 Throws 之前插入 To Do taglet。

```shell
-taglet com.sun.tools.doclets.ToDoTaglet
-tagletpath /home/taglets 
-tag return
-tag param
-tag todo
-tag throws
-tag see
```



或者，您可以使用 -taglet 选项代替其 -tag 选项，但这可能难以阅读。

`-tagletpath tagletpathlist` 指定用于查找 taglet 类文件的搜索路径。所述`tagletpathlist`可以通过将它们与一个结肠分离包含多个路径`:`。该`javadoc`工具搜索指定路径的所有子目录。

`-top html-code` 指定放置在每个输出文件顶部的文本。

`-use` 创建类和包使用页面。每个记录的类和包包括一个使用页面。该页面描述了哪些包、类、方法、构造函数和字段使用指定类或包的任何 API。给定类 C，使用类 C 的内容将包括 C 的子类、声明为 C 的字段、返回 C 的方法以及带有 C 类型参数的方法和构造函数。例如，您可以查看 String 类型的 Use 页面。因为类中的`getName`方法`java.awt.Font`返回类型`String`，`getName`方法使用`String`，所以该`getName`方法出现在使用页面上`String`. 本文档仅记录 API 的使用，不记录实现。当方法在其实现中使用 String，但不将字符串作为参数或返回字符串时，这不被视为使用 String。要访问生成的 Use 页面，请转到类或包并单击使用导航栏中的链接。

`-version` 在生成的文档中包含版本文本。默认情况下省略此文本。要找出您使用的 javadoc 工具的版本，请使用 -J-version 选项。

`-windowtitle title` 指定要放置在 HTML`<title>`标记中的标题。标题标签中指定的文本出现在窗口标题和某人为此页面创建的任何浏览器书签（收藏夹）中。此标题不应包含任何 HTML 标记，因为浏览器无法正确解释它们。在标题标签内的任何内部引号上使用转义字符。如果`-windowtitle`省略该选项，则 javadoc 工具使用该`-doctitle`选项的`-windowtitle`选项值。例如，`javadoc -windowtitle "My Library" com.mypackage`。

 

## 标准 Doclet 提供的附加选项

以下是标准 doclet 提供的附加选项，如有更改，恕不另行通知。其他选项可能不太常用或被视为高级选项。

`-Xdoclint` 启用对 Javadoc 注释中问题的推荐检查。

`-Xdoclint:(all|none|[-]group)` 启用或禁用对错误引用、缺乏可访问性、缺少 javadoc 注释的特定检查，并报告无效 javadoc 语法和缺少 HTML 标记的错误。

此选项使该`javadoc`工具能够检查生成的输出中包含的所有文档注释。您可以选择与标准选项生成的输出中包含哪些项目`-public`，`-protected`，`-package`，和`-private`。

当`-Xdoclint`启用时，它会报告有类似的javac命令消息的问题。javadoc 工具会打印一条消息、一份源代码行的副本以及一个指向检测到错误的确切位置的插入符号。消息可能是警告或错误，这取决于它们的严重性，以及如果生成的文档通过验证器运行时导致错误的可能性。例如，错误的引用或缺少 javadoc 注释不会导致 javadoc 工具生成无效的 HTML，因此这些问题会报告为警告。语法错误或缺少 HTML 结束标记会导致 javadoc 工具生成无效输出，因此将这些问题报告为错误。

该`-Xdoclint`选项根据请求的标记验证输入注释。

默认情况下，该`-Xdoclint`选项处于启用状态。使用选项禁用它`-Xdoclint:none`。

以下选项更改`-Xdoclint`选项报告的内容：

```
`-Xdoclint none`: Disables the `-Xdoclint` option

`-Xdoclint group`: Enables group checks

`-Xdoclint all`: Enables all groups of checks

`-Xdoclint all,-group`: Enables all checks except group checks
```

组变量具有以下值之一：

- `accessibility`：检查可访问性检查器要检测的问题（例如，<table> 标记中未指定标题或摘要属性）。
- `html`：检测高级 HTML 问题，例如将块元素放在行内元素中，或不关闭需要结束标记的元素。这些规则源自 HTML 4 规范或 HTML 5 规范，基于所选的标准 doclet html 输出生成。这种类型的检查使该`javadoc`工具能够检测某些浏览器可能无法按预期解释的 HTML 问题。
- `missing`：检查缺少的 Javadoc 注释或标签（例如，缺少注释或类，或者缺少 @return 标签或方法上的类似标签）。
- `reference`：检查与从`javadoc`标签对 Java API 元素的引用相关的问题（例如，在 @see 中找不到项目或@param 之后的名称错误）。
- `syntax`：检查低级问题，如未转义的尖括号`<`和`>`与号`&`以及无效的 javadoc 标签。

您可以`-Xdoclint`多次指定该选项以启用检查多个类别中的错误和警告的选项。或者，您可以使用上述选项指定多个错误和警告类别。例如，使用以下任一命令检查文件 filename 中的 HTML、语法和可访问性问题。

```shell
javadoc -Xdoclint:html -Xdoclint:syntax -Xdoclint:accessibility filename
javadoc -Xdoclint:html,syntax,accessibility filename
```



`-Xdoclint/package:([-]) packages` 启用或禁用特定包中的检查。包是一个逗号分隔的包说明符列表。包说明符是包的限定名称或包名称前缀后跟 *，它扩展到给定包的所有子包。使用破折号 (-) 为包说明符添加前缀以禁用对指定包的检查。

`-Xdocrootparent url` 将 javadoc 注释中的所有 @docRoot 项后跟 /.. 替换为 url。