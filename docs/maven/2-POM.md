### 2.1.目录

- [什么是 POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#What_is_a_POM)？
- [超级POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Super_POM)
- [最小的 POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Minimal_POM)
- 项目传承
  - [示例 1](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Example_1)
  - [示例 2](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Example_2)
- 项目聚合
  - [示例 3](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Example_3)
  - [示例 4](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Example_4)
- 项目继承与项目聚合
  - [例 5](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Example_5)
- 项目插值和变量
  - [可用变量](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Available_Variables)

### 2.2.什么是 POM？

项目对象模型或 POM 是 Maven 中的基本工作单元。它是一个 XML 文件，其中包含有关 Maven 用于构建项目的项目和配置详细信息的信息。它包含大多数项目的默认值。这方面的示例是构建目录，即`target`; 源目录，即`src/main/java`；测试源目录，即`src/test/java`；等等。执行任务或目标时，Maven 会在当前目录中查找 POM。它读取 POM，获取所需的配置信息，然后执行目标。

可以在 POM 中指定的一些配置是项目依赖项、可以执行的插件或目标、构建配置文件等。还可以指定其他信息，例如项目版本、描述、开发人员、邮件列表等。



### 2.3超级POM

Super POM 是 Maven 的默认 POM。除非明确设置，否则所有 POM 都扩展 Super POM，这意味着 Super POM 中指定的配置由您为项目创建的 POM 继承。

您可以在 Maven Core 参考文档中[查看 Maven 3.6.3](http://maven.apache.org/ref/3.6.3/maven-model-builder/super-pom.html)的[Super POM](http://maven.apache.org/ref/3.6.3/maven-model-builder/super-pom.html)。



### 2.4最小的 POM

POM 的最低要求如下：

- `project` 根
- `modelVersion` - 应设置为 4.0.0
- `groupId` - 项目组的 ID。
- `artifactId` - 工件（项目）的 id
- `version` - 指定组下的工件版本

下面是一个例子：

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```

POM 要求配置其 groupId、artifactId 和 version。这三个值构成了项目的完全限定工件名称。这采用 <groupId>:<artifactId>:<version> 的形式。对于上面的示例，其完全限定的工件名称是“com.mycompany.app:my-app:1”。

此外，如第一部分所述如果未指定配置详细信息，Maven 将使用其默认值。这些默认值之一是包装类型。每个 Maven 项目都有一个打包类型。如果未在 POM 中指定，则将使用默认值“jar”。

此外，您可以看到在最小 POM中没有指定*存储库*。如果您使用最小 POM 构建您的项目，它将继承Super POM 中的*存储库*配置。因此当Maven看到minimal POM中的依赖时，就会知道这些依赖是从`https://repo.maven.apache.org/maven2`Super POM中指定的下载地址中下载的。



### 2.5项目继承

合并的 POM 中的元素如下：

- 依赖
- 开发者和贡献者
- 插件列表（包括报告）
- 具有匹配 ID 的插件执行
- 插件配置
- 资源

Super POM 是项目继承的一个示例，但是您也可以通过在 POM 中指定父元素来引入您自己的父 POM，如下面的示例所示。

#### 2.5.1示例 1

##### 情景

例如，让我们重用之前的工件 com.mycompany.app:my-app:1。让我们介绍另一个工件，com.mycompany.app:my-module:1。

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```

让我们指定它们的目录结构如下：

```
.
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
```

**注意：** `my-module/pom.xml`是 com.mycompany.app:my-module:1`pom.xml`的 POM而是com.mycompany.app:my-app:1的 POM

##### 解决方案

现在，如果我们要将 com.mycompany.app:my-app:1 变成 com.mycompany.app:my-module:1 的父工件，我们将不得不修改 com.mycompany.app:my-module： 1的POM改为如下配置：

**com.mycompany.app:my-module:1 的 POM**

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
  </parent>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```

请注意，我们现在添加了一个部分，即父部分。这部分允许我们指定哪个工件是我们 POM 的父级。我们通过指定父 POM 的完全限定工件名称来实现。通过这种设置，我们的模块现在可以继承父 POM 的一些属性。

或者，如果您希望模块的 groupId 或版本与其父模块相同，则可以在其 POM 中删除模块的 groupId 或版本标识。

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
  </parent>
 
  <artifactId>my-module</artifactId>
</project>
```

这允许模块继承 groupId 或其父 POM 的版本。



#### 2.5.2示例 2

##### 情景

但是，如果父项目已经安装在我们的本地存储库中或在该特定目录结构中（父项目`pom.xml`比模块的 目录高一个目录），这将起作用`pom.xml`。

但是如果父级尚未安装并且目录结构如以下示例中所示怎么办？

```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
```

##### 解决方案

为了解决这个目录结构（或任何其他目录结构），我们必须将`<relativePath>`元素添加到我们的父部分。

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
    <relativePath>../parent/pom.xml</relativePath>
  </parent>
 
  <artifactId>my-module</artifactId>
</project>
```

顾名思义，它是从模块`pom.xml`到父模块的相对路径`pom.xml`。

### 2.6项目聚合

项目聚合类似于[项目继承](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Project_Inheritance)。但是它不是从模块中指定父 POM，而是从父 POM 中指定模块。通过这样做，父项目现在知道它的模块，如果对父项目调用 Maven 命令，那么该 Maven 命令也将被执行到父的模块。要进行项目聚合，您必须执行以下操作：

- 将父 POM 包装更改为值“pom”。
- 在父 POM 中指定其模块（子 POM）的目录。



#### 2.6.1示例 3

##### 情景

鉴于之前的原始工件 POM 和目录结构：

**com.mycompany.app:my-app:1 的 POM**

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```

**com.mycompany.app:my-module:1 的 POM**

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```

**目录结构**

```
.
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
```

##### 解决方案

如果我们要将 my-module 聚合到 my-app 中，我们只需要修改 my-app。

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-module</module>
  </modules>
</project>
```

在修改后的 com.mycompany.app:my-app:1 中，添加了打包部分和模块部分。对于包装，它的值设置为“pom”，对于模块部分，我们有元素`<module>my-module</module>`。的值`<module>`是从 com.mycompany.app:my-app:1 到 com.mycompany.app:my-module:1 的 POM 的相对路径（*实际上，我们使用模块的 artifactId 作为模块目录的名称*）。

现在，每当 Maven 命令处理 com.mycompany.app:my-app:1 时，同样的 Maven 命令也会针对 com.mycompany.app:my-module:1 运行。此外，某些命令（特别是目标）以不同的方式处理项目聚合。



#### 2.6.2示例 4

##### 情景

但是如果我们将目录结构更改为以下内容会怎样：

```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
```

父 POM 将如何指定其模块？

##### 解决方案

答案？- 与示例 3 相同的方式，通过指定模块的路径。

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>../my-module</module>
  </modules>
</project>
```

### 2.7项目继承与项目聚合对比

如果您有多个 Maven 项目，并且它们都有相似的配置，您可以通过拉出那些相似的配置并创建一个父项目来重构您的项目。因此，您所要做的就是让您的 Maven 项目继承该父项目，然后这些配置将应用于所有这些项目。

如果您有一组一起构建或处理的项目，您可以创建一个父项目并让该父项目将这些项目声明为其模块。通过这样做，您只需要构建父级，其余的将随之而来。

但当然，您可以同时拥有项目继承和项目聚合。这意味着，您可以让您的模块指定一个父项目，同时让该父项目将这些 Maven 项目指定为其模块。你只需要应用所有三个规则：

- 在每个子 POM 中指定其父 POM 是谁。
- 将父 POM 的包装更改为值 "pom" 。
- 在父 POM 中指定其模块（子 POM）的目录



#### 2.7.1例 5

##### 情景

再次考虑到之前的原始工件 POM，

**com.mycompany.app:my-app:1 的 POM**

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```

**com.mycompany.app:my-module:1 的 POM**

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```

和这个**目录结构**

```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
```

##### 解决方案

要同时进行项目继承和聚合，您只需应用所有三个规则。

**com.mycompany.app:my-app:1 的 POM**

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>../my-module</module>
  </modules>
</project>
```

**com.mycompany.app:my-module:1 的 POM**

```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
    <relativePath>../parent/pom.xml</relativePath>
  </parent>
 
  <artifactId>my-module</artifactId>
</project>
```

**注意：**配置文件继承与用于 POM 本身的继承策略相同。



### 2.8项目插值和变量

Maven 鼓励的做法之一是*不要重复自己*。但是，在某些情况下，您需要在多个不同位置使用相同的值。为了帮助确保该值只指定一次，Maven 允许您在 POM 中使用您自己的和预定义的变量。

例如，要访问`project.version`变量，您可以像这样引用它：

```
 <version>${project.version}</version>
```

需要注意的一个因素是，这些变量是*在*如上所述的继承*之后*进行处理的。这意味着如果父项目使用一个变量，那么它在子项目中的定义，而不是父项目，将是最终使用的。

#### 2.8.1可用变量

##### 2.8.1.1项目模型变量

模型中作为单值元素的任何字段都可以作为变量引用。例如`${project.groupId}`，`${project.version}`，`${project.build.sourceDirectory}`等等。请参阅 POM 参考以查看完整的属性列表。

这些变量都由前缀“ `project.`”引用。您可能还会看到带有`pom.`作为前缀的引用，或者完全省略前缀 - 这些形式现在已被弃用，不应使用。

##### 2.8.1.2重要变量

| `project.basedir`       | 当前项目所在的目录。                             |
| ----------------------- | ------------------------------------------------ |
| `project.baseUri`       | 当前项目所在的目录，表示为 URI。*自 Maven 2.1.0* |
| `maven.build.timestamp` | 表示构建开始的时间戳 (UTC)。*自 Maven 2.1.0-M1*  |

构建时间戳的格式可以通过声明属性来自定义，`maven.build.timestamp.format`如下例所示：

```
<project>
  ...
  <properties>
    <maven.build.timestamp.format>yyyy-MM-dd'T'HH:mm:ss'Z'</maven.build.timestamp.format>
  </properties>
  ...
</project>
```

格式模式必须符合[SimpleDateFormat](https://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html) API 文档中给出的规则。如果该属性不存在，则格式默认为示例中已经给出的值。

##### 2.8.1.3自定义属性

您还可以将项目中定义的任何属性作为变量引用。考虑以下示例：

```
<project>
  ...
  <properties>
    <mavenVersion>3.0</mavenVersion>
  </properties>
 
  <dependencies>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-artifact</artifactId>
      <version>${mavenVersion}</version>
    </dependency>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-core</artifactId>
      <version>${mavenVersion}</version>
    </dependency>
  </dependencies>
  ...
</project>
```

[[](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)