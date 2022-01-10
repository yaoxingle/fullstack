[jstat](https://docs.oracle.com/en/java/javase/16/docs/specs/man/jstat.html) - 监控 JVM 统计信息

 

## 概要

**注意：**此命令是实验性的，不受支持。

```shell
jstat generalOptions

jstat outputOptions [-t] [-h lines] vmid [interval [count]]
```

*常规选项*

单个通用命令行选项。

*输出选项*

由-options选项报告的选项。一个或多个输出选项，包括单个statOption，以及任意的-t、-h和-J选项。请参见jstat命令的输出选项。

```
-t
```

将时间戳列显示为输出的第一列。时间戳是从目标JVM的开始时间开始的时间。

`-h` *n*

每n个样本(输出行)显示一个列标头，其中n是一个正整数。默认值是0，它显示第一行数据的列标题。

**vmid**

虚拟机标识符，它是一个字符串，表示目标 JVM。请参阅[虚拟机标识符](https://dev.java/learn/jstat---monitoring-your-jvms-statistics/#virtual-machine-identifier)。

*interval*

采样间隔，单位为秒(s)或毫秒(ms)。默认单位为毫秒。这一定是一个正整数。如果指定了该参数，jstat命令将在每个时间间隔生成它的输出。

*count*

显示样品的数量。默认值是无穷大，这将导致jstat命令显示统计信息，直到目标JVM终止或jstat命令终止。该值必须为正整数。

 

## 描述

jstat命令显示一个已测试的Java HotSpot VM的性能统计数据。 目标JVM由其虚拟机标识符或vmid选项标识。  

jstat命令支持两种类型的选项，通用选项和输出选项。 常规选项会导致jstat命令显示简单的用法和版本信息。 输出选项决定统计输出的内容和格式。  

 所有选项及其功能都可能在未来的版本中更改或删除。  

## 常规选项

如果指定一个通用选项，则不能指定任何其他选项或参数。  

```
-help
```

显示帮助消息。

```
-options
```

显示静态选项列表。 请参见jstat命令的[输出选项](https://dev.java/learn/jstat---monitoring-your-jvms-statistics/#output-options-for-the-jstat-command)。

 

## Jstat 命令的输出选项

如果不指定通用选项，则可以指定输出选项。 输出选项决定jstat命令输出的内容和格式，由单个statOption和任何其他输出选项(-h、-t和-J)组成。 statOption必须在前面。  

输出被格式化为一个表，列用空格分隔。 带有标题的标题行描述列。 使用-h选项设置报头显示的频率。 列标头名称在不同选项之间是一致的。 通常，如果两个选项提供具有相同名称的列，那么这两个列的数据源是相同的。  

使用-t选项显示时间戳列，将Timestamp标记为输出的第一列。 Timestamp列包含自目标JVM启动以来经过的时间(以秒为单位)。 时间戳的分辨率取决于各种因素，并且由于在高负载系统上延迟的线程调度而变化。  

使用interval和count参数分别确定jstat命令显示输出的频率和次数。  

**注意：**

不要编写脚本来解析`jstat`命令的输出，因为格式可能会在未来的版本中发生变化。如果您编写解析`jstat`命令输出的脚本，那么希望针对此工具的未来版本修改它们。

```
-statOption
```

确定`jstat`命令显示的统计信息。下面列出了可用的选项。使用`-options`常规选项显示特定平台安装的选项列表。请参阅[统计选项和输出](https://dev.java/learn/jstat---monitoring-your-jvms-statistics/#stat-options-and-output)。

`class`: 显示有关类加载器行为的统计信息。

`compiler`：显示有关 Java HotSpot VM Just-in-Time 编译器行为的统计信息。

`gc`：显示有关垃圾收集堆行为的统计信息。

`gccapacity`: 显示有关世代及其对应空间的容量的统计信息。

`gccause`：显示有关垃圾收集统计信息的摘要（与 相同`-gcutil`），以及上次和当前（如果适用）垃圾收集事件的原因。

`gcnew`：显示有关新生代行为的统计信息。

`gcnewcapacity`: 显示新生代的大小及其对应空间的统计信息。

`gcold`: 显示有关老年代行为的统计信息和元空间统计信息。

`gcoldcapacity`: 显示老年代大小的统计信息。

`gcmetacapacity`：显示有关元空间大小的统计信息。

`gcutil`：显示有关垃圾收集统计信息的摘要。

`printcompilation`: 显示 Java HotSpot VM 编译方法统计信息。

`-J`*选项*

将*javaOption*传递给 Java 应用程序启动器。例如，`-J-Xms48m`将启动内存设置为 48 MB。有关选项的完整列表，请参阅[java](https://dev.java/learn/java---your-application-launcher/)。

 

## 统计选项和输出

以下信息总结了该`jstat`命令为每个*statOption*输出的列。

`-class` *选项*

类加载器统计信息。

```
    `Loaded`: Number of classes loaded.
    
    `Bytes`: Number of KB loaded.
    
    `Unloaded`: Number of classes unloaded.
    
    `Bytes`: Number of KB unloaded.
    
    `Time`: Time spent performing class loading and unloading operations.
```

`-compiler` *选项*

Java HotSpot VM 实时编译器统计信息。

```
    `Compiled`: Number of compilation tasks performed.
    
    `Failed`: Number of compilations tasks failed.
    
    `Invalid`: Number of compilation tasks that were invalidated.
    
    `Time`: Time spent performing compilation tasks.
    
    `FailedType`: Compile type of the last failed compilation.
    
    `FailedMethod`: Class name and method of the last failed compilation.
```

`-gc` *选项*

垃圾收集堆统计信息。

```
    `S0C`: Current survivor space 0 capacity (KB).
    
    `S1C`: Current survivor space 1 capacity (KB).
    
    `S0U`: Survivor space 0 utilization (KB).
    
    `S1U`: Survivor space 1 utilization (KB).
    
    `EC`: Current eden space capacity (KB).
    
    `EU`: Eden space utilization (KB).
    
    `OC`: Current old space capacity (KB).
    
    `OU`: Old space utilization (KB).
    
    `MC`: Metaspace Committed Size (KB).
    
    `MU`: Metaspace utilization (KB).
    
    `CCSC`: Compressed class committed size (KB).
    
    `CCSU`: Compressed class space used (KB).
    
    `YGC`: Number of young generation garbage collection (GC) events.
    
    `YGCT`: Young generation garbage collection time.
    
    `FGC`: Number of full GC events.
    
    `FGCT`: Full garbage collection time.
    
    `GCT`: Total garbage collection time.
```

`-gccapacity` *选项*

内存池生成和空间容量。

```
    `NGCMN`: Minimum new generation capacity (KB).
    
    `NGCMX`: Maximum new generation capacity (KB).
    
    `NGC`: Current new generation capacity (KB).
    
    `S0C`: Current survivor space 0 capacity (KB).
    
    `S1C`: Current survivor space 1 capacity (KB).
    
    `EC`: Current eden space capacity (KB).
    
    `OGCMN`: Minimum old generation capacity (KB).
    
    `OGCMX`: Maximum old generation capacity (KB).
    
    `OGC`: Current old generation capacity (KB).
    
    `OC`: Current old space capacity (KB).
    
    `MCMN`: Minimum metaspace capacity (KB).
    
    `MCMX`: Maximum metaspace capacity (KB).
    
    `MC`: Metaspace Committed Size (KB).
    
    `CCSMN`: Compressed class space minimum capacity (KB).
    
    `CCSMX`: Compressed class space maximum capacity (KB).
    
    `CCSC`: Compressed class committed size (KB).
    
    `YGC`: Number of young generation GC events.
    
    `FGC`: Number of full GC events.
```

`-gccause` *选项*

此选项显示与该选项相同的垃圾收集统计摘要`-gcutil`，但包括上次垃圾收集事件和（如果适用）当前垃圾收集事件的原因。除了为 列出的列之外`-gcutil`，此选项还添加了以下列：

```
    `LGCC`: Cause of last garbage collection
    
    `GCC`: Cause of current garbage collection
```

`-gcnew` *选项*

新生代统计数据。

```
    `S0C`: Current survivor space 0 capacity (KB).
    
    `S1C`: Current survivor space 1 capacity (KB).
    
    `S0U`: Survivor space 0 utilization (KB).
    
    `S1U`: Survivor space 1 utilization (KB).
    
    `TT`: Tenuring threshold.
    
    `MTT`: Maximum tenuring threshold.
    
    `DSS`: Desired survivor size (KB).
    
    `EC`: Current eden space capacity (KB).
    
    `EU`: Eden space utilization (KB).
    
    `YGC`: Number of young generation GC events.
    
    `YGCT`: Young generation garbage collection time.
```

`-gcnewcapacity` *选项*

新生代空间大小统计。

```
    `NGCMN`: Minimum new generation capacity (KB).
    
    `NGCMX`: Maximum new generation capacity (KB).
    
    `NGC`: Current new generation capacity (KB).
    
    `S0CMX`: Maximum survivor space 0 capacity (KB).
    
    `S0C`: Current survivor space 0 capacity (KB).
    
    `S1CMX`: Maximum survivor space 1 capacity (KB).
    
    `S1C`: Current survivor space 1 capacity (KB).
    
    `ECMX`: Maximum eden space capacity (KB).
    
    `EC`: Current eden space capacity (KB).
    
    `YGC`: Number of young generation GC events.
    
    `FGC`: Number of full GC events.
```

`-gcold` *选项*

老年代大小统计。

```
    `MC`: Metaspace Committed Size (KB).
    
    `MU`: Metaspace utilization (KB).
    
    `CCSC`: Compressed class committed size (KB).
    
    `CCSU`: Compressed class space used (KB).
    
    `OC`: Current old space capacity (KB).
    
    `OU`: Old space utilization (KB).
    
    `YGC`: Number of young generation GC events.
    
    `FGC`: Number of full GC events.
    
    `FGCT`: Full garbage collection time.
    
    `GCT`: Total garbage collection time.
```

`-gcoldcapacity` *选项*

老年代统计。

```
    `OGCMN`: Minimum old generation capacity (KB).
    
    `OGCMX`: Maximum old generation capacity (KB).
    
    `OGC`: Current old generation capacity (KB).
    
    `OC`: Current old space capacity (KB).
    
    `YGC`: Number of young generation GC events.
    
    `FGC`: Number of full GC events.
    
    `FGCT`: Full garbage collection time.
    
    `GCT`: Total garbage collection time.
```

`-gcmetacapacity` *选项*

元空间大小统计。

```
    `MCMN`: Minimum metaspace capacity (KB).
    
    `MCMX`: Maximum metaspace capacity (KB).
    
    `MC`: Metaspace Committed Size (KB).
    
    `CCSMN`: Compressed class space minimum capacity (KB).
    
    `CCSMX`: Compressed class space maximum capacity (KB).
    
    `YGC`: Number of young generation GC events.
    
    `FGC`: Number of full GC events.
    
    `FGCT`: Full garbage collection time.
    
    `GCT`: Total garbage collection time.
```

`-gcutil` *选项*

垃圾收集统计摘要。

```
    `S0`: Survivor space 0 utilization as a percentage of the space's current capacity.
    
    `S1`: Survivor space 1 utilization as a percentage of the space's current capacity.
    
    `E`: Eden space utilization as a percentage of the space's current capacity.
    
    `O`: Old space utilization as a percentage of the space's current capacity.
    
    `M`: Metaspace utilization as a percentage of the space's current capacity.
    
    `CCS`: Compressed class space utilization as a percentage.
    
    `YGC`: Number of young generation GC events.
    
    `YGCT`: Young generation garbage collection time.
    
    `FGC`: Number of full GC events.
    
    `FGCT`: Full garbage collection time.
    
    `GCT`: Total garbage collection time.
```

`-printcompilation` *选项*

Java HotSpot VM 编译器方法统计。

```
    `Compiled`: Number of compilation tasks performed by the most recently compiled method.
    
    `Size`: Number of bytes of byte code of the most recently compiled method.
    
    `Type`: Compilation type of the most recently compiled method.
    
    `Method`: Class name and method name identifying the most recently compiled method. Class name uses a slash (/) instead of a dot (.) as a name space separator. The method name is the method within the specified class. The format for these two fields is consistent with the HotSpot `-XX:+PrintCompilation` option.
```

 

## 虚拟机标识符

vmid字符串的语法对应于URI的语法:  

```shell
[protocol:][//]lvmid[@hostname[:port][/servername]
```

vmid字符串的语法对应于URI的语法。 vmid字符串可以是一个简单的整数(表示本地JVM)，也可以是一个更复杂的结构(指定通信协议、端口号和其他特定于实现的值)。  

*协议*

通信协议。如果省略*协议*值且未指定主机名，则默认协议是特定于平台的优化本地协议。如果省略*协议*值并指定主机名，则默认协议为`rmi`。

*lvmid*

目标JVM的本地虚拟机标识符。 lvmid是一个特定于平台的值，它唯一地标识系统上的JVM。 lvmid是虚拟机标识符的唯一必需组件。 lvmid通常(但不一定)是目标JVM进程的操作系统进程标识符。 如果JVM进程没有运行在单独的docker实例中，您可以使用jps命令来确定lvmid。 在Linux和OS X平台上，也可以通过ps命令来确定lvmid，在Windows上，可以通过Windows任务管理器来确定。  

*主机名*

指示目标主机的主机名或 IP 地址。如果省略*主机名*值，则目标主机是本地主机。

*端口*

与远程服务器通信的默认端口。如果省略*主机名*值或*协议*值指定优化的本地协议，则忽略*端口*值。否则，*端口*参数的处理是特定于实现的。对于默认`rmi`协议，端口值表示`rmiregistry`远程主机上的端口号。如果省略*端口*值且*协议*值指示`rmi`，则使用默认 rmiregistry 端口 (1099)。

*服务器名称*

*servername*参数的处理取决于实现。对于优化的本地协议，该字段将被忽略。对于`rmi`协议，它代表远程主机上 RMI 远程对象的名称。

 

## 例子

本节提供了一些监视*lvmid*为 21891的本地 JVM的示例。

 

## Gcutil 选项

此示例附加到 lvmid 21891 并以 250 毫秒的间隔采集 7 个样本并显示`-gcutil`选项指定的输出。

此示例的输出显示在第三个和第四个样本之间发生了年轻代收集。收集耗时0.078秒，将对象从伊甸空间（E）提升到旧空间（O），使得旧空间利用率从66.80%提升至68.19%。在收集之前，幸存者空间的利用率为 97.02%，但在此收集之后，它的利用率为 91.03%。

```shell
    jstat -gcutil 21891 250 7
      S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
      0.00  97.02  70.31  66.80  95.52  89.14      7    0.300     0    0.000    0.300
      0.00  97.02  86.23  66.80  95.52  89.14      7    0.300     0    0.000    0.300
      0.00  97.02  96.53  66.80  95.52  89.14      7    0.300     0    0.000    0.300
     91.03   0.00   1.98  68.19  95.89  91.24      8    0.378     0    0.000    0.378
     91.03   0.00  15.82  68.19  95.89  91.24      8    0.378     0    0.000    0.378
     91.03   0.00  17.80  68.19  95.89  91.24      8    0.378     0    0.000    0.378
     91.03   0.00  17.80  68.19  95.89  91.24      8    0.378     0    0.000    0.378
```



 

## 重复列标题字符串

此示例附加到 lvmid 21891 并以 250 毫秒的间隔采样并显示`-gcnew`选项指定的输出。此外，它使用`-h3`选项在每 3 行数据后输出列标题。

除了显示重复的标头字符串外，此示例还显示在第二个和第三个样本之间发生了年轻的 GC。其持续时间为 0.001 秒。收集发现足够的活动数据，幸存者空间 0 利用率 (S0U) 将超过所需的幸存者大小 (DSS)。结果，对象被提升到老年代（在这个输出中不可见），并且任期阈值（TT）从 31 降低到 2。

另一个收集发生在第五个和第六个样本之间。该集合发现的幸存者很少，并将任期阈值恢复为 31。

```shell
    jstat -gcnew -h3 21891 250
     S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT
      64.0   64.0    0.0   31.7 31  31   32.0    512.0    178.6    249    0.203
      64.0   64.0    0.0   31.7 31  31   32.0    512.0    355.5    249    0.203
      64.0   64.0   35.4    0.0  2  31   32.0    512.0     21.9    250    0.204
     S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT
      64.0   64.0   35.4    0.0  2  31   32.0    512.0    245.9    250    0.204
      64.0   64.0   35.4    0.0  2  31   32.0    512.0    421.1    250    0.204
      64.0   64.0    0.0   19.0 31  31   32.0    512.0     84.4    251    0.204
     S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT
      64.0   64.0    0.0   19.0 31  31   32.0    512.0    306.7    251    0.204
```



 

## 包括每个样本的时间戳

此示例附加到 lvmid 21891 并以 250 毫秒的间隔采集 3 个样本。该`-t`选项用于为第一列中的每个样本生成时间戳。

时间戳列报告自目标 JVM 启动以来经过的时间（以秒为单位）。此外，`-gcoldcapacity` 输出显示老年代容量（OGC）和老空间容量（OC）随着堆的扩展而增加以满足分配或提升需求。在第 81 次完全垃圾回收 (FGC) 之后，老年代容量 (OGC) 从 11,696 KB 增长到 13,820 KB。该代（和空间）的最大容量为 60,544 KB (OGCMX)，因此仍有扩展空间。

```shell
    Timestamp      OGCMN    OGCMX     OGC       OC       YGC   FGC    FGCT    GCT
              150.1   1408.0  60544.0  11696.0  11696.0   194    80    2.874   3.799
              150.4   1408.0  60544.0  13820.0  13820.0   194    81    2.938   3.863
              150.7   1408.0  60544.0  13820.0  13820.0   194    81    2.938   3.863
```



 

## 监控远程 JVM 的检测

此示例附加到`remote.domain`使用该`-gcutil`选项命名的系统上的 lvmid 40496 ，无限期地每秒采集一次样本。

lvmid 与远程主机的名称组合以构建`[[email protected]](/cdn-cgi/l/email-protection)`. 此 vmid 导致使用该`rmi`协议`jstatd`与远程主机上的默认服务器进行通信。该`jstatd`服务器是使用位于`rmiregistry`上命令`remote.domain`绑定的默认端口的`rmiregistry`命令（端口1099）。

```shell
jstat -gcutil [email protected] 1000
  ... output omitted
```