[jstack](https://docs.oracle.com/en/java/javase/16/docs/specs/man/jstack.html) - 打印指定Java进程的Java线程的Java堆栈跟踪信息 

 

## 概要

**注意：**此命令是实验性的，不受支持。

```shell
jstack [options] pid
```

*options*

这表示`jstack`命令行选项。请参阅[jstack 命令的选项](https://dev.java/learn/jstack---printing-java-stack-traces/#options-for-the-jstack-command)。

*pid*

为其打印堆栈跟踪的进程 ID。该进程必须是 Java 进程。要获取机器上运行的 Java 进程的列表，请使用`ps`命令，或者，如果 JVM 进程未在单独的 docker 实例中运行。

 

## 描述



jstack命令打印指定Java进程的Java线程的Java堆栈跟踪信息。 对于每个Java帧，将打印完整的类名、方法名、字节码索引(BCI)和行号(如果可用)。 c++中乱七八糟的名字不会被曲解。 为了获取c++名称，这个命令的输出可以通过管道传递到c++filt。 当指定的进程在64位JVM上运行时，可能需要指定-J-d64选项，例如:`jstack -J-d64` pid。  

**注意：**

该命令不受支持，可能在JDK的未来版本中不可用。 在没有dbgen .dll文件的Windows系统中，必须安装Windows调试工具才能使这些工具正常工作。 PATH环境变量需要包含目标进程所使用的jvm.dll的位置，或者生成核心转储文件的位置。   

## jstack 命令的选项

```
-l
```

长列表选项打印关于锁的附加信息。

```
-h` 或者 `-help
```

打印帮助消息。