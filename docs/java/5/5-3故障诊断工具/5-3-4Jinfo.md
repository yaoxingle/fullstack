[jinfo](https://docs.oracle.com/en/java/javase/16/docs/specs/man/jinfo.html) - 为指定的 Java 进程生成 Java 配置信息

 

## 概要

**注意：**此命令是实验性的，不受支持。

```shell
jinfo [option] pid
```

复制

*选项*

这表示`jinfo`命令行选项。请参阅[jinfo 命令的选项](https://dev.java/learn/jinfo---generating-java-configuration-information/#options-for-the-jinfo-command)。

*PID*

要为其打印配置信息的进程 ID。该进程必须是 Java 进程。要获取机器上运行的 Java 进程的列表，请使用该`ps`命令，或者，如果 JVM 进程未在单独的 docker 实例中运行，则使用[jps](https://dev.java/learn/jps---listing-your-instrumented-jvms/)命令。

 

## 描述

该`jinfo`命令打印指定 Java 进程的 Java 配置信息。配置信息包括 Java 系统属性和 JVM 命令行标志。如果指定的进程在 64 位 JVM 上运行，那么您可能需要指定该`-J-d64`选项，例如：

```she
jinfo -J-d64 -sysprops pid
```

复制

此命令不受支持，并且在 JDK 的未来版本中可能不可用。在`dbgeng.dll`不存在的Windows 系统中，必须安装 Windows 调试工具才能使这些工具正常工作。在`PATH`环境变量应该包含的位置`jvm.dll`，这些会用来由目标过程或从其中核心转储文件制作的位置。

 

## jinfo 命令的选项

**备注：**

如果未使用以下任何选项，则会打印命令行标志和系统属性名称-值对。

`-flag` *名称*

打印指定命令行标志的名称和值。

`-flag` `+`| `-`*名称*

启用或禁用指定的布尔命令行标志。

`-flag` *名称*`=`*值*

将指定的命令行标志设置为指定的值。

```
-flags
```

打印传递给 JVM 的命令行标志。

```
-sysprops
```

将 Java 系统属性打印为名称-值对。

```
-h` 或者 `-help
```

打印帮助消息。