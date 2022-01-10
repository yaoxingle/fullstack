[jconsole](https://docs.oracle.com/en/java/javase/16/docs/specs/man/jconsole.html) - 启动图形控制台来监视和管理 Java 应用程序

 

## 概要

```shell
jconsole [-interval=n] [-notile] [-plugin path] [-version] [connection ... ] [-Jinput_arguments]

jconsole -help
```



## 选项

```
-interval
```

将更新间隔设置为`n`秒（默认为 4 秒）。

```
-notile
```

不为两个或多个连接平铺窗户。

`-pluginpath` **path**

指定jconsole用于查找插件的路径。 插件路径应该包含一个名为META-INF/services/com.sun.tools.jconsole的提供程序配置文件。 包含每个插件一行的JConsolePlugin。 这一行指定了实现com.sun.tools.jconsole.JConsolePlugin类的类的完全限定类名。  

```
-version
```

打印程序版本。

*connection* = *pid* | *host*`:`*port* | *jmxURL*

连接可以用pid、host:port或jmxURL来描述。  

- pid值为目标进程的进程ID。 JVM运行的用户ID必须与运行jconsole命令的用户ID相同。  

- “host:port”值是JVM运行的主机系统的名称，以及JVM启动时由系统属性com.sun.management.jmxremote.port指定的端口号。  

- jmxUrl值是要连接到的JMX代理的地址，如JMXServiceURL中所述。  

`-J`*输入参数*

将*input_arguments*传递给`jconsole`运行命令的 JVM 。

```
-help` 或者 `--help
```

显示命令的帮助消息。

 

## 描述

jconsole命令启动一个图形控制台工具，允许您监视和管理本地或远程机器上的Java应用程序和虚拟机。  

在Windows上，jconsole命令不与控制台窗口关联。 但是，当jconsole命令失败时，它会显示一个包含错误信息的对话框。  