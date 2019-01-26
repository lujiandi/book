# maven

> maven 基于项目管理的对象模型(POM),可用一小段描述信息来管理项目的构建、报告和文档的 Java 项目管理工具

#### 安装

1. 设置系统环境变量 `M2_HOME`(maven 安装目录)
2. 添加 `%M2_HOME%/bin`到系统变量`Path`

#### 常用命令

- mvn -v
- mvn test
- mvn clean
- mvn compile
- mvn package -Dmaven.test.skip=true -P 环境变量
- mvn install

#### 通过命令行创建项目

- `mvn archetype:generate`

#### maven 中的坐标和仓库

`坐标:`

> 用来标识 maven 构件,包含`groupId`、`articleId`、`version`标识.

> `groupId:`组织名(网址反写+项目名)

> `articleId:`项目名-模块名

`仓库:`

> 用来管理项目依赖，包含本地仓库和远程仓库。

#### 修改本地仓库存放地址

> 修改\${maven 安装目录}\conf\settings.xml

```
<localRepository>D:/developer/apache-maven-3.3.1/rep</localRepository>
```

#### maven 镜像仓库

> 修改\${maven 安装目录}\conf\settings.xml

`阿里云:`

```
    <mirror>
      <id>alimaven</id>
      <mirrorOf>central</mirrorOf>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </mirror>
```

#### maven 中如何指定 jdk 的版本

1. 全局配置:指在 \${MAVEN_HOME}\conf\settings.xml 中进行配置，例如，要配置 jdk1.8, 打开 settings.xml 这个文件，然后在 \<profiles> \</profiles> 之间添加如下代码

   ```
   <profile>
   <id>jdk1.8</id>
   <activation>
   <activeByDefault>true</activeByDefault>
   <jdk>1.8</jdk>
   </activation>
   <properties>
   <maven.compiler.source>1.8</maven.compiler.source>
   <maven.compiler.target>1.8</maven.compiler.target>
   <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
   </properties>
   </profile>
   ```

   全局配置的好处就是省事、方便。一次配置以后，再使用 maven 构建项目，项目编译
   时，默认使用 jdk1.8 进行编译。

2. 局部配置就是只针对具体某个项目进行配置的。具体就是，在项目的 pom.xml 文件中
   添加如下代码

   ```
   <build>
   <plugins>
   <plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-compiler-plugin</artifactId>
   <configuration>
   <source>1.7</source>
   <target>1.7</target>
   </configuration>
   </plugin>
   </plugins>
   <build>
   ```

#### maven webapp 项目结构

![maven webapp 项目结构](images/maven-web.png)

#### maven plugins

```
// tomcat
<plugin>
   <groupId>org.apache.tomcat.maven</groupId>
   <artifactId>tomcat7-maven-plugin</artifactId>
   <version>2.2</version>
   <configuration>
       <path>/</path>
       <port>8080</port>
       <uriEncoding>UTF-8</uriEncoding>
   </configuration>
</plugin>

```
