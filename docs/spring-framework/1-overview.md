# Spring 框架概述

Spring框架为现代基于java的企业应用程序提供了全面的编程和配置模型——适用于任何类型的部署平台。

Spring的是应用程序级的基础设施支持的一个关键元素:Spring关注企业应用程序的“管道”，这样团队就可以专注于应用程序级业务逻辑，而不需要与特定的部署环境有不必要的联系。



## 特性

核心技术:依赖注入、事件、资源、i18n、验证、数据绑定、类型转换、SpEL、AOP。

测试:模拟对象，TestContext框架，Spring MVC测试，WebTestClient。

数据访问:事务、DAO支持、JDBC、ORM、编组XML。

Spring MVC和Spring WebFlux web框架。

集成:远程、JMS、JCA、JMX、电子邮件、任务、调度、缓存。

语言:Kotlin, Groovy，动态语言。



Spring 使创建 Java 企业应用程序变得容易。它提供了在企业环境中使用 Java 语言所需的一切，支持 Groovy 和 Kotlin 作为 JVM 上的替代语言，并且可以根据应用程序的需要灵活地创建多种架构。从 Spring Framework 5.1 开始，Spring 需要 JDK 8+（Java SE 8+）并为 JDK 11 LTS 提供开箱即用的支持。建议将 Java SE 8 update 60 作为 Java 8 的最低补丁版本，但通常建议使用最新的补丁版本。

Spring 支持广泛的应用场景。在大型企业中，应用程序往往存在很长时间，并且必须运行在升级周期不受开发人员控制的JDK和应用程序服务器上。其他可能作为嵌入服务器的单个 jar 运行，可能在云环境中。还有一些可能是不需要服务器的独立应用程序（例如批处理或集成工作负载）。

Spring 是开源的。它拥有一个庞大而活跃的社区，可根据各种实际用例提供持续的反馈。这帮助 Spring 在很长一段时间内成功发展。

## 1. 我们所说的“Spring”是什么意思

术语“Spring”在不同的上下文中意味着不同的东西。它可以用来指代 Spring Framework 项目本身，这就是一切的开始。随着时间的推移，其他 Spring 项目已经构建在 Spring 框架之上。大多数情况下，当人们说“Spring”时，他们指的是整个项目系列。本参考文档侧重于基础：Spring 框架本身。

Spring 框架分为多个模块。应用程序可以选择他们需要的模块。核心是核心容器的模块，包括配置模型和依赖注入机制。除此之外，Spring Framework 为不同的应用程序架构提供基础支持，包括消息传递、事务数据和持久性以及 Web。它还包括基于 Servlet 的 Spring MVC Web 框架，以及并行的 Spring WebFlux 反应式 Web 框架。

关于模块的说明：Spring 的框架 jar 允许部署到 JDK 9 的模块路径（“Jigsaw”）。为了在支持 Jigsaw 的应用程序中使用，Spring Framework 5 jar 带有“Automatic-Module-Name”清单条目，这些条目定义了独立于jar 工件名称（jar 遵循相同的命名模式，使用“-”代替“.”，例如“spring-core”和“spring-context”）。当然，Spring 的框架 jars 在 JDK 8 和 9+ 的类路径上都能正常工作。

## 2. Spring 和 Spring 框架的历史

Spring 于 2003 年应运而生，以应对早期[J2EE](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition)规范的复杂性 。虽然有些人认为 Java EE 和 Spring 是在竞争，但实际上 Spring 是对 Java EE 的补充。Spring 编程模型不包含 Java EE 平台规范；相反，它集成了 EE组织中精心挑选的规范：

- Servlet API ( [JSR 340](https://jcp.org/en/jsr/detail?id=340) )
- WebSocket API ( [JSR 356](https://www.jcp.org/en/jsr/detail?id=356) )
- 并发实用程序 ( [JSR 236](https://www.jcp.org/en/jsr/detail?id=236) )
- JSON 绑定 API ( [JSR 367](https://jcp.org/en/jsr/detail?id=367) )
- Bean 验证 ( [JSR 303](https://jcp.org/en/jsr/detail?id=303) )
- JPA ( [JSR 338](https://jcp.org/en/jsr/detail?id=338) )
- JMS ( [JSR 914](https://jcp.org/en/jsr/detail?id=914) )
- 以及用于事务协调的 JTA/JCA 设置（如有必要）。

Spring Framework 还支持 Dependency Injection ( [JSR 330](https://www.jcp.org/en/jsr/detail?id=330) ) 和 Common Annotations ( [JSR 250](https://jcp.org/en/jsr/detail?id=250) ) 规范，应用程序开发人员可以选择使用这些规范来代替 Spring Framework 提供的 Spring 特定机制。

从 Spring Framework 5.0 开始，Spring 至少需要 Java EE 7 级别（例如 Servlet 3.1+、JPA 2.1+） - 同时在 Java EE 8 级别提供与较新 API 的开箱即用集成（例如 Servlet 4.0、JSON Binding API）在运行时遇到。这使 Spring 与 Tomcat 8 和 9、WebSphere 9 和 JBoss EAP 7 等完全兼容。

随着时间的推移，Java EE 在应用程序开发中的作用不断发展。在 Java EE 和 Spring 的早期，创建应用程序是为了部署到应用程序服务器。今天，在 Spring Boot 的帮助下，应用程序以一种 DevOps 和云友好的方式创建，嵌入了 Servlet 容器，更改起来很简单。从 Spring Framework 5 开始，WebFlux 应用程序甚至不直接使用 Servlet API，并且可以在不是 Servlet 容器的服务器（例如 Netty）上运行。

Spring 不断创新和发展。除了 Spring 框架之外，还有其他项目，例如 Spring Boot、Spring Security、Spring Data、Spring Cloud、Spring Batch 等。重要的是要记住，每个项目都有自己的源代码存储库、问题跟踪器和发布节奏。有关Spring 项目的完整列表，请参阅[spring.io/projects](https://spring.io/projects)。

## 3、设计理念

当您了解一个框架时，重要的是不仅要了解它的作用，还要了解它遵循的原则。以下是 Spring Framework 的指导原则：

- 提供各个级别的选择。Spring 允许您尽可能晚地推迟设计决策。例如，您可以在不更改代码的情况下通过配置切换持久性提供程序。许多其他基础设施问题和与第三方 API 的集成也是如此。
- 包容不同的观点。Spring 拥抱灵活性，并且对事情应该如何做没有固执己见。它以不同的视角支持广泛的应用需求。
- 保持强大的向后兼容性。Spring 的演变经过精心管理，以强制在版本之间进行很少的重大更改。Spring 支持精心挑选的一系列 JDK 版本和第三方库，以方便维护依赖 Spring 的应用程序和库。
- 关心 API 设计。Spring 团队投入了大量的思想和时间来制作直观的 API，这些 API 可以跨越多个版本和多年。
- 为代码质量设定高标准。Spring Framework 非常强调有意义、最新和准确的 javadoc。它是极少数可以声称代码结构清晰且包之间没有循环依赖关系的项目之一。