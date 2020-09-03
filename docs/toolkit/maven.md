##### [Apache Maven Project](https://maven.apache.org/)

Apache Maven是一个软件项目管理和理解工具。基于项目对象模型(POM)的概念，Maven可以从一个中心信息段管理项目的构建、报告和文档。

##### 一、依赖范围

compile:编译依赖范围。默认使用该依赖范围。使用此依赖范围的maven依赖，对于编译、测试、运行三种classpath都有效。

runtime:运行时依赖。对于测试和运行classpath有效，在编译代码时无效。(jdbc驱动)

test:测试依赖范围。只对测试classpath有效。（junit）

system:系统依赖范围。和provided依赖范围完全一致。必须通过systemPath元素显示指定依赖文件的路径。

provided:已提供依赖范围。（servlet）

##### 二、依赖管理

###### a、依赖调解

> 项目A有这样的依赖关系：A-> B->C->X(1.0)，A->D->X(2.0) 哪个版本的X会被maven解析使用呢？
>
> **第一原则：路径最近者优先。 X(2.0)被解析**。
>
> A-> B->Y(1.0)，A->C->X(2.0)，依赖长度一样，都为2。谁会被解析使用呢？
>
> **第二原则：第一声明者优先**。

###### b、可选依赖 

> <optional>true</optional>表示依赖为可选依赖，他们只对当前项目产生影响，当其他项目依赖当前项目是，这个依赖不会传递。

###### c、排除依赖

> <exclusions><exclusion>......</exclusion></exclusions>



##### 三、仓库分类

- remote repository：相当于公共的仓库，大家都能访问到，一般可以用URL的形式访问
  - 中央仓库：http://repo1.maven.org/maven2/ 
  - 私服：内网自建的maven repository，其URL是一个内部网址 
  - 其他公共仓库：其他可以互联网公共访问maven repository，例如 jboss repository等
- local repository：存放在本地磁盘的一个文件夹，例如，windows上默认是C:\Users\｛用户名｝\.m2\repository目录

默认maven远程仓库地址

maven-3.xxx版本之后，在maven可以找到这个文件：/lib/maven-model-builder- `$`{version}.jar中；\org\apache\maven\model\pom-4.0.0.xml

###### 配置远程仓库

a、项目中pom配置

```java
<project>  
  <repositories>  
    <repository>  
      <id>maven-net-cn</id>  
      <name>Maven China Mirror</name>   <url>http://maven.net.cn/content/groups/public/</url>  
      <releases>  
        <enabled>true</enabled>  
      </releases>  
      <snapshots>  
        <enabled>false</enabled>  
      </snapshots>  
    </repository>  
  </repositories>  
  <pluginRepositories>  
    <pluginRepository>  
      <id>maven-net-cn</id>  
      <name>Maven China Mirror</name>  
  <url>http://maven.net.cn/content/groups/public/</url>  
      <releases>  
        <enabled>true</enabled>  
      </releases>  
      <snapshots>  
        <enabled>false</enabled>  
      </snapshots>      
    </pluginRepository>  
  </pluginRepositories>  
</project>  

```

b、settings.xml配置

```java
<settings>  
  <profile>
      <id>jdk-1.4</id>
      <activation>
        <jdk>1.4</jdk>
      </activation>

      <repositories>
        <repository>
          <id>jdk14</id>
          <name>Repository for JDK 1.4 builds</name> <url>http://www.myhost.com/maven/jdk14</url>
          <layout>default</layout> <snapshotPolicy>always</snapshotPolicy>
        </repository>
      </repositories>
    </profile> 
</settings>  
```

> 定义一个id为dev的profile，将所有repositories以及pluginRepositories元素放到这个profile中，然后，使用activeProfile元素自动激活该profile。这样，你就不用再为每个POM重复配置仓库。

##### 四、镜像Mirror

> 1. 如果你的地理位置附近有一个速度更快的central镜像；
>
> 2. 你想覆盖central仓库配置；
>
> 3. 你想为所有POM使用唯一的一个远程仓库（这个远程仓库代理的所有必要的其它仓库）。
>
> <font color='red'>注： 如果配置了镜像，maven将不再去找中央工厂统一去找镜像地址下载</font>


mirror相当于一个拦截器，它会拦截maven对remote repository的相关请求，把请求里的remote repository地址，重定向到mirror里配置的地址。

```java
<settings>  
  <mirrors>  
    <mirror>  
      <id>maven-net-cn</id>  
      <name>Maven China Mirror</name>  
      <url>http://maven.net.cn/content/groups/public/</url>  
      <mirrorOf>central</mirrorOf>  
    </mirror>  
  </mirrors>  
</settings> 
```

##### 五、发布构件

###### a、项目中pom配置

```java
<!--定义snapshots库和releases库的nexus地址-->  
    <distributionManagement>  
        <repository>  
            <id>nexus-releases</id>  
            <url>   http://172.17.103.59:8081/nexus/content/repositories/releases/  
            </url>  
        </repository>  
        <snapshotRepository>  
            <id>nexus-snapshots</id>  
            <url>   http://172.17.103.59:8081/nexus/content/repositories/snapshots/  
            </url>  
        </snapshotRepository>  
    </distributionManagement>  
```

###### b、settings.xml配置

```java
<servers>
    <server>  
      <id>nexus-releases</id>  
      <username>admin</username>  
      <password>admin123</password>  
    </server>  
    <server>  
      <id>nexus-snapshots</id>  
      <username>admin</username>  
      <password>admin123</password>  
    </server>
</servers>
```



##### 六、maven插件

官方两个插件列表，

第一个roupId为org.apache.maven.plugins，http://maven.apache.org/plugins/index.html

第二个GroupId为org.codehaus.mojo，http://mojo.codehaus.org/plugins.html

| plugin                 | function                                              | life cycle phase        |
| ---------------------- | ----------------------------------------------------- | ----------------------- |
| maven-clean-plugin     | 清理上一次执行创建的目标文件                          | clean                   |
| maven-resources-plugin | 处理源资源文件和测试资源文件                          | resources,testResources |
| maven-compiler-plugin  | 编译源文件和测试源文件                                | compile,testCompile     |
| maven-surefire-plugin  | 执行测试文件                                          | test                    |
| maven-jar-plugin       | 创建 jar                                              | jar                     |
| maven-install-plugin   | 安装 jar，将创建生成的 jar 拷贝到 .m2/repository 下面 | install                 |
| maven-deploy-plugin    | 发布 jar                                              | deploy                  |

##### 七、变量

- 内置变量 `${basedir}` `${version}`

- POM属性 `${project.groupId}` (通过 . 来访问pom元素)

- 自定义属性：<properties> 中的属性， ${ 属性名 }

- Settings属性：settings 里的属性，通过 `${ settings.??? }` 访问

- Java 属性：所有Java 属性都可以直接访问, 如 `${ user.home }`

- 环境变量: 通过 `${ env.??? }` 访问

##### 八、命令

修改子module工程版本为父工程版本：

mvn -N versions:update-child-modules -DgenerateBackupPoms=false