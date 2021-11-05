### 1.3. Bean概述

Spring IoC 容器管理一个或多个 bean。这些 bean 是使用您提供给容器的配置元数据创建的（例如，以 XML`<bean/>`定义的形式 ）。

在容器本身内，这些 bean 定义表示为`BeanDefinition` 对象，其中包含（除其他信息外）以下元数据：

- 包限定的类名：通常是定义的 bean 的实际实现类。
- Bean 行为配置元素，它说明 Bean 在容器中的行为方式（范围、生命周期回调等）。
- 对 bean 执行其工作所需的其他 bean 的引用。这些引用也称为协作者或依赖项。
- 在新创建的对象中设置的其他配置设置——例如，池的大小限制或在管理连接池的 bean 中使用的连接数。

此元数据转换为组成每个 bean 定义的一组属性。下表描述了这些属性：

| 属性                     | 更多解释说明                                                 |
| :----------------------- | :----------------------------------------------------------- |
| Class                    | [实例化 Bean](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-class) |
| Name                     | [命名 Bean](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-beanname) |
| Scope                    | [Bean 作用域](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-scopes) |
| Constructor arguments    | [依赖注入](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-collaborators) |
| Properties               | [依赖注入](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-collaborators) |
| Autowiring mode          | [自动装配合作者](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-autowire) |
| Lazy initialization mode | [延迟初始化的 Bean](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-lazy-init) |
| Initialization method    | [初始化回调](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-lifecycle-initializingbean) |
| Destruction method       | [销毁回调](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-lifecycle-disposablebean) |

除了包含有关如何创建特定 bean 的信息的 bean 定义之外，`ApplicationContext`实现还允许注册在容器外（由用户）创建的现有对象。这是通过`getBeanFactory()`返回 BeanFactory`DefaultListableBeanFactory`实现的方法访问 ApplicationContext 的 BeanFactory 来完成的。`DefaultListableBeanFactory` 通过`registerSingleton(..)`和 `registerBeanDefinition(..)`方法支持此注册。但是，典型的应用程序仅使用通过常规 bean 定义元数据定义的 bean。

|      | Bean 元数据和手动提供的单例实例需要尽早注册，以便容器在自动装配和其他内省步骤中正确推理它们。虽然在某种程度上支持覆盖现有元数据和现有单例实例，但官方不支持在运行时注册新 bean（与实时访问工厂同时），并可能导致并发访问异常、bean 容器中的状态不一致，或两个都。 |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

#### 1.3.1. 命名 Bean

每个 bean 都有一个或多个标识符。这些标识符在承载 bean 的容器中必须是唯一的。一个 bean 通常只有一个标识符。但是，如果它需要多个，则可以将多余的视为别名。

在基于 XML 的配置元数据中，您可以使用`id`属性、`name`属性或两者来指定 bean 标识符。该`id`属性允许您指定一个 ID。通常，这些名称是字母数字（'myBean'、'someService' 等），但它们也可以包含特殊字符。如果要为 bean 引入其他别名，也可以在`name` 属性中指定它们，用逗号 ( `,`)、分号 ( `;`) 或空格分隔。作为历史记录，在 Spring 3.1 之前的版本中，`id`属性被定义为一种`xsd:ID`类型，它限制了可能的字符。从 3.1 开始，它被定义为一种`xsd:string`类型。请注意，bean 的`id`唯一性仍然由容器强制执行，但不再由 XML 解析器强制执行。

您不需要为 bean提供 a`name`或 an `id`。如果您没有明确提供 a `name`或`id`，则容器会为该 bean 生成一个唯一的名称。但是，如果您想通过名称引用该 bean，通过使用`ref`元素或服务定位器样式查找，您必须提供名称。不提供名称的动机与使用[内部 bean](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-inner-beans)和[自动装配协作者有关](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-autowire)。

​																							Bean 命名约定

约定是在命名 bean 时对实例字段名称使用标准 Java 约定。也就是说，bean 名称以小写字母开头，并从那里开始使用驼峰式大小写。此类名称的示例包括`accountManager`、 `accountService`、`userDao`、`loginController`等等。

始终如一地命名 bean 使您的配置更易于阅读和理解。此外，如果您使用 Spring AOP，则在将建议应用于一组按名称相关的 bean 时会很有帮助。

|      | 通过类路径中的组件扫描，Spring 为未命名的组件生成 bean 名称，遵循前面描述的规则：本质上，采用简单的类名并将其初始字符转换为小写。然而，在有多个字符且第一个和第二个字符都是大写的（不寻常的）特殊情况下，原始大小写被保留。这些与定义的规则相同`java.beans.Introspector.decapitalize`（Spring 在这里使用）。 |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

##### 在 Bean 定义之外给 Bean 取别名

在 bean 定义本身中，您可以通过使用`id`属性指定的最多一个名称和属性中任意数量的其他名称的组合，为 bean 提供多个名称`name`。这些名称可以是同一个 bean 的等效别名，并且在某些情况下很有用，例如让应用程序中的每个组件通过使用特定于该组件本身的 bean 名称来引用公共依赖项。

然而，在实际定义 bean 的地方指定所有别名并不总是足够的。有时需要为在别处定义的 bean 引入别名。这在大型系统中很常见，其中配置在每个子系统之间拆分，每个子系统都有自己的一组对象定义。在基于 XML 的配置元数据中，您可以使用`<alias/>`元素来完成此操作。以下示例显示了如何执行此操作：

```xml
<alias name="fromName" alias="toName"/>
```

在这种情况下，命名的 bean（在同一容器中）`fromName`也可以在使用此别名定义后称为`toName`.

例如，子系统 A 的配置元数据可能引用名为 的数据源`subsystemA-dataSource`。子系统 B 的配置元数据可以通过名称来引用数据源`subsystemB-dataSource`。在组合使用这两个子系统的主应用程序时，主应用程序通过名称引用 DataSource `myApp-dataSource`。要让所有三个名称都引用同一个对象，您可以将以下别名定义添加到配置元数据中：

```xml
<alias name="myApp-dataSource" alias="subsystemA-dataSource"/>
<alias name="myApp-dataSource" alias="subsystemB-dataSource"/>
```

现在，每个组件和主应用程序都可以通过一个唯一的名称来引用 dataSource，并且保证不会与任何其他定义发生冲突（有效地创建一个命名空间），但它们引用的是同一个 bean。

Java-配置

如果您使用 Javaconfiguration，则该`@Bean`注解可用于提供别名。有关详细信息，请参阅[使用`@Bean`注释](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-java-bean-annotation)。

#### 1.3.2. 实例化 Bean

bean 定义本质上是创建一个或多个对象的方法。当被询问时，容器会查看命名 bean 的配方，并使用该 bean 定义封装的配置元数据来创建（或获取）实际对象。

如果使用基于 XML 的配置元数据，则指定要在元素的`class`属性中实例化的对象的类型（或类）`<bean/>`。这个 `class`属性（在内部，它是`Class`一个`BeanDefinition` 实例的属性）通常是强制性的。（对于例外情况，请参阅 [使用实例工厂方法](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-class-instance-factory-method)和[Bean 定义继承](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-child-bean-definitions)[进行实例化](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-class-instance-factory-method)。）您可以通过以下`Class`两种方式之一使用该属性：

- 通常，在容器本身通过反射调用其构造函数直接创建 bean 的情况下，指定要构造的 bean 类，有点等同于带有`new`运算符的Java 代码。
- 指定包含`static`被调用以创建对象的工厂方法的实际类，在不太常见的情况下，容器调用 `static`类上的工厂方法来创建 bean。调用`static`工厂方法返回的对象类型可能是同一个类，也可能完全是另一个类。

嵌套类名

如果要为嵌套类配置 bean 定义，可以使用嵌套类的二进制名称或源名称。

例如，如果您`SomeThing`在`com.example`包中调用了一个类，并且`SomeThing`该类有一个`static`名为的嵌套类`OtherThing`，则它们之间可以用美元符号 ( `$`) 或点 ( `.`)分隔。因此`class`bean 定义中的属性值将是`com.example.SomeThing$OtherThing`or `com.example.SomeThing.OtherThing`。

##### 使用构造函数实例化

当您通过构造函数方法创建 bean 时，所有普通类都可以被 Spring 使用并与 Spring 兼容。也就是说，正在开发的类不需要实现任何特定的接口或以特定的方式进行编码。只需指定 bean 类就足够了。但是，根据您对该特定 bean 使用的 IoC 类型，您可能需要一个默认（空）构造函数。

Spring IoC 容器几乎可以管理您希望它管理的任何类。它不仅限于管理真正的 JavaBean。大多数 Spring 用户更喜欢实际的 JavaBeans，它只有一个默认（无参数）构造函数和适当的 setter 和 getter，它们以容器中的属性为模型。您还可以在您的容器中拥有更多异国情调的非 bean 风格的类。例如，如果您需要使用绝对不符合 JavaBean 规范的遗留连接池，Spring 也可以管理它。

使用基于 XML 的配置元数据，您可以按如下方式指定 bean 类：

```xml
<bean id="exampleBean" class="examples.ExampleBean"/>

<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
```

有关向构造函数提供参数（如果需要）和在构造对象后设置对象实例属性的机制的详细信息，请参阅 [注入依赖项](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-collaborators)。

##### 使用静态工厂方法实例化

在定义使用静态工厂方法创建的 bean 时，使用`class` 属性来指定包含`static`工厂方法的类和命名`factory-method`为指定工厂方法本身名称的属性。您应该能够调用此方法（带有可选参数，如下所述）并返回一个活动对象，随后将其视为通过构造函数创建的。这种 bean 定义的一种用途是`static`在遗留代码中调用工厂。

以下 bean 定义指定通过调用工厂方法来创建 bean。定义中没有指定返回对象的类型（类），只指定包含工厂方法的类。在这个例子中，`createInstance()` 方法必须是静态方法。以下示例显示了如何指定工厂方法：

```xml
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```

以下示例显示了一个可以与前面的 bean 定义一起使用的类：

Java

```java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```

有关在工厂返回对象后向工厂方法提供（可选）参数和设置对象实例属性的机制的详细信息，请参阅[详细依赖项和配置](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-properties-detailed)。

##### 使用实例工厂方法实例化

与通过[静态工厂方法](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-class-static-factory-method)实例化类似，使用实例[工厂方法](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-class-static-factory-method)实例化从容器中调用现有 bean 的非静态方法来创建新 bean。要使用此机制，请将`class`属性留空，并在`factory-bean`属性中指定当前（或父或祖先）容器中 bean 的名称，该容器包含要调用以创建对象的实例方法。使用`factory-method`属性设置工厂方法本身的名称。以下示例显示了如何配置此类 bean：

```xml
<!-- the factory bean, which contains a method called createInstance() -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<!-- the bean to be created via the factory bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```

以下示例显示了相应的类：

Java

```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }
}
```

一个工厂类也可以包含多个工厂方法，如下例所示：

```xml
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>

<bean id="accountService"
    factory-bean="serviceLocator"
    factory-method="createAccountServiceInstance"/>
```

以下示例显示了相应的类：

Java

```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    private static AccountService accountService = new AccountServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }

    public AccountService createAccountServiceInstance() {
        return accountService;
    }
}
```

这种方法表明工厂 bean 本身可以通过依赖注入 (DI) 进行管理和配置。请参阅详细的[依赖关系和配置](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-properties-detailed)。

|      | 在 Spring 文档中，“工厂 bean”是指在 Spring 容器中配置并通过[实例](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-class-instance-factory-method)或 [静态](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-class-static-factory-method)工厂方法创建对象的 bean 。相比之下， `FactoryBean`（注意大写）是指特定于 Spring 的 [`FactoryBean`](https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans-factory-extension-factorybean)实现类。 |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

##### 确定 Bean 的运行时类型

确定特定 bean 的运行时类型并非易事。bean 元数据定义中的指定类只是一个初始类引用，可能与声明的工厂方法相结合，或者是`FactoryBean`类可能导致 bean 的不同运行时类型，或者在实例的情况下根本没有设置 -级别工厂方法（通过指定`factory-bean`名称解析）。此外，AOP 代理可以使用基于接口的代理包装 bean 实例，并限制暴露目标 bean 的实际类型（仅其实现的接口）。

找出特定 bean 的实际运行时类型的推荐方法是`BeanFactory.getType`调用指定的 bean 名称。这考虑了上述所有情况，并返回`BeanFactory.getBean`调用将针对相同 bean 名称返回的对象类型。

