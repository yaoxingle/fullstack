Apache Maven 竭尽全力确保构建是可移植的。除此之外，这意味着允许在 POM 内部构建配置，避免**所有**文件系统引用（在继承、依赖和其他地方），并更多地依赖本地存储库来存储使这成为可能所需的元数据。

然而，有时便携性并非完全可能。在某些情况下，插件可能需要配置本地文件系统路径。在其他情况下，将需要稍微不同的依赖集，并且项目的工件名称可能需要稍作调整。在其他时候，您甚至可能需要根据检测到的构建环境在构建生命周期中包含整个插件。

为了解决这些情况，Maven 支持构建配置文件。配置文件是使用 POM 本身中可用元素的子集（加上一个额外的部分）指定的，并以多种方式中的任何一种触发。它们在构建时修改 POM，旨在用于补充集，为一组目标环境提供等效但不同的参数（例如，提供应用程序服务器根在开发、测试和生产环境）。因此，配置文件很容易导致团队不同成员产生不同的构建结果。但是，如果使用得当，可以使用配置文件，同时仍然保持项目的可移植性。这也将最大限度地减少使用`-f`的 maven 选项，它允许用户创建另一个具有不同参数或配置的 POM 来构建，这使得它更易于维护，因为它仅使用一个 POM 运行。

### 3.1有哪些不同类型的配置文件？每个在哪里定义？

- 每个项目

  \- 在 POM 本身中定义`(pom.xml)`。

- 每个用户

  \- 在[Maven-settings 中](http://maven.apache.org/ref/current/maven-settings/settings.html) 定义`(%USER_HOME%/.m2/settings.xml)`。

- 全局的

  \- 在[全局 Maven-settings 中](http://maven.apache.org/ref/current/maven-settings/settings.html) 定义`(${maven.home}/conf/settings.xml)`。

- 配置文件描述符

  \- 位于[项目 basedir 中`(profiles.xml)`](http://maven.apache.org/ref/2.2.1/maven-profile/profiles.html)的描述符（Maven 3.0 及更高版本不再支持；请参阅[Maven 3 兼容性说明](https://cwiki.apache.org/confluence/display/MAVEN/Maven+3.x+Compatibility+Notes#Maven3.xCompatibilityNotes-profiles.xml)）

### 3.2如何触发配置文件？这如何根据所使用的配置文件类型而有所不同？

可以通过多种方式激活配置文件：

- 从命令行
- 通过Maven设置
- 基于环境变量
- 操作系统设置
- 存在或丢失的文件

#### 3.2.1有关配置文件激活的详细信息

可以使用`-P`命令行标志显式指定配置文件。

此标志后跟要使用的配置文件 ID 的逗号分隔列表。除了通过其激活配置或 中的`<activeProfiles>`部分激活的任何配置文件之外，还激活选项中指定的配置文件`settings.xml`。从 Maven 4 开始，Maven 将拒绝激活或停用无法解析的配置文件。为了防止这种情况，在配置文件标识符前加上`?`，将其标记为可选：

```
mvn groupId:artifactId:goal -P profile-1,profile-2,?profile-3
```

可以通过`<activeProfiles>`部分在 Maven 设置中激活配置文件。此部分采用`<activeProfile>`元素列表，每个元素都包含一个配置文件 ID。

```
<settings>
  ...
  <activeProfiles>
    <activeProfile>profile-1</activeProfile>
  </activeProfiles>
  ...
</settings>
```

`<activeProfiles>`每次项目使用时，默认情况下都会激活标记中列出的配置文件。

可以根据检测到的构建环境状态自动触发配置文件。这些触发器是通过`<activation>`配置文件本身的一个部分指定的。目前，此检测仅限于 JDK 版本的前缀匹配、系统属性的存在或系统属性的值。这里有些例子。

以下配置将在 JDK 版本以“1.4”开头（例如“1.4.0_08”、“1.4.2_07”、“1.4”）时触发配置文件：

```
<profiles>
  <profile>
    <activation>
      <jdk>1.4</jdk>
    </activation>
    ...
  </profile>
</profiles>
```

 Maven 2.1 开始也可以从使用范围（有关更多信息，请参阅[Enforcer 版本范围语法](http://maven.apache.org/enforcer/enforcer-rules/versionRanges.html)）。以下尊重版本 1.3、1.4 和 1.5。

```
<profiles>
  <profile>
    <activation>
      <jdk>[1.3,1.6)</jdk>
    </activation>
    ...
  </profile>
</profiles>
```

*注意：*上限`,1.5]`可能不包括大多数 1.5 版本，因为它们将有一个额外的“补丁”版本，例如`_05`在上述范围内未考虑在内。

下一个将根据操作系统设置激活。有关 OS 值的更多详细信息，请参阅[Maven Enforcer 插件](http://maven.apache.org/enforcer/enforcer-rules/requireOS.html)。

```
<profiles>
  <profile>
    <activation>
      <os>
        <name>Windows XP</name>
        <family>Windows</family>
        <arch>x86</arch>
        <version>5.1.2600</version>
      </os>
    </activation>
    ...
  </profile>
</profiles>
```

当系统属性“debug”被指定为任何值时，下面的配置文件将被激活：

```
<profiles>
  <profile>
    <activation>
      <property>
        <name>debug</name>
      </property>
    </activation>
    ...
  </profile>
</profiles>
```

当根本没有定义系统属性“调试”时，将激活以下配置文件：

```
<profiles>
  <profile>
    <activation>
      <property>
        <name>!debug</name>
      </property>
    </activation>
    ...
  </profile>
</profiles>
```

当系统属性“debug”未定义或定义的值不是“true”时，将激活以下配置文件。

```
<profiles>
  <profile>
    <activation>
      <property>
        <name>debug</name>
        <value>!true</value>
      </property>
    </activation>
    ...
  </profile>
</profiles>
```

要激活它，您可以在命令行中键入其中之一：

```
mvn groupId:artifactId:goal
mvn groupId:artifactId:goal -Ddebug=false
```

下一个示例将在使用值“test”指定系统属性“environment”时触发配置文件：

```
profiles>
  <profile>
    <activation>
      <property>
        <name>environment</name>
        <value>test</value>
      </property>
    </activation>
    ...
  </profile>
</profiles>
```

要激活它，您可以在命令行中输入：

```
mvn groupId:artifactId:goal -Denvironment=test
```

从 Maven 3.0 开始，POM 中的配置文件也可以根据来自`settings.xml`.

**注意**：像这样的环境变量`FOO`可以作为表单的属性使用`env.FOO`。进一步注意，环境变量名称在 Windows 上被规范化为全部大写。

此示例将在生成的文件`target/generated-sources/axistools/wsdl2java/org/apache/maven`丢失时触发配置文件。

```
<profiles>
  <profile>
    <activation>
      <file>
        <missing>target/generated-sources/axistools/wsdl2java/org/apache/maven</missing>
      </file>
    </activation>
    ...
  </profile>
</profiles>
```

从 Maven 2.0.9 开始，标签`<exists>`和`<missing>`可以插入。支持的变量是系统属性`${user.home}`和环境变量，例如`${env.HOME}`. 请注意，在 POM 本身中定义的属性和值在此处不可用于插值，例如上面的示例激活器不能使用`${project.build.directory}`但需要硬编码 path `target`。

默认情况下，配置文件也可以使用如下配置处于活动状态：

```
profiles>
  <profile>
    <id>profile-1</id>
    <activation>
      <activeByDefault>true</activeByDefault>
    </activation>
    ...
  </profile>
</profiles>
```

除非使用前面描述的方法之一激活同一 POM 中的另一个配置文件，否则此配置文件将对所有构建自动激活。当在命令行上或通过其激活配置激活 POM 中的配置文件时，默认情况下处于活动状态的所有配置文件都会自动停用。

#### 3.2.2停用配置文件

从 Maven 2.0.10 开始，可以使用命令行停用一个或多个配置文件，方法是在其标识符前加上字符“！” 或 '-' 如下所示：

```
mvn groupId:artifactId:goal -P !profile-1,!profile-2,!?profile-3
```

或 +---+ mvn groupId:artifactId:goal -P -profile-1,-profile-2,-?profile-3 +---+

这可用于停用标记为 activeByDefault 的配置文件或否则将通过其激活配置激活的配置文件。

### 3.3每种类型的配置文件可以自定义 POM 的哪些区域

现在我们已经谈到在哪里指定配置文件，以及如何激活它们，这将是谈论有用的*东西*，你可以在配置文件中指定。与配置文件配置的其他方面一样，这个答案并不简单。

根据您选择配置配置文件的位置，您将可以访问不同的 POM 配置选项。

#### 3.3.1外部文件中的配置文件

在外部文件（即 settings.xml`或`profiles.xml`）中指定的配置文件在最严格的意义上是不可移植的。任何看起来很有可能改变构建结果的东西都被限制在 POM 中的内联配置文件中。诸如存储库列表之类的东西可能只是已批准工件的专有存储库，并且不会改变构建的结果。因此，您将只能修改`<repositories>`和`<pluginRepositories>`部分，以及额外的`<properties>`部分。

该`<properties>`部分允许您指定自由格式的键值对，这些键值对将包含在 POM 的插值过程中。这允许您以`${profile.provided.path}`.

#### 3.3.2POM 中的配置文件

在另一方面，如果你的配置文件可以指定合理在POM里面，你有更多的选择。当然，权衡是您只能修改*该*项目及其子模块。由于这些配置文件是内嵌指定的，因此更有可能保持可移植性，因此可以合理地说，您可以向它们添加更多信息，而不会有其他用户无法使用这些信息的风险。

POM 中指定的配置文件可以修改[以下 POM 元素](http://maven.apache.org/ref/current/maven-model/maven.html)：

- `<repositories>`
- `<pluginRepositories>`
- `<dependencies>`
- `<plugins>`
- `<properties>` （实际上在主 POM 中不可用，但在幕后使用）
- `<modules>`
- `<reports>`
- `<reporting>`
- `<dependencyManagement>`
- `<distributionManagement>`
- <build>元素的子集，它包括
  - `<defaultGoal>`
  - `<resources>`
  - `<testResources>`
  - `<directory>`
  - `<finalName>`
  - `<filters>`
  - `<pluginManagement>`
  - `<plugins>`

#### 3.3.3<profiles> 之外的 POM 元素

我们不允许修改 POM 配置文件之外的某些 POM 元素，因为当 POM 部署到存储库系统时，这些运行时修改将不会分发，从而使该人对该项目的构建与其他人完全不同。虽然您可以使用为外部配置文件提供的选项在一定程度上做到这一点，但危险是有限的。另一个原因是这个 POM 信息有时会从父 POM 中重用。

外部文件，例如`settings.xml`并且`profiles.xml`也不支持 POM 配置文件之外的元素。让我们以这个场景为例进行阐述。当有效的 POM 部署到远程存储库时，任何人都可以从存储库中提取其信息并使用它直接构建 Maven 项目。现在，想象一下，如果我们可以在依赖项中设置配置文件，这对构建非常重要，或者在 POM 配置文件之外的任何其他元素中设置，`settings.xml`那么我们很可能无法指望其他人从存储库中使用该 POM 并能够构建它。我们还必须考虑如何分享`settings.xml`和其他人。请注意，要配置的文件过多会非常混乱且难以维护。底线是，由于这是构建数据，它应该在 POM 中。Maven 2 的目标之一是将运行构建所需的所有信息合并到单个文件或 POM 文件层次结构中。

### 3.4配置顺序

来自活动配置文件的 POM 中的所有配置文件元素覆盖具有相同 POM 名称的全局元素，或者在集合的情况下扩展这些元素。如果多个配置文件在同一个 POM 或外部文件中处于活动状态，则**稍后**定义的配置优先于**之前**定义的配置（独立于它们的配置文件 ID 和激活顺序）。

例子：

```
<project>
  ...
  <repositories>
    <repository>
      <id>global-repo</id>
      ...
    </repository>
  </repositories>
  ...
  <profiles>
    <profile>
      <id>profile-1</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <repository>
          <id>profile-1-repo</id>
          ...
        </repository>
      </repositories>
    </profile>
    <profile>
      <id>profile-2</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <repository>
          <id>profile-2-repo</id>
          ...
        </repository>
      </repositories>
    </profile>
    ...
  </profiles>
  ...
</project>
```

这导致存储库列表：`profile-2-repo, profile-1-repo, global-repo`。

### 3.5配置文件陷阱

我们已经提到将配置文件添加到构建中可能会破坏项目的可移植性这一事实。我们甚至强调了配置文件可能破坏项目可移植性的情况。然而，值得重申这些要点，作为关于使用配置文件时要避免的一些陷阱的更连贯的讨论的一部分。

使用配置文件时要记住两个主要问题区域。首先是外部属性，通常用于插件配置。这些会带来破坏项目可移植性的风险。另一个更微妙的领域是一组自然配置文件的不完整规范。

#### 3.5.1外部属性

外部属性定义涉及在其外部定义`pom.xml`但未在其内部相应配置文件中定义的任何属性值。POM 中属性最明显的用法是在插件配置中。虽然在没有属性的情况下肯定有可能破坏项目的可移植性，但这些小动物可能会产生导致构建失败的微妙影响。例如，`settings.xml`当团队中的另一个用户尝试在没有类似`settings.xml`. 考虑以下`pom.xml`Web 应用程序项目的片段：

```
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.myco.plugins</groupId>
        <artifactId>spiffy-integrationTest-plugin</artifactId>
        <version>1.0</version>
        <configuration>
          <appserverHome>${appserver.home}</appserverHome>
        </configuration>
      </plugin>
      ...
    </plugins>
  </build>
  ...
</project>
```

现在，在你的本地`${user.home}/.m2/settings.xml`，你有：

```
<settings>
  ...
  <profiles>
    <profile>
      <id>appserverConfig</id>
      <properties>
        <appserver.home>/path/to/appserver</appserver.home>
      </properties>
    </profile>
  </profiles>
 
  <activeProfiles>
    <activeProfile>appserverConfig</activeProfile>
  </activeProfiles>
  ...
</settings>
```

当您构建**集成测试**生命周期阶段时，您的集成测试就会通过，因为您提供的路径允许测试插件安装和测试此 Web 应用程序。

*然而*，当你的同事试图构建到**集成测试时**，他的构建失败了，抱怨它无法解析插件配置参数`<appserverHome>`，或者更糟糕的是，该参数的值 - 从字面上看`${appserver.home}`- 无效（如果它警告你的话） .

恭喜，您的项目现在是不可移植的。在您的配置文件中内联此配置文件`pom.xml`可以帮助缓解这种情况，但明显的缺点是每个项目层次结构（允许继承的影响）现在必须指定此信息。由于 Maven 为项目继承提供了良好的支持，因此可以将此类配置粘贴在`<pluginManagement>`团队级 POM 或类似的部分中，并简单地继承路径。

另一个不太有吸引力的答案可能是开发环境的标准化。然而，这往往会损害 Maven 能够提供的生产力增益。

#### 3.5.2自然剖面集的不完整规范

除了上述便携性破坏者之外，很容易无法用您的配置文件涵盖所有情况。当你这样做时，你通常会离开你的目标环境之一高处和干燥。让我们`pom.xml`再看一次上面的示例片段：

```
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.myco.plugins</groupId>
        <artifactId>spiffy-integrationTest-plugin</artifactId>
        <version>1.0</version>
        <configuration>
          <appserverHome>${appserver.home}</appserverHome>
        </configuration>
      </plugin>
      ...
    </plugins>
  </build>
  ...
</project>
```

现在，考虑以下配置文件，它将在 中内联指定`pom.xml`：

```
<project>
  ...
  <profiles>
    <profile>
      <id>appserverConfig-dev</id>
      <activation>
        <property>
          <name>env</name>
          <value>dev</value>
        </property>
      </activation>
      <properties>
        <appserver.home>/path/to/dev/appserver</appserver.home>
      </properties>
    </profile>
 
    <profile>
      <id>appserverConfig-dev-2</id>
      <activation>
        <property>
          <name>env</name>
          <value>dev-2</value>
        </property>
      </activation>
      <properties>
        <appserver.home>/path/to/another/dev/appserver2</appserver.home>
      </properties>
    </profile>
  </profiles>
  ..
</project>
```

此配置文件看起来与上一个示例中的配置文件非常相似，但有一些重要的例外：它显然是面向开发环境的，`appserverConfig-dev-2`添加了一个名为的新配置文件，并且它有一个激活部分，当系统属性包含“ env=dev" 用于名为 的配置文件`appserverConfig-dev`， "env=dev-2" 用于名为`appserverConfig-dev-2`. 所以，执行：

```
mvn -Denv=dev-2 integration-test
```

将导致成功构建，应用名为 的配置文件给出的属性`appserverConfig-dev-2`。当我们执行

```
mvn -Denv=dev integration-test
```

它将导致成功构建应用名为`appserverConfig-dev`. 但是，执行：

```
mvn -Denv=production integration-test
```

不会成功构建。为什么？因为，生成的非插值文字值`${appserver.home}`将不是部署和测试 Web 应用程序的有效路径。我们在编写配置文件时没有考虑生产环境的情况。“生产”环境 (env=production) 与“测试”甚至可能是“本地”一起构成了一组自然的目标环境，我们可能希望为其构建集成测试生命周期阶段。这个自然集的不完整规范意味着我们已经有效地将我们的有效目标环境限制在开发环境中。您的队友 - 可能还有您的经理 - 不会看到其中的幽默。当您构建配置文件来处理此类情况时，请确保处理整个目标排列集。

顺便说一句，用户特定的配置文件可以以类似的方式运行。这意味着当团队添加新的开发人员时，用于处理针对用户的不同环境的配置文件可以起作用。虽然我认为这*可以*作为对新手的有用培训，但以这种方式将他们扔给狼群并不好。同样，一定要考虑*整套*配置文件。

### 3.6如何判断构建期间哪些配置文件有效？

确定活动配置文件将帮助用户了解在构建期间执行了哪些特定配置文件。我们可以使用[Maven 帮助插件](http://maven.apache.org/plugins/maven-help-plugin/)来告诉构建期间哪些配置文件有效。

```
 mvn help:active-profiles
```

让我们提供一些小样本，以帮助我们更多地了解该插件的*active-profiles*目标。

从 中的最后一个配置文件示例中`pom.xml`，您会注意到有两个已命名的配置文件`appserverConfig-dev`，`appserverConfig-dev-2`它们已被赋予不同的属性值。如果我们继续执行：

```
 mvn help:active-profiles -Denv=dev
```

结果将是配置文件 id 的项目符号列表，其激活属性为“env=dev”以及声明它的源。请参阅下面的示例。

```
The following profiles are active:

 - appserverConfig-dev (source: pom)
```

现在，如果我们在中声明了一个配置文件`settings.xml`（请参阅中的配置文件示例`settings.xml`）并且已将其设置为活动配置文件并执行：

```
  mvn help:active-profiles
```

结果应该是这样的

```
The following profiles are active:

 - appserverConfig (source: settings.xml)
```

即使我们没有激活属性，配置文件也已列为活动状态。为什么？就像我们之前提到的，在 中设置为活动配置文件的配置文件`settings.xml`会自动激活。

现在，如果我们有一个类似配置文件的东西，`settings.xml`它已被设置为活动配置文件，并在 POM 中触发了一个配置文件。您认为哪个配置文件会对构建产生影响？

```
  mvn help:active-profiles -P appserverConfig-dev
```

这将列出激活的配置文件：

```
The following profiles are active:

 - appserverConfig-dev (source: pom)
 - appserverConfig (source: settings.xml)
```

即使它列出了两个活动配置文件，我们也不确定应用了其中的哪一个。要查看对构建的影响，请执行：

```
mvn help:effective-pom -P appserverConfig-dev
```

这将将此构建配置的有效 POM 打印到控制台。请注意，`settings.xml`在 POM 中的配置文件比 POM 中的配置文件具有更高的优先级。所以这里应用的配置文件`appserverConfig`不是`appserverConfig-dev`.

如果要将插件的输出重定向到名为 的文件`effective-pom.xml`，请使用命令行选项`-Doutput=effective-pom.xml`。

### 3.7命名约定

到目前为止，您已经注意到配置文件是解决不同目标环境的不同构建配置要求问题的自然方式。上面，我们讨论了解决这种情况的配置文件“自然集”的概念，以及考虑将需要的整个配置文件集的重要性。

然而，如何组织和管理该集合的演变的问题也很重要。正如优秀的开发人员努力编写自文档化代码一样，重要的是您的个人资料 ID 提示他们的预期用途。实现此目的的一种好方法是使用通用系统属性触发器作为配置文件名称的一部分。对于由系统属性**env**触发的配置文件，这可能会导致名称类似于**env-dev**、**env-test**和**env-prod**。这样的系统就如何激活针对特定环境的构建留下了非常直观的提示。因此，要为测试环境激活构建，您需要通过发出以下命令来激活**env-test**：

```
mvn -Denv=test <phase>
```

只需在配置文件 ID 中用“=”替换“-”即可获得正确的命令行选项。

