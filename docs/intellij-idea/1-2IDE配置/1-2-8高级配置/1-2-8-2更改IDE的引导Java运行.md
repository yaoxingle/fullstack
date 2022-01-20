作为 Java 应用程序，IntelliJ IDEA 包含默认使用的 JetBrains Runtime（基于 OpenJDK 11）。建议使用 JetBrains Runtime 运行 IntelliJ IDEA，它修复了各种已知的 OpenJDK 和 Oracle JDK 错误，并提供更好的性能和稳定性。但是，在某些情况下，您可能需要使用另一个 Java 运行时或特定版本的 JetBrains Runtime。

> 更改运行时可能会导致意外问题。除非[JetBrains 支持](https://www.jetbrains.com/help/idea/2021.1/getting-started.html)人员明确要求您这样做，否则请勿更改它。

### 切换用于运行 IntelliJ IDEA 的 Java 运行时﻿

1. 从主菜单中，选择**Help | Find Action** 或按Ctrl+Shift+A。

2. 查找并选择**Choose Boot Java Runtime for the IDE**。

3. 选择所需的运行时并单击**OK**。

   如有必要，您可以更改 IntelliJ IDEA 将下载所选运行时的位置。

4. 等待 IntelliJ IDEA 使用新的运行时重新启动。

首次打开**为 IDE 选择引导运行时**对话框时，可能需要一段时间才能从服务器加载 JetBrains 运行时构建列表。

要使用计算机上可用的不同 Java 运行时，请选择**Advanced**下的**Add Custom Runtime**。IntelliJ IDEA 列出了它能够检测到的所有 JDK 和 JRE。或者，单击**Add JDK**以指定所需 Java 主目录的位置。

要重置回 IDE 最初**使用的默认运行时**，请单击**Use Default Runtime**。

所选运行时的路径存储在IntelliJ IDEA[配置目录](https://www.jetbrains.com/help/idea/2021.1/tuning-the-ide.html#config-directory)中的**idea.jdk**或**idea64.jdk**文件中。如果所选运行时有问题，您可以删除此文件以恢复为默认运行时。

您还可以通过添加带有所需JDK主目录路径的IDEA_JDK环境变量来覆盖IntelliJ IDEA使用的运行时。  