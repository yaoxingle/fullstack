### 1.2. 容器概述

该`org.springframework.context.ApplicationContext`接口代表 Spring IoC 容器，负责实例化、配置和组装 bean。容器通过读取配置元数据来获取有关要实例化、配置和组装哪些对象的指令。配置元数据以 XML、Java 注释或 Java 代码表示。它可以让您表达组成应用程序的对象以及这些对象之间丰富的相互依赖关系。

`ApplicationContext`Spring 提供了该接口的几种实现。在独立应用程序中，通常创建[`ClassPathXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/5.3.12/javadoc-api/org/springframework/context/support/ClassPathXmlApplicationContext.html) 或的实例 [`FileSystemXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/5.3.12/javadoc-api/org/springframework/context/support/FileSystemXmlApplicationContext.html)。虽然 XML 一直是定义配置元数据的传统格式，但您可以通过提供少量 XML 配置来声明性地启用对这些附加元数据格式的支持，从而指示容器使用 Java 注释或代码作为元数据格式。

在大多数应用场景中，不需要显式的用户代码来实例化一个或多个 Spring IoC 容器实例。例如，在 Web 应用程序场景中，应用程序文件中的简单八行（左右）样板 Web 描述符 XML`web.xml`通常就足够了（请参阅[Web 应用程序的便捷 ApplicationContext 实例化](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#context-create)）。如果您使用 [Spring Tools for Eclipse](https://spring.io/tools)（一个 Eclipse 驱动的开发环境），您可以通过点击几下鼠标或按键轻松创建这个样板配置。

下图显示了 Spring 如何工作的高级视图。您的应用程序类与配置元数据相结合，因此，在`ApplicationContext`创建和初始化之后，您就有了一个完全配置且可执行的系统或应用程序。

![container magic](1-2Container介绍.assets/container-magic.png)

图 1. Spring IoC 容器

#### 1.2.1. 配置元数据

如上图所示，Spring IoC 容器使用一种形式的配置元数据。此配置元数据表示您作为应用程序开发人员如何告诉 Spring 容器实例化、配置和组装应用程序中的对象。

配置元数据传统上以简单直观的 XML 格式提供，本章的大部分内容使用这种格式来传达 Spring IoC 容器的关键概念和特性。

|      | 基于 XML 的元数据并不是唯一允许的配置元数据形式。Spring IoC 容器本身与实际写入此配置元数据的格式完全分离。现在，许多开发人员为他们的 Spring 应用程序选择 [基于 Java 的配置](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java)。 |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

有关在 Spring 容器中使用其他形式的元数据的信息，请参阅：

- [基于注解的配置](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-annotation-config)：Spring 2.5 引入了对基于注解的配置元数据的支持。
- [基于 Java 的配置](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java)：从 Spring 3.0 开始，Spring JavaConfig 项目提供的许多特性成为核心 Spring Framework 的一部分。因此，您可以使用 Java 而不是 XML 文件来定义应用程序类外部的 bean。要使用这些新功能，请参阅 [`@Configuration`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html)， [`@Bean`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html)， [`@Import`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Import.html)，和[`@DependsOn`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/DependsOn.html)注释。

Spring 配置包含至少一个并且通常不止一个容器必须管理的 bean 定义。基于 XML 的配置元数据将这些 bean 配置为`<bean/>`顶级`<beans/>`元素中的元素。Java 配置通常`@Bean`在`@Configuration`类中使用-annotated 方法。

这些 bean 定义对应于构成应用程序的实际对象。通常，您定义服务层对象、数据访问对象 (DAO)、表示对象（例如 Struts`Action`实例）、基础设施对象（例如 Hibernate `SessionFactories`、JMS`Queues`等）。通常，不会在容器中配置细粒度的域对象，因为创建和加载域对象通常是 DAO 和业务逻辑的责任。但是，您可以使用 Spring 与 AspectJ 的集成来配置在 IoC 容器控制之外创建的对象。请参阅[使用 AspectJ 通过 Spring 依赖注入域对象](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-atconfigurable)。

以下示例显示了基于 XML 的配置元数据的基本结构：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">  
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```

|      | 该`id`属性是一个字符串，用于标识单个 bean 定义。    |
| ---- | --------------------------------------------------- |
|      | 该`class`属性定义 bean 的类型并使用完全限定的类名。 |

该`id`属性的值是指协作对象。此示例中未显示用于引用协作对象的 XML。有关更多信息，请参阅 [依赖项](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-dependencies)。

#### 1.2.2. 实例化一个容器

提供给`ApplicationContext`构造函数的一个或多个位置路径是资源字符串，允许容器从各种外部资源（例如本地文件系统、Java 等）加载配置元数据`CLASSPATH`。

Java



```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

|      | 在了解了 Spring 的 IoC 容器之后，您可能想了解更多关于 Spring 的 `Resource`抽象（如[参考资料中所述](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources)），它提供了一种从 URI 语法中定义的位置读取 InputStream 的便捷机制。特别是， `Resource`路径用于构造应用程序上下文，如[应用程序上下文和资源路径中所述](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources-app-ctx)。 |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

以下示例显示了服务层对象`(services.xml)`配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```

以下示例显示了数据访问对象`daos.xml`文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao"
        class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for data access objects go here -->

</beans>
```

在前面的示例中，服务层由`PetStoreServiceImpl`类和两个类型`JpaAccountDao`和`JpaItemDao`（基于 JPA 对象-关系映射标准）的数据访问对象组成。该`property name`元素是指JavaBean属性的名称，以及`ref`元素指的是另一个bean定义的名称。`id`和`ref`元素之间的这种联系表达了协作对象之间的依赖关系。有关配置对象依赖项的详细信息，请参阅 [依赖项](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-dependencies)。

##### 编写基于 XML 的配置元数据

让 bean 定义跨越多个 XML 文件会很有用。通常，每个单独的 XML 配置文件都代表您架构中的一个逻辑层或模块。

您可以使用应用程序上下文构造函数从所有这些 XML 片段加载 bean 定义。该构造函数采用多个`Resource`位置，如上[一节](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-instantiation)所示 。或者，使用一个或多个`<import/>`元素从另一个文件或多个文件加载 bean 定义。以下示例显示了如何执行此操作：

```xml
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```

在前面的例子中，外部豆定义是从三个文件加载： `services.xml`，`messageSource.xml`，和`themeSource.xml`。所有位置路径都相对于执行导入的定义文件，因此`services.xml`必须与执行导入的文件位于同一目录或类路径位置， `messageSource.xml`并且`themeSource.xml`必须位于`resources`导入文件所在位置下方的位置。如您所见，前导斜杠被忽略。但是，鉴于这些路径是相对的，最好根本不使用斜杠。`<beans/>`根据 Spring Schema，被导入文件的内容，包括顶级元素，必须是有效的 XML bean 定义。

|      | 可以但不建议使用相对“../”路径引用父目录中的文件。这样做会创建对当前应用程序之外的文件的依赖关系。特别是，不建议将此引用用于`classpath:`URL（例如，`classpath:../services.xml`），其中运行时解析过程选择“最近的”类路径根，然后查看其父目录。类路径配置更改可能会导致选择不同的、不正确的目录。您始终可以使用完全限定的资源位置而不是相对路径：例如，`file:C:/config/services.xml`或`classpath:/config/services.xml`。但是，请注意您将应用程序的配置耦合到特定的绝对位置。通常最好为这样的绝对位置保留一个间接地址——例如，通过在运行时根据 JVM 系统属性解析的“${… }”占位符。 |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

命名空间本身提供了导入指令功能。Spring 提供的一系列 XML 命名空间中提供了超出普通 bean 定义的更多配置功能——例如，`context`和`util`命名空间。

##### Groovy Bean 定义 DSL

作为外部化配置元数据的另一个示例，bean 定义也可以在 Spring 的 Groovy Bean Definition DSL 中表达，正如 Grails 框架中所知。通常，此类配置位于“.groovy”文件中，其结构如下例所示：

```groovy
beans {
    dataSource(BasicDataSource) {
        driverClassName = "org.hsqldb.jdbcDriver"
        url = "jdbc:hsqldb:mem:grailsDB"
        username = "sa"
        password = ""
        settings = [mynew:"setting"]
    }
    sessionFactory(SessionFactory) {
        dataSource = dataSource
    }
    myService(MyService) {
        nestedBean = { AnotherBean bean ->
            dataSource = dataSource
        }
    }
}
```

这种配置风格在很大程度上等同于 XML bean 定义，甚至支持 Spring 的 XML 配置命名空间。它还允许通过`importBeans`指令导入 XML bean 定义文件。

#### 1.2.3. 使用容器

这`ApplicationContext`是一个高级工厂的接口，能够维护不同 bean 及其依赖项的注册表。通过使用 方法 `T getBean(String name, Class<T> requiredType)`，您可以检索 bean 的实例。

将`ApplicationContext`让你读bean定义和访问它们，如下例所示：

Java



```java
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```

使用 Groovy 配置，引导看起来非常相似。它有一个不同的上下文实现类，它是 Groovy 感知的（但也理解 XML bean 定义）。以下示例显示了 Groovy 配置：

Java



```java
ApplicationContext context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```

最灵活的变体是`GenericApplicationContext`结合读者委托——例如，结合`XmlBeanDefinitionReader`for XML 文件，如下例所示：

Java

```java
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
context.refresh();
```

您还可以使用`GroovyBeanDefinitionReader`for Groovy 文件，如以下示例所示：

Java

```java
GenericApplicationContext context = new GenericApplicationContext();
new GroovyBeanDefinitionReader(context).loadBeanDefinitions("services.groovy", "daos.groovy");
context.refresh();
```

您可以在同一个 上混合和匹配此类读取器委托`ApplicationContext`，从不同的配置源读取 bean 定义。

然后，您可以使用它`getBean`来检索 bean 的实例。该`ApplicationContext` 接口还有一些其他方法来检索 bean，但理想情况下，您的应用程序代码永远不应该使用它们。实际上，您的应用程序代码根本不应该调用该 `getBean()`方法，因此完全不依赖 Spring API。例如，Spring 与 Web 框架的集成为各种 Web 框架组件（例如控制器和 JSF 管理的 bean）提供了依赖注入，让您可以通过元数据（例如自动装配注释）声明对特定 bean 的依赖。