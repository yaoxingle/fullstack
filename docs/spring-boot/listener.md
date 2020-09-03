一、java的的监听机制，其实是对Java提供的时间监听机制的封装。

Java中的事件监听机制定义了以下几个角色

1、事件：Event 继承java.util.EventObject类的对象

2、事件源：Source 任意对象Object

3、监听器：Listener 实现java.util.EventListener接口的对象

springBoot监听器

二、SpringBoot在项目启动时，会对几个监听器进行回调，我们可以实现这些监听器接口，在项目启动时完成一些操作。

一共有四种实现方法：

- ApplicationContextInitializer

- SpringApplicationRunListener

- CommandLineRunner

- ApplicationRunner 