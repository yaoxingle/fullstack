[jcmd](https://docs.oracle.com/en/java/javase/16/docs/specs/man/jcmd.html) - 向正在运行的 Java 虚拟机 (JVM) 发送诊断命令请求



## 概要

```shell
jcmd [pid | main-class] command... | PerfCounter.print | -f filename

jcmd [-l]

jcmd -h
```

*pid*

使用时，该`jcmd`实用程序将诊断命令请求发送到 Java 进程的进程 ID。

*main-class*

使用时，该`jcmd`实用程序将诊断命令请求发送到具有指定主类名称的所有 Java 进程。

*command*

本`command`必须是一个有效`jcmd`的选择JVM命令。可用命令列表`jcmd`是通过运行`help`命令 ( `jcmd` *pid* `help` )获得的，其中*pid*是正在运行的 Java 进程的进程 ID。如果*pid*是`0`，则命令将发送到所有 Java 进程。main 类参数将用于部分或完全匹配用于启动 Java 的类。如果没有给出选项，它会列出正在运行的 Java 进程标识符以及用于启动进程的主类和命令行参数（与 using 相同`-l`）。

```
Perfcounter.print
```

打印由指定的 Java 进程公开的性能计数器。

`-f` *文件名*

从指定的文件*filename*读取并执行命令。

```
-l
```

显示未在单独的 docker 进程中运行的 Java 虚拟机进程标识符列表以及用于启动进程的主类和命令行参数。如果JVM在docker进程中，则必须使用诸如`ps`查找PID之类的工具。

**注意：**

使用不带参数的jcmd与使用jcmd -l是一样的。

```
-h
```

显示`jcmd`实用程序的命令行帮助。

 

## 描述

jcmd实用程序用于向JVM发送诊断命令请求。它必须在运行JVM的同一台机器上使用，并且具有用于启动JVM的相同有效用户和组标识符。每个诊断命令都有自己的一组参数。若要显示诊断命令的描述、语法和可用参数列表，请使用命令的名称作为参数。例如:

```shell
jcmd pid help command
```

如果参数包含空格，则必须用单引号或双引号('或")将其括起来。此外，您必须转义带有反斜杠的单引号或双引号(\)，以防止操作系统外壳处理引号。或者，您可以先用单引号包围这些参数，然后再用双引号(或者先用双引号，然后用单引号)包围它们。

如果指定进程标识符(pid)或主类(main-class)作为第一个参数，那么jcmd实用程序将诊断命令请求发送给具有指定标识符的Java进程，或者发送给具有指定名称的主类的所有Java进程。通过指定0作为进程标识符，还可以将诊断命令请求发送到所有可用的Java进程。