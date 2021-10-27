### 1.1目录

- [构建生命周期基础]
- 设置您的项目以使用构建生命周期
  - [包装]
  - [插件]
- 生命周期参考
- [内置生命周期绑定]

### 1.2。构建生命周期基础

Maven 基于构建生命周期的中心概念。这意味着明确定义了构建和分发特定工件（项目）的过程。

对于构建项目的人来说，这意味着只需要学习一小组命令来构建任何 Maven 项目，[POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)将确保他们得到他们想要的结果。

有三个内置的构建生命周期：default、clean 和 site。在`default`生命周期处理你的项目部署中，`clean`生命周期把手伸出清洗，而`site`生命周期把手创建项目的网站。

#### 1.2.1构建生命周期由阶段组成

这些构建生命周期中的每一个都由不同的构建阶段列表定义，其中构建阶段代表生命周期中的一个阶段。

例如，默认生命周期包括以下阶段（有关生命周期阶段的完整列表，请参阅[生命周期参考](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference)）：

- `validate` - 验证项目是否正确并且所有必要的信息都可用
- `compile` - 编译项目的源代码
- `test`- 使用合适的单元测试框架测试编译的源代码。这些测试不应该要求打包或部署代码
- `package` - 将编译后的代码打包成可分发的格式，例如 JAR。
- `verify` - 对集成测试的结果进行任何检查，以确保满足质量标准
- `install` - 将包安装到本地存储库中，作为本地其他项目的依赖项
- `deploy` - 在构建环境中完成，将最终包复制到远程存储库以与其他开发人员和项目共享。

这些生命周期阶段（以及此处未显示的其他生命周期阶段）按顺序执行以完成`default`生命周期。鉴于上述生命周期阶段，这意味着当使用默认生命周期时，Maven 将首先验证项目，然后将尝试编译源代码，针对测试运行这些源代码，打包二进制文件（例如 jar），针对该项目运行集成测试包，验证集成测试，将经过验证的包安装到本地存储库，然后将安装的包部署到远程存储库。



#### 1.2.2通常的命令行调用

您应该选择与您的结果相匹配的阶段。如果你想要你的罐子，运行`package`. 如果要运行单元测试，请运行`test`.

如果您不确定自己想要什么，调用的首选阶段是

```
mvn verify
```

此命令按顺序执行每个默认生命周期阶段（`validate`，`compile`，`package`等），在执行之前`verify`。您只需要调用要执行的最后一个构建阶段，在本例中为`verify`. 在大多数情况下，效果与 相同`package`。但是，如果有集成测试，它们也会被执行。并且在该`verify`阶段可以进行一些额外的检查，例如，如果您的代码是根据预定义的 checkstyle 规则编写的。

在构建环境中，使用以下调用干净地构建工件并将其部署到共享存储库中。

```
mvn clean deploy
```

相同的命令可用于多模块场景（即具有一个或多个子项目的项目）。Maven 遍历每个子项目并执行`clean`，然后执行`deploy`（包括所有先前的构建阶段步骤）。

#### 1.2.3构建阶段由插件目标组成

然而，即使构建阶段负责构建生命周期中的特定步骤，它执行这些职责的方式可能会有所不同。这是通过声明绑定到这些构建阶段的插件目标来完成的。

插件目标表示有助于构建和管理项目的特定任务（比构建阶段更精细）。它可能绑定到零个或多个构建阶段。未绑定到任何构建阶段的目标可以通过直接调用在构建生命周期之外执行。执行顺序取决于调用目标和构建阶段的顺序。例如，考虑下面的命令。在`clean`和`package`参数是构建阶段，而`dependency:copy-dependencies`为（的插件）的目标。

```
mvn clean dependency:copy-dependencies package
```

如果要执行这个，`clean`阶段将首先执行（意味着它将运行干净生命周期的所有前面的阶段，加上`clean`阶段本身），然后是`dependency:copy-dependencies`目标，最后执行`package`阶段（及其所有前面的构建阶段）默认生命周期）。

此外，如果目标绑定到一个或多个构建阶段，则该目标将在所有这些阶段中调用。

此外，构建阶段也可以绑定零个或多个目标。如果构建阶段没有与之绑定的目标，则该构建阶段将不会执行。但是如果它绑定了一个或多个目标，它将执行所有这些目标。

(*注意：在Maven 2.0.5及以上版本中，绑定到一个阶段的多个目标按照它们在POM中声明的顺序执行，但不支持同一插件的多个实例。同一插件的多个实例被分组在 Maven 2.0.11 及更高版本中一起执行和订购*）。

#### 1.2.4某些阶段通常不会从命令行调用

以连字符（`pre-*`、`post-*`、 或`process-*`）命名的阶段通常不会直接从命令行调用。这些阶段对构建进行排序，产生在构建之外无用的中间结果。在调用 的情况下`integration-test`，环境可能会处于挂起状态。

Jacoco等代码覆盖工具和Tomcat、Cargo、Docker等执行容器插件将目标绑定到`pre-integration-test`阶段，准备集成测试容器环境。这些插件还将目标绑定到`post-integration-test`阶段以收集覆盖率统计信息或停用集成测试容器。

故障安全和代码覆盖插件绑定目标，`integration-test`和`verify`阶段。最终结果是测试和覆盖率报告在该`verify`阶段之后可用。如果`integration-test`要从命令行调用，则不会生成任何报告。更糟糕的是，集成测试容器环境处于挂起状态；Tomcat 网络服务器或 Docker 实例仍在运行，而 Maven 甚至可能不会自行终止。



### 1.3设置您的项目以使用构建生命周期

构建生命周期使用起来非常简单，但是当您为项目构建 Maven 构建时，您如何将任务分配给每个构建阶段？

#### 1.3.1Packaging

第一种也是最常见的方法是通过同名的 POM 元素为您的项目设置打包`<packaging>`。一些有效的包装价值的是`jar`，`war`，`ear`和`pom`。如果未指定包装值，则默认为`jar`。

每个包装都包含要绑定到特定阶段的目标列表。例如，`jar`打包将以下目标绑定到默认生命周期的构建阶段。

| 阶段                     | 插件：目标                |
| :----------------------- | :------------------------ |
| `process-resources`      | `resources:resources`     |
| `compile`                | `compiler:compile`        |
| `process-test-resources` | `resources:testResources` |
| `test-compile`           | `compiler:testCompile`    |
| `test`                   | `surefire:test`           |
| `package`                | `jar:jar`                 |
| `install`                | `install:install`         |
| `deploy`                 | `deploy:deploy`           |

这是一组几乎[标准的绑定](http://maven.apache.org/ref/current/maven-core/default-bindings.html)；但是，有些包装对它们的处理方式不同。例如，一个纯粹是元数据（打包值为`pom`）的项目只将目标绑定到`install`和`deploy`阶段（有关某些打包类型的目标到构建阶段绑定的完整列表，请参阅[生命周期参考](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference)）。

请注意，对于某些可用的打包类型，您可能还需要在`<build>`POM的部分中包含一个特定的插件并`<extensions>true</extensions>`为该插件指定。需要此功能的插件的一个示例是 Plexus 插件，它提供了一个`plexus-application`和`plexus-service`包装。



#### 1.3.2插件

向阶段添加目标的第二种方法是在项目中配置插件。插件是为 Maven 提供目标的工件。此外，插件可以具有一个或多个目标，其中每个目标代表该插件的能力。例如，Compiler 插件有两个目标：`compile`和`testCompile`. 前者编译您的主代码的源代码，而后者编译您的测试代码的源代码。

正如您将在后面的部分中看到的，插件可以包含指示要将目标绑定到哪个生命周期阶段的信息。请注意，仅添加插件是不够的信息 - 您还必须指定要作为构建的一部分运行的目标。

配置的目标将添加到已绑定到所选包装的生命周期的目标中。如果多个目标绑定到特定阶段，则使用的顺序是首先执行包装中的目标，然后是 POM 中配置的目标。请注意，您可以使用该`<executions>`元素来更好地控制特定目标的顺序。

例如，Modello 插件默认将其目标绑定`modello:java`到`generate-sources`阶段（注意：`modello:java`目标生成 Java 源代码）。因此，要使用 Modello 插件并让它从模型生成源并将其合并到构建中，您需要将以下内容添加到 POM 的`<plugins>`部分`<build>`：

```xml
...
 <plugin>
   <groupId>org.codehaus.modello</groupId>
   <artifactId>modello-maven-plugin</artifactId>
   <version>1.8.1</version>
   <executions>
     <execution>
       <configuration>
         <models>
           <model>src/main/mdo/maven.mdo</model>
         </models>
         <version>4.0.0</version>
       </configuration>
       <goals>
         <goal>java</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
...
```

您可能想知道为什么会出现该`<executions>`元素。这样您就可以在需要时使用不同的配置多次运行相同的目标。还可以为单独的执行分配一个 ID，以便在继承或配置文件的应用期间，您可以控制目标配置是合并还是转换为附加执行。

当给出匹配特定阶段的多个执行时，它们按照 POM 中指定的顺序执行，首先运行继承的执行。

现在，在`modello:java` 的情况下，它只在`generate-sources`阶段中有意义。但是有些目标可以在多个阶段使用，并且可能没有合理的默认值。对于那些，您可以自己指定阶段。例如，假设您有一个`display:time`将当前时间回显到命令行的目标，并且您希望它在`process-test-resources`阶段中运行以指示测试何时开始。这将像这样配置：

```
...
 <plugin>
   <groupId>com.mycompany.example</groupId>
   <artifactId>display-maven-plugin</artifactId>
   <version>1.0</version>
   <executions>
     <execution>
       <phase>process-test-resources</phase>
       <goals>
         <goal>time</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
...
```

### 1.4生命周期参考

下面列出了`default`、`clean`和`site`生命周期的所有构建阶段，它们按照指定的时间点之前的顺序执行。

#### 1.4.1清洁生命周期

| 阶段         | 描述                             |
| :----------- | :------------------------------- |
| `pre-clean`  | 在实际项目清理之前执行所需的流程 |
| `clean`      | 删除先前构建生成的所有文件       |
| `post-clean` | 执行完成项目清理所需的流程       |

#### 1.4.2默认生命周期

| 阶段                      | 描述                                                         |
| :------------------------ | :----------------------------------------------------------- |
| `validate`                | 验证项目是否正确并且所有必要的信息都可用。                   |
| `initialize`              | 初始化构建状态，例如设置属性或创建目录。                     |
| `generate-sources`        | 生成包含在编译中的任何源代码。                               |
| `process-sources`         | 处理源代码，例如过滤任何值。                                 |
| `generate-resources`      | 生成包含在包中的资源。                                       |
| `process-resources`       | 将资源复制并处理到目标目录中，准备打包。                     |
| `compile`                 | 编译项目的源代码。                                           |
| `process-classes`         | 对编译生成的文件进行后处理，例如对 Java 类进行字节码增强。   |
| `generate-test-sources`   | 生成任何测试源代码以包含在编译中。                           |
| `process-test-sources`    | 处理测试源代码，例如过滤任何值。                             |
| `generate-test-resources` | 创建用于测试的资源。                                         |
| `process-test-resources`  | 将资源复制并处理到测试目标目录中。                           |
| `test-compile`            | 将测试源代码编译到测试目标目录中                             |
| `process-test-classes`    | 对测试编译生成的文件进行后处理，例如对 Java 类进行字节码增强。 |
| `test`                    | 使用合适的单元测试框架运行测试。这些测试不应该要求打包或部署代码。 |
| `prepare-package`         | 在实际包装之前执行准备包装所需的任何操作。这通常会导致包的解包、处理版本。 |
| `package`                 | 获取编译后的代码并将其打包为可分发的格式，例如 JAR。         |
| `pre-integration-test`    | 在执行集成测试之前执行所需的操作。这可能涉及诸如设置所需环境之类的事情。 |
| `integration-test`        | 如有必要，处理并部署包到可以运行集成测试的环境中。           |
| `post-integration-test`   | 执行集成测试后执行所需的操作。这可能包括清理环境。           |
| `verify`                  | 运行任何检查以验证包是否有效并符合质量标准。                 |
| `install`                 | 将包安装到本地存储库中，用作本地其他项目的依赖项。           |
| `deploy`                  | 在集成或发布环境中完成，将最终包复制到远程存储库以与其他开发人员和项目共享。 |

#### 1.4.3站点生命周期

| 阶段          | 描述                                     |
| :------------ | :--------------------------------------- |
| `pre-site`    | 在实际项目现场生成之前执行所需的流程     |
| `site`        | 生成项目的站点文档                       |
| `post-site`   | 执行完成站点生成和准备站点部署所需的流程 |
| `site-deploy` | 将生成的站点文档部署到指定的 Web 服务器  |



### 1.5内置生命周期绑定

默认情况下，某些阶段具有与其绑定的目标。对于默认生命周期，这些绑定取决于打包值。以下是一些目标到构建阶段的绑定。

#### 1.5.1清理生命周期绑定

| 阶段    | 插件：目标    |
| :------ | :------------ |
| `clean` | `clean:clean` |

#### 1.5.2默认生命周期绑定 - 包装`ejb`/ `ejb3`/ `jar`/ `par`/ `rar`/`war`

| 阶段                     | 插件：目标                                                   |
| :----------------------- | :----------------------------------------------------------- |
| `process-resources`      | `resources:resources`                                        |
| `compile`                | `compiler:compile`                                           |
| `process-test-resources` | `resources:testResources`                                    |
| `test-compile`           | `compiler:testCompile`                                       |
| `test`                   | `surefire:test`                                              |
| `package`                | `ejb:ejb` *或* `ejb3:ejb3` *或* `jar:jar` *或* `par:par` *或* `rar:rar` *或* `war:war` |
| `install`                | `install:install`                                            |
| `deploy`                 | `deploy:deploy`                                              |

#### 1.5.3默认生命周期绑定 - 打包 `ear`

| 阶段                 | 插件：目标                     |
| :------------------- | :----------------------------- |
| `generate-resources` | `ear:generate-application-xml` |
| `process-resources`  | `resources:resources`          |
| `package`            | `ear:ear`                      |
| `install`            | `install:install`              |
| `deploy`             | `deploy:deploy`                |

#### 1.5.4默认生命周期绑定 - 打包 `maven-plugin`

| 阶段                     | 插件：目标                                        |
| :----------------------- | :------------------------------------------------ |
| `generate-resources`     | `plugin:descriptor`                               |
| `process-resources`      | `resources:resources`                             |
| `compile`                | `compiler:compile`                                |
| `process-test-resources` | `resources:testResources`                         |
| `test-compile`           | `compiler:testCompile`                            |
| `test`                   | `surefire:test`                                   |
| `package`                | `jar:jar` *和* `plugin:addPluginArtifactMetadata` |
| `install`                | `install:install`                                 |
| `deploy`                 | `deploy:deploy`                                   |

#### 1.5.5默认生命周期绑定 - 打包 `pom`

| 阶段      | 插件：目标        |
| :-------- | :---------------- |
| `package` |                   |
| `install` | `install:install` |
| `deploy`  | `deploy:deploy`   |

#### 1.5.6站点生命周期绑定

| 阶段          | 插件：目标    |
| :------------ | :------------ |
| `site`        | `site:site`   |
| `site-deploy` | `site:deploy` |

#### 