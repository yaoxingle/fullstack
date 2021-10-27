依赖管理是 Maven 的一个核心特性。管理单个项目的依赖关系很容易。管理由数百个模块组成的多模块项目和应用程序的依赖关系是可能的。Maven 在定义、创建和维护具有明确定义的类路径和库版本的可重现构建方面有很大帮助。

### 5.1目录

- 传递依赖
  - 排除/可选依赖项
- [依赖范围](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Dependency_Scope)
- 依赖管理
  - [导入依赖](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Importing_Dependencies)
  - [物料清单 (BOM) POM](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#bill-of-materials-bom-poms)
- [系统依赖](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#System_Dependencies)

### 5.2递依赖

Maven 通过自动包含传递依赖项，避免了发现和指定您自己的依赖项所需的库的需要。

通过从指定的远程存储库读取依赖项的项目文件来促进此功能。通常，这些项目的所有依赖项都会在您的项目中使用，项目从其父项或从其依赖项等继承的任何依赖项也是如此。

可以从中收集依赖项的级别数量没有限制。只有在发现循环依赖时才会出现问题。

使用传递依赖，包含的库的图会很快变得非常大。出于这个原因，还有一些额外的功能可以限制包含哪些依赖项：

- 依赖仲裁

  ——这决定了当遇到多个版本作为依赖时将选择哪个版本的工件。Maven 选择“最近的定义”。也就是说，它使用依赖项树中与您的项目最接近的依赖项的版本。您始终可以通过在项目的 POM 中明确声明来保证版本。请注意，如果两个依赖版本在依赖树中的深度相同，则第一个声明获胜。

  - “最近的定义”意味着所使用的版本将是依赖关系树中与您的项目最接近的版本。考虑这个依赖关系树：

    ```
      A 
      ├── B 
      │ └── C 
      │ └── D 2.0 
      └── E 
          └── D 1.0
    ```

    在文本中，A、B 和 C 的依赖关系定义为 A -> B -> C -> D 2.0 和 A -> E -> D 1.0，那么在构建 A 时将使用 D 1.0，因为从 A 到D 到 E 较短。您可以在 A 中显式添加对 D 2.0 的依赖以强制使用 D 2.0，如下所示：

    ```
      A 
      ├── B 
      │ └── C 
      │ └── D 2.0 
      ├── E 
      │ └── D 1.0 
      │ 
      └── D 2.0      
    ```

- *依赖管理*——这允许项目作者直接指定在传递依赖或未指定版本的依赖中遇到要使用的工件版本。在上一节的示例中，即使 A 不直接使用依赖项，它也直接添加到 A 中。相反，A 可以将 D 作为依赖项包含在其dependencyManagement 部分中，并直接控制何时使用哪个版本的 D，或者如果，它曾经被引用过。

- *依赖范围*- 这允许您仅包含适合当前构建阶段的依赖项。这将在下面更详细地描述。

- *排除的依赖项*- 如果项目 X 依赖于项目 Y，而项目 Y 依赖于项目 Z，则项目 X 的所有者可以使用“排除”元素明确地将项目 Z 作为依赖项排除在外。

- *可选依赖项*- 如果项目 Y 依赖于项目 Z，则项目 Y 的所有者可以使用“可选”元素将项目 Z 标记为可选依赖项。当项目 X 依赖于项目 Y 时，X 将仅依赖于 Y，而不依赖于 Y 的可选依赖项 Z。然后，项目 X 的所有者可以根据自己的选择明确添加对 Z 的依赖项。（将可选依赖项视为“默认排除”可能会有所帮助。）

尽管可传递依赖项可以隐式包含所需的依赖项，但显式指定源代码直接使用的依赖项是一种很好的做法。这个最佳实践证明了它的价值，尤其是当你的项目的依赖项改变了它们的依赖项时。

例如，假设你的项目A指定了对另一个项目B的依赖，而项目B指定了对项目C的依赖。如果你在项目C中直接使用组件，而你在项目A中没有指定项目C，它当项目 B 突然更新/删除其对项目 C 的依赖时，可能会导致构建失败。

直接指定依赖项的另一个原因是它为您的项目提供了更好的文档：您可以通过阅读项目中的 POM 文件或执行**mvn dependency:tree**来了解更多信息。

Maven 还提供了[dependency:analyze](http://maven.apache.org/plugins/maven-dependency-plugin/analyze-mojo.html)插件目标来分析依赖：它有助于使这个最佳实践更容易实现。

### 5.3依赖范围

依赖范围用于限制依赖的传递性并确定何时将依赖包含在类路径中。

有6个范围：

- **compile**
  这是默认范围，如果没有指定则使用。编译依赖项在项目的所有类路径中都可用。此外，这些依赖关系会传播到依赖项目。
- ****provided****
  这很像`compile`，但表明您希望 JDK 或容器在运行时提供依赖项。例如，在为 Java Enterprise Edition 构建 Web 应用程序时，您可以将对 Servlet API 和相关 Java EE API 的依赖项设置为作用域，`provided`因为 Web 容器提供这些类。具有此范围的依赖项会添加到用于编译和测试的类路径中，但不会添加到运行时类路径中。它不是传递性的。
- ****runtime****
  这个范围表示依赖不是编译所必需的，而是执行所必需的。Maven 在运行时和测试类路径中包含具有此范围的依赖项，但在编译类路径中不包含。
- **test**
  这个作用域表示应用程序的正常使用不需要依赖，只在测试编译和执行阶段可用。此作用域不可传递。通常，此范围用于测试库，例如 JUnit 和 Mockito。如果这些库用于单元测试 (src/test/java) 但不在模型代码 (src/main/java) 中，它也用于非测试库，例如 Apache Commons IO。
- **system**
  此范围类似于，`provided`除了您必须提供明确包含它的 JAR。该工件始终可用，不会在存储库中查找。
- **import**
  此作用域仅`pom`在`<dependencyManagement>`部分中的类型依赖项上受支持。它指示依赖项将替换为指定 POM`<dependencyManagement>`部分中的有效依赖项列表。由于它们被替换，因此范围为 的依赖项`import`实际上并不参与限制依赖项的传递性。

每个作用域（除了`import`）都以不同的方式影响传递依赖，如下表所示。如果将依赖项设置为左列中的范围，则该依赖项的传递依赖项与跨顶行的范围会导致主项目中的依赖项具有在交点处列出的范围。如果未列出范围，则表示忽略了依赖项。

|          | compile    | provided | runtime  | test |
| -------- | ---------- | -------- | -------- | ---- |
| compile  | compile(*) | -        | runtime  | -    |
| provided | provided   | -        | provided | -    |
| runtime  | runtime    | -        | runtime  | -    |
| est      | test       | -        | test     | -    |

**(\*) 注意：**这应该是运行时范围，因此必须明确列出所有编译依赖项。但是，如果您依赖的库扩展了另一个库中的类，则两者都必须在编译时可用。出于这个原因，即使它们是可传递的，编译时依赖项仍保留为编译范围。

### 5.4依赖管理

依赖管理部分是一种用于集中依赖信息的机制。当您有一组从公共父级继承的项目时，可以将有关依赖项的所有信息放在公共 POM 中，并且可以更简单地引用子 POM 中的工件。通过一些示例可以最好地说明该机制。鉴于这两个扩展同一父级的 POM：

项目A：

```
<project>
  ...
  <dependencies>
    <dependency>
      <groupId>group-a</groupId>
      <artifactId>artifact-a</artifactId>
      <version>1.0</version>
      <exclusions>
        <exclusion>
          <groupId>group-c</groupId>
          <artifactId>excluded-artifact</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    <dependency>
      <groupId>group-a</groupId>
      <artifactId>artifact-b</artifactId>
      <version>1.0</version>
      <type>bar</type>
      <scope>runtime</scope>
    </dependency>
  </dependencies>
</project>
```

项目乙：

```
<project>
  ...
  <dependencies>
    <dependency>
      <groupId>group-c</groupId>
      <artifactId>artifact-b</artifactId>
      <version>1.0</version>
      <type>war</type>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupId>group-a</groupId>
      <artifactId>artifact-b</artifactId>
      <version>1.0</version>
      <type>bar</type>
      <scope>runtime</scope>
    </dependency>
  </dependencies>
</project>
```

这两个示例 POM 共享一个公共依赖项，并且每个 POM 都有一个非平凡的依赖项。这些信息可以像这样放在父 POM 中：

```
<project>
  ...
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>group-a</groupId>
        <artifactId>artifact-a</artifactId>
        <version>1.0</version>
 
        <exclusions>
          <exclusion>
            <groupId>group-c</groupId>
            <artifactId>excluded-artifact</artifactId>
          </exclusion>
        </exclusions>
 
      </dependency>
 
      <dependency>
        <groupId>group-c</groupId>
        <artifactId>artifact-b</artifactId>
        <version>1.0</version>
        <type>war</type>
        <scope>runtime</scope>
      </dependency>
 
      <dependency>
        <groupId>group-a</groupId>
        <artifactId>artifact-b</artifactId>
        <version>1.0</version>
        <type>bar</type>
        <scope>runtime</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>
</project>
```

那么这两个子 POM 就变得简单多了：

```
<project>
  ...
  <dependencies>
    <dependency>
      <groupId>group-a</groupId>
      <artifactId>artifact-a</artifactId>
    </dependency>
 
    <dependency>
      <groupId>group-a</groupId>
      <artifactId>artifact-b</artifactId>
      <!-- This is not a jar dependency, so we must specify type. -->
      <type>bar</type>
    </dependency>
  </dependencies>
</project>
```
```
<project>
  ...
  <dependencies>
    <dependency>
      <groupId>group-c</groupId>
      <artifactId>artifact-b</artifactId>
      <!-- This is not a jar dependency, so we must specify type. -->
      <type>war</type>
    </dependency>
 
    <dependency>
      <groupId>group-a</groupId>
      <artifactId>artifact-b</artifactId>
      <!-- This is not a jar dependency, so we must specify type. -->
      <type>bar</type>
    </dependency>
  </dependencies>
</project>
```


**注意：**在其中两个依赖项引用中，我们必须指定 <type/> 元素。这是因为用于将依赖项引用与依赖项管理部分匹配的最小信息集实际上是**{groupId, artifactId, type, classifier}**。在许多情况下，这些依赖项将引用没有分类器的 jar 工件。这允许我们将身份集简写为**{groupId, artifactId}**，因为 type 字段`jar`的默认值为，并且默认分类器为 null。

依赖管理部分的第二个也是非常重要的用途是控制传递依赖中使用的工件的版本。以这些项目为例：

项目A：

```
<project>
 <modelVersion>4.0.0</modelVersion>
 <groupId>maven</groupId>
 <artifactId>A</artifactId>
 <packaging>pom</packaging>
 <name>A</name>
 <version>1.0</version>
 <dependencyManagement>
   <dependencies>
     <dependency>
       <groupId>test</groupId>
       <artifactId>a</artifactId>
       <version>1.2</version>
     </dependency>
     <dependency>
       <groupId>test</groupId>
       <artifactId>b</artifactId>
       <version>1.0</version>
       <scope>compile</scope>
     </dependency>
     <dependency>
       <groupId>test</groupId>
       <artifactId>c</artifactId>
       <version>1.0</version>
       <scope>compile</scope>
     </dependency>
     <dependency>
       <groupId>test</groupId>
       <artifactId>d</artifactId>
       <version>1.2</version>
     </dependency>
   </dependencies>
 </dependencyManagement>
</project>
```

项目乙：

```
<project>
  <parent>
    <artifactId>A</artifactId>
    <groupId>maven</groupId>
    <version>1.0</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  <groupId>maven</groupId>
  <artifactId>B</artifactId>
  <packaging>pom</packaging>
  <name>B</name>
  <version>1.0</version>
 
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>test</groupId>
        <artifactId>d</artifactId>
        <version>1.0</version>
      </dependency>
    </dependencies>
  </dependencyManagement>
 
  <dependencies>
    <dependency>
      <groupId>test</groupId>
      <artifactId>a</artifactId>
      <version>1.0</version>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupId>test</groupId>
      <artifactId>c</artifactId>
      <scope>runtime</scope>
    </dependency>
  </dependencies>
</project>
```

当 maven 在项目 B 上运行时，将使用工件 a、b、c 和 d 的 1.0 版本，而不管其 POM 中指定的版本如何。

- a 和 c 都声明为项目的依赖项，因此由于依赖项中介而使用 1.0 版。两者都具有运行时范围，因为它是直接指定的。
- b 在 B 的父级的依赖管理部分中定义，并且由于依赖管理优先于传递依赖的依赖中介，如果在 a 或 c 的 POM 中引用它，将选择版本 1.0。b 也将具有编译范围。
- 最后，由于 d 在 B 的依赖管理部分中指定，因此 d 应该是 a 或 c 的依赖（或传递依赖），将选择 1.0 版 - 再次因为依赖管理优先于依赖中介，也因为当前 POM 的声明需要优先于其父声明。

有关依赖项管理标记的参考信息可从[项目描述符参考中获得](http://maven.apache.org/ref/current/maven-model/maven.html#class_DependencyManagement)。

#### 5.4.1导入依赖

上一节中的示例描述了如何通过继承指定托管依赖项。但是，在较大的项目中可能无法实现这一点，因为项目只能从单个父项继承。为了适应这一点，项目可以从其他项目导入托管依赖项。这是通过将 POM 工件声明为具有“导入”范围的依赖项来实现的。

项目乙：

```
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>maven</groupId>
  <artifactId>B</artifactId>
  <packaging>pom</packaging>
  <name>B</name>
  <version>1.0</version>
 
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>maven</groupId>
        <artifactId>A</artifactId>
        <version>1.0</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <dependency>
        <groupId>test</groupId>
        <artifactId>d</artifactId>
        <version>1.0</version>
      </dependency>
    </dependencies>
  </dependencyManagement>
 
  <dependencies>
    <dependency>
      <groupId>test</groupId>
      <artifactId>a</artifactId>
      <version>1.0</version>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupId>test</groupId>
      <artifactId>c</artifactId>
      <scope>runtime</scope>
    </dependency>
  </dependencies>
</project>
```

假设 A 是前面示例中定义的 POM，最终结果将是相同的。除了 d 之外，A 的所有托管依赖项都将合并到 B 中，因为它是在此 POM 中定义的。

项目X：

```
<project>
 <modelVersion>4.0.0</modelVersion>
 <groupId>maven</groupId>
 <artifactId>X</artifactId>
 <packaging>pom</packaging>
 <name>X</name>
 <version>1.0</version>
 
 <dependencyManagement>
   <dependencies>
     <dependency>
       <groupId>test</groupId>
       <artifactId>a</artifactId>
       <version>1.1</version>
     </dependency>
     <dependency>
       <groupId>test</groupId>
       <artifactId>b</artifactId>
       <version>1.0</version>
       <scope>compile</scope>
     </dependency>
   </dependencies>
 </dependencyManagement>
</project>
```

项目 Y：

```
<project>
 <modelVersion>4.0.0</modelVersion>
 <groupId>maven</groupId>
 <artifactId>Y</artifactId>
 <packaging>pom</packaging>
 <name>Y</name>
 <version>1.0</version>
 
 <dependencyManagement>
   <dependencies>
     <dependency>
       <groupId>test</groupId>
       <artifactId>a</artifactId>
       <version>1.2</version>
     </dependency>
     <dependency>
       <groupId>test</groupId>
       <artifactId>c</artifactId>
       <version>1.0</version>
       <scope>compile</scope>
     </dependency>
   </dependencies>
 </dependencyManagement>
</project>
```

Z项目：

```
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>maven</groupId>
  <artifactId>Z</artifactId>
  <packaging>pom</packaging>
  <name>Z</name>
  <version>1.0</version>
 
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>maven</groupId>
        <artifactId>X</artifactId>
        <version>1.0</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <dependency>
        <groupId>maven</groupId>
        <artifactId>Y</artifactId>
        <version>1.0</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>
</project>
```

在上面的示例中，Z 从 X 和 Y 导入托管依赖项。但是，X 和 Y 都包含依赖项 a。在这里，将使用 a 的 1.1 版，因为首先声明了 X 而没有在 Z 的依赖项管理中声明 a。

这个过程是递归的。例如，如果 X 导入了另一个 POM，Q，那么在处理 Z 时，它只会显示 Q 的所有托管依赖项都在 X 中定义。

#### 5.4.2物料清单 (BOM) POM

当用于定义通常是多项目构建的一部分的相关工件的“库”时，导入是最有效的。一个项目使用这些库中的一个或多个工件是相当普遍的。但是，有时很难使用工件使项目中的版本与库中分发的版本保持同步。下面的模式说明了如何创建“物料清单”(BOM) 以供其他项目使用。

项目的根是 BOM POM。它定义了将在库中创建的所有工件的版本。希望使用该库的其他项目应将此 POM 导入其 POM 的依赖项管理部分。

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.test</groupId>
  <artifactId>bom</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>
  <properties>
    <project1Version>1.0.0</project1Version>
    <project2Version>1.0.0</project2Version>
  </properties>
 
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>com.test</groupId>
        <artifactId>project1</artifactId>
        <version>${project1Version}</version>
      </dependency>
      <dependency>
        <groupId>com.test</groupId>
        <artifactId>project2</artifactId>
        <version>${project2Version}</version>
      </dependency>
    </dependencies>
  </dependencyManagement>
 
  <modules>
    <module>parent</module>
  </modules>
</project>
```

父子项目将 BOM POM 作为其父项。它是一个普通的多项目 pom。

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.test</groupId>
    <version>1.0.0</version>
    <artifactId>bom</artifactId>
  </parent>
 
  <groupId>com.test</groupId>
  <artifactId>parent</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>
 
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.12</version>
      </dependency>
      <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.1.1</version>
      </dependency>
    </dependencies>
  </dependencyManagement>
  <modules>
    <module>project1</module>
    <module>project2</module>
  </modules>
</project>
```

接下来是实际的项目 POM。

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.test</groupId>
    <version>1.0.0</version>
    <artifactId>parent</artifactId>
  </parent>
  <groupId>com.test</groupId>
  <artifactId>project1</artifactId>
  <version>${project1Version}</version>
  <packaging>jar</packaging>
 
  <dependencies>
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
    </dependency>
  </dependencies>
</project>
 
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.test</groupId>
    <version>1.0.0</version>
    <artifactId>parent</artifactId>
  </parent>
  <groupId>com.test</groupId>
  <artifactId>project2</artifactId>
  <version>${project2Version}</version>
  <packaging>jar</packaging>
 
  <dependencies>
    <dependency>
      <groupId>commons-logging</groupId>
      <artifactId>commons-logging</artifactId>
    </dependency>
  </dependencies>
</project>
```

下面的项目展示了如何在另一个项目中使用该库，而无需指定依赖项目的版本。

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.test</groupId>
  <artifactId>use</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>
 
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>com.test</groupId>
        <artifactId>bom</artifactId>
        <version>1.0.0</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>com.test</groupId>
      <artifactId>project1</artifactId>
    </dependency>
    <dependency>
      <groupId>com.test</groupId>
      <artifactId>project2</artifactId>
    </dependency>
  </dependencies>
</project>
```

最后，在创建导入依赖项的项目时，请注意以下几点：

- 不要尝试导入在当前 POM 的子模块中定义的 POM。尝试这样做将导致构建失败，因为它将无法找到 POM。
- 切勿将导入 POM 的 POM 声明为目标 POM 的父级（或祖父级等）。没有办法解决循环问题，会抛出异常。
- 当提及其 POM 具有可传递依赖项的工件时，项目需要将这些工件的版本指定为托管依赖项。不这样做会导致构建失败，因为工件可能没有指定版本。（在任何情况下，这都应被视为最佳实践，因为它可以防止工件的版本从一个构建更改为下一个构建）。

### 5.5系统依赖

```
Important note: This is deprecated.
```

与范围*系统的*依赖关系始终可用，不会在存储库中查找。它们通常用于告诉 Maven 由 JDK 或 VM 提供的依赖项。因此，系统依赖关系对于解决现在由 JDK 提供的工件的依赖关系特别有用，但之前可以单独下载。典型示例是 JDBC 标准扩展或 Java 身份验证和授权服务 (JAAS)。

一个简单的例子是：

```
<project>
  ...
  <dependencies>
    <dependency>
      <groupId>javax.sql</groupId>
      <artifactId>jdbc-stdext</artifactId>
      <version>2.0</version>
      <scope>system</scope>
      <systemPath>${java.home}/lib/rt.jar</systemPath>
    </dependency>
  </dependencies>
  ...
</project>
```

如果您的工件由 JDK `tools.jar`提供，则系统路径将定义如下：

```
<project>
  ...
  <dependencies>
    <dependency>
      <groupId>sun.jdk</groupId>
      <artifactId>tools</artifactId>
      <version>1.5.0</version>
      <scope>system</scope>
      <systemPath>${java.home}/../lib/tools.jar</systemPath>
    </dependency>
  </dependencies>
  ...
</project>
```