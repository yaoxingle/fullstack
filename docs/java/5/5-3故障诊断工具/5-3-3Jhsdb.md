[jhsdb](https://docs.oracle.com/en/java/javase/16/docs/specs/man/jhsdb.html) - 附加到 Java 进程或启动事后调试器以分析来自崩溃的 Java 虚拟机 (JVM) 的核心转储的内容

 

## 概要

```shell
jhsdb clhsdb [--pid pid | --exe executable --core coredump]

jhsdb hsdb [--pid pid | --exe executable --core coredump]

jhsdb debugd (--pid pid | --exe executable --core coredump) [options]

jhsdb jstack (--pid pid | --exe executable --core coredump | --connect [server-id@]debugd-host) [options]

jhsdb jmap (--pid pid | --exe executable --core coredump | --connect [server-id@]debugd-host) [options]

jhsdb jinfo (--pid pid | --exe executable --core coredump | --connect [server-id@]debugd-host) [options]

jhsdb jsnap (--pid pid | --exe executable --core coredump | --connect [server-id@]debugd-host) [options]
```

*pid*

`jhsdb`工具应附加到的进程 ID 。该进程必须是 Java 进程。要获取机器上运行的 Java 进程的列表，请使用该`ps`命令，或者，如果 JVM 进程未在单独的 docker 实例中运行，则使用[jps](https://dev.java/learn/jps---listing-your-instrumented-jvms/)命令。

**executable**

从中生成核心转储的 Java 可执行文件。

**coredump**

`jhsdb`工具应附加到的核心文件。

```
[server-id@]debugd-host
```

可选的服务器 ID 和远程调试服务器 (debugd) 的地址。

*选项*

jhsdb模式的命令行选项。见[Options for the debugd Mode](https://dev.java/learn/jhsdb---analyzing-the-core-dump-of-a-crashed-jvm/#options-for-the-debugd-mode), [Options for the jstack Mode](https://dev.java/learn/jhsdb---analyzing-the-core-dump-of-a-crashed-jvm/#options-for-the-jstack-mode), [Options for the jmap Mode](https://dev.java/learn/jhsdb---analyzing-the-core-dump-of-a-crashed-jvm/#options-for-the-jmap-mode), [Options for the jinfo Mode](https://dev.java/learn/jhsdb---analyzing-the-core-dump-of-a-crashed-jvm/#options-for-the-jinfo-mode), and [Options for the jsnap Mode](https://dev.java/learn/jhsdb---analyzing-the-core-dump-of-a-crashed-jvm/#options-for-the-jsnap-mode).

**备注：**

无论是*PID*或对*可执行*和*核心*文件或`[server-id@]debugd-host`需要规定`debugd`，`jstack`，`jmap`，`jinfo`和`jsnap`模式。

 

## 描述

您可以使用该`jhsdb`工具附加到 Java 进程或启动事后调试器来分析来自崩溃的 Java 虚拟机 (JVM) 的核心转储的内容。此命令是实验性的，不受支持。

**备注：**

将`jhsdb`工具附加到活动进程将导致进程挂起，并且当调试器分离时进程可能会崩溃。

该`jhsdb`工具可以在以下任何一种模式下启动：

```
jhsdb clhsdb
```

启动交互式命令行调试器。

```
jhsdb hsdb
```

启动交互式 GUI 调试器。

```
jhsdb debugd
```

启动远程调试服务器。

```
jhsdb jstack
```

打印堆栈和锁定信息。

```
jhsdb jmap
```

打印堆信息。

```
jhsdb jinfo
```

打印基本 JVM 信息。

```
jhsdb jsnap
```

打印性能计数器信息。

```
jhsdb command --help
```

显示可用于*命令*的选项。

 

## 调试模式的选项

`--serverid` *服务器 ID*

此调试服务器的可选唯一 ID。如果在同一台机器上运行多个调试服务器，则需要这样做。

`--rmiport` *端口*

设置 RMI 连接器绑定的端口号。如果未指定，则使用随机可用端口。

`--registryport` *端口*

设置 RMI 注册表端口。此选项覆盖系统属性“sun.jvm.hotspot.rmi.port”。如果未指定，则使用系统属性。如果未设置系统属性，则使用默认端口 1099。

`--hostname` *主机名*

设置 RMI 连接器绑定的主机名。该值可以是主机名或 IPv4/IPv6 地址。此选项覆盖系统属性“java.rmi.server.hostname”。如果未指定，则使用系统属性。如果未设置系统属性，则使用系统主机名。

 

## jinfo 模式的选项

```
--flags
```

打印 VM 标志。

```
--sysprops
```

打印 Java 系统属性。

没有选择

打印 VM 标志和 Java 系统属性。

 

## jmap 模式的选项

没有选择

打印与 相同的信息`pmap`。

```
--heap
```

打印`java`堆摘要。

```
--binaryheap
```

`java`以`hprof`二进制格式转储堆。

`--dumpfile` *名称*

转储文件的名称。

```
--histo
```

打印`java`对象堆的直方图。

```
--clstats
```

打印类加载器统计信息。

```
--finalizerinfo
```

打印有关等待完成的对象的信息。

 

## jstack 模式的选项

```
--locks
```

打印`java.util.concurrent`锁信息。

```
--mixed
```

`java`如果平台允许，尝试同时打印本机帧。

 

## jsnap 模式的选项

```
--all
```

打印所有性能计数器。