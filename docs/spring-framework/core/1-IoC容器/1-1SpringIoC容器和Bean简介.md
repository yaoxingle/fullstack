### 1.1. Spring IoC Container 和 Bean 介绍

本章涵盖了控制反转 (IoC) 原则的 Spring Framework 实现。IoC 也称为依赖注入 (DI)。这是一个过程，其中对象仅通过构造函数参数、工厂方法的参数或在对象实例被构造或从工厂方法返回后在对象实例上设置的属性来定义它们的依赖项（即，它们使用的其他对象） . 然后容器在创建 bean 时注入这些依赖项。这个过程基本上是 bean 本身的逆过程（因此得名，控制反转），通过使用类的直接构造或诸如服务定位器模式之类的机制来控制其依赖项的实例化或位置。

在`org.springframework.beans`和`org.springframework.context`包是Spring框架的IoC容器的基础。该 [`BeanFactory`](https://docs.spring.io/spring-framework/docs/5.3.12/javadoc-api/org/springframework/beans/factory/BeanFactory.html) 接口提供了一种能够管理任何类型对象的高级配置机制。 [`ApplicationContext`](https://docs.spring.io/spring-framework/docs/5.3.12/javadoc-api/org/springframework/context/ApplicationContext.html) 是 的子接口`BeanFactory`。它补充说：

- 更容易与 Spring 的 AOP 特性集成
- 消息资源处理（用于国际化）
- 活动发布
- 应用层特定的上下文，例如`WebApplicationContext` 在 Web 应用程序中使用的 。

简而言之，它`BeanFactory`提供了配置框架和基本功能，并`ApplicationContext`添加了更多企业特定的功能。该`ApplicationContext`是对一个完整的超集`BeanFactory`，并在Spring的IoC容器的描述本章独占使用。有关使用的详细信息`BeanFactory`，而不是`ApplicationContext,`看到 [的`BeanFactory`](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-beanfactory)。

在 Spring 中，构成应用程序主干并由 Spring IoC 容器管理的对象称为 bean。bean 是由 Spring IoC 容器实例化、组装和管理的对象。否则，bean 只是应用程序中众多对象之一。Bean 以及它们之间的依赖关系反映在容器使用的配置元数据中。

