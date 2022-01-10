## 介绍 Javap

[javap](https://docs.oracle.com/en/java/javase/16/docs/specs/man/javap.html) - 反汇编一个或多个类文件

```shell
javap [options] classes...
```



**options** 指定命令行选项。请参阅 javap 的选项。

**classes** 指定要为注释处理的一个或多个用空格分隔的类。 可以通过类的文件名、URL或完全限定类名在类路径中指定类。 。

## 例子

```shell
path/to/MyClass.class
```

```shell
jar:file:///path/to/MyJar.jar!/mypkg/MyClass.class
```

```shell
java.lang.Object
```

 

## 描述

javap命令反汇编一个或多个类文件。 输出取决于所使用的选项。 当不使用任何选项时，javap命令将打印受保护字段和公共字段，以及传递给它的类的方法。  

javap命令不支持多版本JAR。 使用命令的类路径形式将导致查看所有JAR文件中的基本条目，无论是否多版本。 使用URL形式，您可以使用参数的URL形式来指定要反汇编的类的特定版本。  

javap命令将其输出打印到stdout。  

 

## 选项

`help, --help, or -?` 打印 javap 命令的帮助消息。

`-version` 打印发布信息。

`-verbose or -v` 打印有关所选类的附加信息。

`-l` 打印行和局部变量表。

`-public` 仅显示公共类和成员。

`-protected` 仅显示受保护的和公共的类和成员。

`-package` 显示包/受保护/公共类和成员（默认）。

`-private`或`-p` 显示所有类和成员。

`-c` 为类中的每个方法打印反汇编代码，例如，组成 Java 字节码的指令。

`-s` 打印内部类型签名。

`-sysinfo` 显示正在处理的类的系统信息（路径、大小、日期、MD5 哈希）。

`-constants` 显示静态最终常量。

`--module module`或`-m module` 指定包含要反汇编的类的模块。

`--module-path path` 指定在哪里可以找到应用程序模块。

`--system jdk` 指定在哪里可以找到系统模块。

`--class-path path`、`-classpath`路径或`-cp path` 指定 javap 命令用来查找用户类文件的路径。它会在设置时覆盖默认值或 CLASSPATH 环境变量。

`-bootclasspath path` 覆盖引导类文件的位置。

`-Joption` 将指定的选项传递给 JVM。例如：

```shell
javap -J-version
javap -J-Djava.security.manager -J-Djava.security.policy=MyPolicy MyClassName
```



 

## 更多例子

编译以下 HelloWorldFrame 类：

```java
import java.awt.Graphics;
 
import javax.swing.JFrame;
import javax.swing.JPanel;
 
public class HelloWorldFrame extends JFrame {
 
   String message = "Hello World!";
 
   public HelloWorldFrame(){
        setContentPane(new JPanel(){
            @Override
            protected void paintComponent(Graphics g) {
                g.drawString(message ,15, 30);
            }
        });
        setSize(100,100);
    }
    
    public static void main(String[] args) {
        HelloWorldFrame frame = new HelloWorldFrame();
        frame.setVisible(true);
    }
}
```

javap`HelloWorldFrame.class`命令的输出结果如下：

```shell
Compiled from "HelloWorldFrame.java"
public class HelloWorldFrame extends javax.swing.JFrame {
  java.lang.String message;
  public HelloWorldFrame();
  public static void main(java.lang.String[]);
}
```

`javap -c HelloWorldFrame.class`命令的输出结果如下：

```shell
Compiled from "HelloWorldFrame.java"
public class HelloWorldFrame extends javax.swing.JFrame {
  java.lang.String message;
 
  public HelloWorldFrame();
    Code:
       0: aload_0
       1: invokespecial #1        // Method javax/swing/JFrame."<init>":()V
       4: aload_0
       5: ldc           #2        // String Hello World!
       7: putfield      #3        // Field message:Ljava/lang/String;
      10: aload_0
      11: new           #4        // class HelloWorldFrame$1
      14: dup
      15: aload_0
      16: invokespecial #5        // Method HelloWorldFrame$1."<init>":(LHelloWorldFrame;)V
      19: invokevirtual #6        // Method setContentPane:(Ljava/awt/Container;)V
      22: aload_0
      23: bipush        100
      25: bipush        100
      27: invokevirtual #7        // Method setSize:(II)V
      30: return
 
  public static void main(java.lang.String[]);
    Code:
       0: new           #8        // class HelloWorldFrame
       3: dup
       4: invokespecial #9        // Method "<init>":()V
       7: astore_1
       8: aload_1
       9: iconst_1
      10: invokevirtual #10       // Method setVisible:(Z)V
      13: return
}
```