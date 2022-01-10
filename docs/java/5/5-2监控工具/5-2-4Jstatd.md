[jstatd](https://docs.oracle.com/en/java/javase/16/docs/specs/man/jstatd.html) - 监视检测的 Java HotSpot VM 的创建和终止

 

## 概要

**注意：**此命令是实验性的，不受支持。

```shell
jstatd [options]
```

*选项*

这代表`jstatd`命令行选项。请参阅[jstatd 命令的选项](https://dev.java/learn/jstatd---monitoring-the-creation-and-termination-of-your-jvms/#options-for-the-jstatd-command)。

 

## 描述

jstatd命令是一个RMI服务器应用程序，它监视被监视的Java HotSpot虚拟机的创建和终止，并提供一个接口，使远程监视工具jstat和jps能够连接到运行在本地主机上的JVM，并收集关于JVM进程的信息。  

jstatd服务器需要本地主机上的RMI注册表。 jstatd服务器尝试连接到默认端口上的RMI注册表，或者使用-p port选项指定的端口上。 如果没有找到RMI注册表，则在jstatd应用程序中创建一个，该应用程序绑定到由-p port选项指示的端口，或者在省略-p port选项时绑定到默认的RMI注册表端口。 通过指定-nr选项，可以停止创建内部RMI注册表。

 

## Jstatd 命令的选项

```
-nr
```

`jstatd`当找不到现有 RMI 注册表时，此选项不会尝试在进程内创建内部 RMI 注册表。

`-p` *port*

此选项设置希望找到 RMI 注册表的端口号，或者在未找到时，如果`-nr`未指定该选项，则创建该端口号。

`-r` *rmi端口*

此选项设置RMI连接器绑定的端口号。 如果未指定，则使用随机可用端口。  

`-n` *rmi名称*

这个选项设置远程RMI对象在RMI注册表中绑定的名称。默认名称为JStatRemoteHost。如果在同一主机上启动多个jstatd服务器，那么可以通过指定这个选项使每个服务器导出的RMI对象的名称惟一。但是，这样做需要在监视客户机的hostid和vmid字符串中包含唯一的服务器名称。

`-J`*选项*

该选项将一个Java选项传递给JVM，该选项是Java应用程序启动器参考页面中描述的选项之一。例如，-J-Xms48m设置启动内存为48mb。 

## 安全

jstatd服务器只能监视它具有适当本机访问权限的jvm。因此，jstatd进程必须使用与目标jvm相同的用户凭据运行。一些用户凭证，例如Linux和OS X操作系统中的root用户，具有访问系统上任何JVM导出的工具的权限。使用这种凭据运行的jstatd进程可以监视系统上的任何JVM，但会引入额外的安全问题。

jstatd服务器不提供任何远程客户端的身份验证。因此，运行jstatd服务器进程将jstatd进程具有访问权限的所有jvm的检测导出公开给网络上的任何用户。这种公开在您的环境中可能是不希望看到的，因此，在启动jstatd进程之前应该考虑本地安全策略，特别是在不安全的生产环境或网络中。

当没有安装其他安全管理器时，jstatd服务器安装一个RMISecurityPolicy实例，因此需要指定一个安全策略文件。策略文件必须符合“默认策略实现”和“策略文件语法”。

如果您的安全问题不能通过定制的策略文件来解决，那么最安全的操作是不运行jstatd服务器，而是在本地使用jstat和jps工具。然而，当使用jps来获取被检测jvm的列表时，该列表将不包括任何运行在docker容器中的jvm。

## 远程接口

jstatd进程导出的接口是私有的，保证可以更改。不鼓励用户和开发人员编写此接口。 

## 例子

以下是该`jstatd`命令的示例。该`jstatd`脚本会自动在后台启动服务器。

### 内部 RMI 注册表

此示例显示如何`jstatd`使用内部 RMI 注册表启动会话。此示例假定没有其他服务器绑定到默认 RMI 注册表端口（端口`1099`）。

```shell
jstatd -J-Djava.security.policy=all.policy
```

### 外部 RMI 注册表

此示例`jstatd`使用外部 RMI 注册表启动会话。

```shell
rmiregistry&
jstatd -J-Djava.security.policy=all.policy
```

本示例`jstatd`在端口 上启动与外部 RMI 注册服务器的会话`2020`。

```shell
jrmiregistry 2020&
jstatd -J-Djava.security.policy=all.policy -p 2020
```

此示例`jstatd`在端口`2020`和绑定到端口的 JMX 连接器上启动与外部 RMI 注册服务器的会话`2021`。

```shell
jrmiregistry 2020&
jstatd -J-Djava.security.policy=all.policy -p 2020 -r 2021
```

此示例`jstatd`在绑定到 的端口 2020 上启动与外部 RMI 注册表的会话`AlternateJstatdServerName`。

```shell
rmiregistry 2020&
jstatd -J-Djava.security.policy=all.policy -p 2020 -n AlternateJstatdServerName
```

### 停止创建进程内 RMI 注册表

此示例启动一个`jstatd`会话，该会话在未找到 RMI 注册表时不创建该注册表。此示例假定 RMI 注册表已在运行。如果 RMI 注册表未运行，则会显示错误消息。

```shell
jstatd -J-Djava.security.policy=all.policy -nr
```

### 启用 RMI 日志记录

此示例在`jstatd`启用 RMI 日志记录功能的情况下启动会话。此技术可用作故障排除帮助或监视服务器活动。

```shell
jstatd -J-Djava.security.policy=all.policy -J-Djava.rmi.server.logCalls=true
```