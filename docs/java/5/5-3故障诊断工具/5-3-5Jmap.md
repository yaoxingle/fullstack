[jmap](https://docs.oracle.com/en/java/javase/16/docs/specs/man/jmap.html) - 打印指定进程的详细信息

 

## 概要

**注意：**此命令是实验性的，不受支持。

```shell
jmap [options] pid
```

*选项*

这表示`jmap`命令行选项。请参阅[jmap 命令的选项](https://dev.java/learn/jmap---printing-detais-of-a-process/#options-for-the-jmap-command)。

*pid*

要打印由*选项*指定的信息的进程 ID 。该进程必须是 Java 进程。要获取机器上运行的 Java 进程的列表，请使用该`ps`命令，或者，如果 JVM 进程未在单独的 docker 实例中运行，则使用[jps](https://dev.java/learn/jps---listing-your-instrumented-jvms/)命令。

 

## 描述

该`jmap`命令打印指定运行进程的详细信息。

**注意：**

 该命令不受支持，可能在JDK的未来版本中不可用。 在没有dbgen .dll文件的Windows系统上，必须安装Windows调试工具才能使这些工具工作。 PATH环境变量应该包含目标进程使用的jvm.dll文件的位置，或者生成核心转储文件的位置。  



## jmap 命令的选项

`-clstats` *pid*

连接到正在运行的进程并打印 Java 堆的类加载器统计信息。

`-finalizerinfo` *pid*

连接到正在运行的进程并打印有关等待完成的对象的信息。

`-histo``:live` *pid*

连接到正在运行的进程并打印 Java 对象堆的直方图。如果`live`指定了子选项，则它只计算活动对象。

`-dump:`*dump_options* *pid*

连接到正在运行的进程并转储 Java 堆。该*dump_options*包括：

- `live`--- 指定时，仅转储活动对象；如果未指定，则转储堆中的所有对象。
- `format=b`--- 以`hprof`二进制格式转储 Java 堆
- `file=`*文件名*--- 将堆转储到*文件名*

例子：

```shell
jmap -dump:live,format=b,file=heap.bin pid
```

