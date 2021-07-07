**概要：**

1. maven 基本概念
2. maven 核心配置
3. maven 生命周期
4. Maven 自定义插件开发
# **1.maven  安装与核心概念**

---


概要：

1. maven 安装
2. maven 编译(compile)
3. 执行测试用例(test)
4. maven 打包
5. maven  依懒管理
## **1**.1安装

1. 官网下载 Maven （[http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi?fileGuid=htV36dpryC9GdJQY)）
2. 解压指定目录
3. 配置环境变量
4. 检查安装是否成功 （mvn -version）

maven 是什么？它的基本功能是什么？**编译**、**打包**、**测试、依赖管理**直观感受一下 maven 编译打包的过程。

## **1.2**maven 编译

```powershell
mvn compile
```

maven 采用了约定的方式从指项目结构中获取源码与资源文件进行编译打包。

```powershell
1. 主源码文件：${project}/src/main/java
2. 主资源文件：${project}/src/main/resources
3. 测试源码文件：${project}/src/test/java
4. 测试资源文件：${project}/src/test/resources
```

## **1.3**Maven打包

```powershell
mvn package
```

## 1.4maven 单元测试演示

```java
mvn test
```



## **1.5**maven 依赖管理

* 在 pom 文件中添加 junit 依赖

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>

```

**maven 核心功能总结：**

1. maven 核心作用是编译、测试、打包。
2. 根目录下的 pom.xml 文件设置分组 ID 与 artifactId。
3. maven 基于约定的方式从项目中获取源码与资源文件进行编译打包。
4. 对于项目所依懒的组件与会本地仓库引用，如果本地仓库不存在则会从中央仓库下载。
# **2.**maven核心配置

---


概要：

1. 项目依懒(内部、外部)
2. 项目聚合与继承
3. 项目构建配置

项目依赖是指 maven 通过依赖传播、依赖优先原则、可选依赖、排除依赖、依赖范围等特性来管理项目ClassPath。

## 2.1依赖传播特性:

我们的项目通常需要依赖第三方组件，而第三方组件又会依赖其它组件遇到这种情况 Maven 会将依赖网络中的所有节点都会加入 ClassPath 当中，这就是 Maven 的依赖传播特性。

    * 举例演示 Spring MVC 的依赖网络

```xml
<!-- 添加 spring web mvc 演示 -->

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.0.4.RELEASE</version>
</dependency>
```

## **2**.2依赖优先原则

基于依赖传播特性，导致整个依赖网络会很复杂，难免会出现相同组件不同版本的情况。Maven 此时会基于依赖优先原则选择其中一个版本。

第一原则：最短路径优先。

第二原则：相同路径下配置在前的优先。

## 2.3可选依赖

可选依赖表示这个依赖不是必须的。通过在 <dependency> 添  <optional>true</optional> 表示，默认是不可选的。可选依赖不会被传递。

## **2.4**排除依赖

即排除指定的间接依赖。通过配置 <exclusions> 配置排除指定组件。

<!-- 排除指定项目 -->

```xml
<exclusions>
    <exclusion>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
    </exclusion>
</exclusions>
```

## **2.5**依赖范围

像 junit 这个组件 我们只有在运行测试用例的时候去要用到，这就没有必要在打包的时候把 junit.jar 包过构建进去，可以通过 Mave 的依赖范围配置<scope>来达到这种目的。maven 总共支持以下四种依赖范围：

**compile(默认)**: 编译范围，编译和打包都会依赖。

**provided：**提供范围，编译时依赖，但不会打包进去。如：servlet-api.jar

**runtime：**运行时范围，打包时依赖，编译不会。如：mysql-connector-java.jar

**test：**测试范围，编译运行测试用例依赖，不会打包进去。如：junit.jar

**system：**表示由系统中 CLASSPATH 指定。编译时依赖，不会打包进去。配合<systemPath> 一起使用。示例：java.home 下的 tool.jar

system 除了可以用于引入系统 classpath 中包，也可以用于引入系统非 maven  收录的第三方 Jar，做法是将第三方 Jar 放置在 项目的 lib 目录下，然后配置 相对路径，但因 system 不会打包进去所以需要配合 maven-dependency-plugin 插件配合使用。当然推荐大家还是通过 将第三方 Jar 手动 install 到仓库。



#手动加入本地仓库

```powershell
mvn install:install-file -Dfile=abc_client_v1.20.jar -DgroupId=tuling  -DartifactId=tuling-client -Dversion=1.20 -Dpackaging=jar
```

**项目聚合与继承**

### **1、聚合**

是指将多个模块整合在一起，统一构建，避免一个一个的构建。聚合需要个父工程，然后使用 <modules> 进行配置其中对应的是子工程的相对路径

```xml
<modules>
    <module>xx-client</module>
    <module>xx-server</module>
</modules>
```

### **2、继承**

继承是指子工程直接继承父工程 当中的属性、依赖、插件等配置，避免重复配置。

1. 属性继承：
2. 依赖继承：
3. 插件继承：

上面的三个配置子工程都可以进行重写，重写之后以子工程的为准。

### **3、依赖管理**

通过继承的特性，子工程是可以间接依赖父工程的依赖，但多个子工程依赖有时并不一至，这时就可以在父工程中加入 <dependencyManagement> 声明该功程需要的 JAR 包，然后在子工程中引入。

<！-- 父工程中声明 junit 4.12 -->

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
</dependencyManagement>

<!-- 子工程中引入 -->

<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
</dependency>
```

### 4、项目属性：

通过 <properties> 配置 属性参数，可以简化配置。

```properties
<!-- 配置 proName 属性 -->

<properties>

<proName>ddd</proName>

</properties>

<!-- 引用方式 -->

${proName}

maven 默认的属性

${basedir} 项目根目录

${version}表示项目版本;

${project.basedir}同${basedir};

${project.version}表示项目版本,与${version}相同;

${project.build.directory} 构建目录，缺省为 target

${project.build.sourceEncoding}表示主源码的编码格式;

${project.build.sourceDirectory}表示主源码路径;

${project.build.finalName}表示输出文件名称;

${project.build.outputDirectory} 构建过程输出目录，缺省为 target/classes
```



# **3.maven 生命周期**

---

**知识点概要：**

1. 生命周期的概念与意义
2. maven 三大生命周期与其对应的 phase(阶段)
3. 生命周期与插件的关系
4. 生命周期与默认插件的绑定
## **3.1生命周期的概念与意义**

在项目构建时通常会包含清理、编译、测试、打包、验证、部署，文档生成等步骤，maven 统一对其进行了整理抽像成三个生命周期 (lifecycle)及各自对应的多个阶段(phase)。这么做的意义是：

1. 每个阶段都成为了一个扩展点，可以采用不同的方式来实现，提高了扩展性与灵活性。
2. 规范统一了 maven 的执行路径。

## **3.2maven 三大生命周期与其对应的 phase(阶段)**

maven 总共包含三大生生命周期

1. clean Lifecycle：清理生命周期，用于于清理项目
2. default Lifecycle：默认生命周期，用于编译、打包、测试、部署等
3. site Lifecycle站点文档生成，用于构建站点文档
|**生命周期(lifecycle)**|**阶段(phase)**|**描述(describe)**|
|:----|:----|:----|:----|:----|:----|
|clean Lifecycle|pre-clean|预清理|
|    |clean|清理|
|    |post-clean|清理之后|
|default Lifecycle|validate|验证|
|    |initialize|初始化|
|    |generate-sources|    |
|    |process-sources|    |
|    |generate-resources|    |
|    |process-resources|    |
|    |compile|编译|
|    |process-classes|    |
|    |generate-test-sources|    |
|    |process-test-sources|    |
|    |generate-test-resources|    |
|    |process-test-resources|    |
|    |test-compile|编译测试类|
|    |process-test-classes|    |
|    |test|执行测试|
|    |prepare-package|构建前准备|
|    |package|打包构建|
|    |pre-integration-test|    |
|    |integration-test|    |
|    |post-integration-test|    |
|    |verify|验证|
|    |install|上传到本地仓库|
|    |deploy|上传到远程仓库|
|site Lifecycle|pre-site|准备构建站点|
|    |site|构建站点|
|    |post-site|构建站点之后|
|    |site-deploy|站点部署|

三大生命周期其相互独立执行，也可以合在一起执行。但 lifecycle 中的 phase 是有严格执行的顺序的，比如必须是先执行完 compile 才能执行 pakcage 动作，此外 phase 还有包含逻辑存在，即当你执行一个 phase 时 其前面的 phase 会自动执行。



## **3.3生命周期与插件的关系**

生命周期的 phase 组成了项目过建的完整过程，但这些过程具体由谁来实现呢？这就是插件，maven 的核心部分代码量其实很少，其大部分实现都是由插件来完成的。比如：test 阶段就是由maven-surefire-plugin 实现。在 pom.xml 中我们可以设置指定插件目标(gogal)与 phase 绑定，当项目构建到达指定 phase 时就会触发些插件 gogal 的执行。 一个插件有时会实现多个 phas 比如：maven-compiler-plugin插件分别实现了 compile 和 testCompile。

总结：

生命周期的 阶段 可以绑定具体的插件及目标

不同配置下同一个阶段可以对应多个插件和目标

phase==>plugin==>goal(功能)

## **3.4生命周期与插件的默认绑定**

在我们的项目当中并没有配置 maven-compiler-plugin 插件,但当我们执行 compile 阶段时一样能够执行编译操作，原因是 maven 默认为指定阶段绑定了插件实现。列如下以下两个操作在一定程度上是等价的。

```powershell
mvn compile

#直接执行 compile 插件目标

mvn org.apache.maven.plugins:maven-compiler-plugin:3.1:compile
```




# **4.maven 自定义插件开发**

---

场景：mybatis自动生成

**知识点：**

1. 插件的相关概念
2. 常用插件的使用
3. 开发一个自定义插件
## **4.1maven 插件相关概念**

**插件坐标定位：**

插件与普通 jar 包一样包含 一组件坐标定位属性即：

groupId、artifactId、version，当使用该插件时会从本地仓库中搜索，如果没有即从远程仓库下载

<!-- 唯一定位到 dependency 插件 -->

```xml
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-dependency-plugin</artifactId>
<version>2.10</version>
```

**插件执行 execution：**

execution 配置包含一组指示插件如何执行的属性：

**id**： 执行器命名

**phase**：在什么阶段执行？

**goals**：执行一组什么目标或功能？

**configuration**：执行目标所需的配置文件？

## **4.2常用插件的使用**

```properties
除了通过配置的方式使用插件以外，Maven 也提供了通过命令直接调用插件目标其命令格式如下：

mvn groupId:artifactId:version:goal -D{参数名}

展示 pom 的依赖关系树

mvn org.apache.maven.plugins:maven-dependency-plugin:2.10:tree

也可以直接简化版的命令，但前提必须是 maven 官方插件

mvn dependency:tree

其它常用插件：

查看 pom 文件的最终配置

mvn help:effective-pom

原型项目生成

archetype:generate

#快速创建一个 WEB 程序

mvn archetype:generate -DgroupId=tuling -DartifactId=simple-webbapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false

#快速创建一个 java 项目

mvn archetype:generate -DgroupId=tuling -DartifactId=simple-java -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```



## **4.3开发一个自定义插件**

实现步骤：

    * 创建 maven 插件项目
    * 设定 packaging 为 maven-plugin
    * 添加插件依赖
    * 编写插件实现逻辑
    * 打包构建插件

