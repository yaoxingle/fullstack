#### 1、程序计数器（The pc Register ） 

由于 Java 虚拟机的多线程是通过线程轮流切换并分配处理器执行时间的方式来实现的，在任何一个确定的时刻，一个处理器内核都只会执行一条线程中的指令。因此，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各条线程之间计数器互不影响，独立存储。

#### 2、 虚拟机栈（Java Virtual Machine Stacks） 

用于描述java方法执行的内存模型。每个java方法在执行时，会创建一个“栈帧（stack frame）”，栈帧的结构分为“<span style="background-color:#FF6600">局部变量表</span>、<span style="background-color:#FF6600">操作数栈</span>、<span style="background-color:#FF6600">动态链接</span>、<span style="background-color:#FF6600">方法出口</span>几个部分，每一个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。

##### 2.1、局部变量表

是一组变量值存储空间，用于存放方法参数和方法内部定义的局部变量。并且在Java编译为Class文件时，就已经确定了该方法所需要分配的局部变量表的最大容量
2.局部变量表存放了编译期可知的各种基本数据类型(boolean、byte、char、short、int、float、long、double)「String是引用类型」，
对象引用(reference类型) 和 returnAddress类型（它指向了一条字节码指令的地址）
局部变量表的容量以<span style="background-color:#FF6600">变量槽</span>为最小单位，每个变量槽都可以存储<span style="background-color:#FF6600">32位</span>长度的内存空间

##### 2.2、操作数栈



##### 2.3、动态链接



##### 2.4、方法出口



#### 3、 堆（Heap）

Java 虚拟机所管理的内存中最大的一块。Java 堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。

#### 4、 方法区 （Method Area）或称为永久带

与 Java 堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。



##### 5、本地方法栈 （Native Method Stacks）

为虚拟机使用到Native方法服务