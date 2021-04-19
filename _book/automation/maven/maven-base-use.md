**概要：**

1. maven 基本概念
2. maven 核心配置
3. maven 生命周期
4. Maven 自定义插件开发
5. 基于 nexus 构建企业私服
# **1.maven  安装与核心概念**

---


概要：

1. maven 安装
2. maven 编译(compile)
3. 执行测试用例(test)
4. maven 打包
5. maven  依懒管理
## **1**.1安装：

1. 官网下载 Maven （[http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi?fileGuid=htV36dpryC9GdJQY)）
2. 解压指定目录
3. 配置环境变量
4. 检查安装是否成功 （mvn -version）

maven 是什么？它的基本功能是什么？**编译**、**打包**、**测试、依赖管理**直观感受一下 maven 编译打包的过程。

## **1.2**maven 编译

**maven 编译过程演示**

* 创建 maven 项目。
* 创建 src 文件
* 编写 pom 文件
* 执行编译命令

编写 pom 文件基础配置

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
 
<groupId>org.codehaus.mojo</groupId>
<artifactId>my-project</artifactId>
<version>1.0.SNAPSHOT</version>
</project>
```
mvn 编译命令

mvn compile

---------------------------

[INFO] No sources to compile

[INFO] ---------------------------------------------------------------

[INFO] BUILD SUCCESS

[INFO] ---------------------------------------------------------------

[INFO] Total time: 0.473 s

[INFO] Finished at: 2018-08-05T15:55:44+08:00

[INFO] Final Memory: 6M/153M

[INFO] ---------------------------------------------------------------


请注意，在上述配置和命令当中，我们并没有指定源码文件在哪里？最后编译到哪里去？在这里

maven 采用了约定的方式从指项目结构中获取源码与资源文件进行编译打包。

    1. 主源码文件：${project}/src/main/java
    2. 主资源文件：${project}/src/main/resources
    3. 测试源码文件：${project}/src/test/java
    4. 测试资源文件：${project}/src/test/resources

将 java 文件移至 src/main/java 目录，重新执行编译.

mv src/hello.java /src/main/java/hello.java

mvn compile;

### 
## **1.3**Maven打包

maven 打包演示

mvn 打包命令

mvn package

## 1.4maven 单元测试演示

* 编写测试类
* 执行测试命令


编译测试类

# 创建测试目录

mkdir -p /src/test/java

# 编写 测试类

vim TestHello.java

#测试类代码------------------------

package com.test.tuling;

public class TestHello{

public void sayHelloTest(){

System.out.println("run test .....");

}

}

执行测试指令:

#执行测试

mvn test

执行完指令发现没有执行我们的测试方法，这是为何？原因在于 maven 当中的测试类又做了约定，约定必须是 Test 开头的类名与 test 开头的方法才会执行。

重新修改方法名后 在执行 mvn test 即可正常执行。

package com.test.tuling;

public class TestHello{

public void testsayHelloTest(){

System.out.println("run test .....");

}

}

通常测试我们是通过 junit 来编译测试用例，这时就就需添加 junit 的依赖。

## **1.5**maven 依赖管理

* 在 pom 文件中添加 junit 依赖
* 修改测试类，加入 junit 代码
* 执行测试命令

加入依懒配置

<dependencies>

<dependency>

<groupId>junit</groupId>

<artifactId>junit</artifactId>

<version>4.0</version>

<scope>test</scope>

</dependency>

</dependencies>

修改测试类引入 junit 类.

//引入 junit 类

import org.junit.Assert;

import org.junit.Test;

Assert.assertEquals("","hi");

注意：当我们在 classPath 当中加入 junit，原来以 test 开头的方法不会被执行，必须加入 @Test 注解才能被执行。

**提问：**

在刚才的演示过程当中 ，junit jar 包在哪里？是怎么加入到 classPath 当中去的？maven 是在执行 test 命令的时间 动态从本地仓库中去引入 junit jar 包，如果找不到就会去远程仓库下载，然后在引入。

![图片](https://uploader.shimo.im/f/Jm7hJrXP8NrPVKbd.png!thumbnail?fileGuid=htV36dpryC9GdJQY)

**默认远程仓库：**

默认远程仓库 maven central 其配置在

maven-model-builder-3.2.1.jar\org\apache\maven\model\pom-4.0.0.xml 位置

**本地仓库位置：**

本地仓库位置默认在 ~/.m2/respository 下

要修改${M2_HOME}/conf/settings.xml  来指定仓库目录

<!-- 指定本地仓库目录-->

<localRepository>G:\.m2\repository</localRepository>

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
## **项目依懒**

项目依赖是指 maven 通过依赖传播、依赖优先原则、可选依赖、排除依赖、依赖范围等特性来管理项目ClassPath。

## **2.1依赖传播特性:

我们的项目通常需要依赖第三方组件，而第三方组件又会依赖其它组件遇到这种情况 Maven 会将依赖网络中的所有节点都会加入 ClassPath 当中，这就是 Maven 的依赖传播特性。

    * 举例演示 Spring MVC 的依赖网络

<!-- 添加 spring web mvc 演示 -->

<dependency>

<groupId>org.springframework</groupId>

<artifactId>spring-webmvc</artifactId>

<version>4.0.4.RELEASE</version>

</dependency>

![图片](https://uploader.shimo.im/f/ghvrpEYzNxQ0bu7d.png!thumbnail?fileGuid=htV36dpryC9GdJQY)

在刚刚的演示当中，项目直接依赖了 spring-webmvc 叫直接依赖，而对 commons-logging 依赖是通过 webmvc 传递的所以叫间接依赖。

## **2**.2依赖优先原则

基于依赖传播特性，导致整个依赖网络会很复杂，难免会出现相同组件不同版本的情况。Maven 此时会基于依赖优先原则选择其中一个版本。

第一原则：最短路径优先。

第二原则：相同路径下配置在前的优先。

    * 第一原则演示

<!-- 直接添加 commons-logging -->

<dependency>

<groupId>commons-logging</groupId>

<artifactId>commons-logging</artifactId>

<version>1.2</version>

</dependency>

上述例子中 commons-logging 通过 spring-webmvc 依赖了 1.1.3，而项目中直接依赖了 1.2，基于最短路径原则项目最终引入的是 1.2 版本。

    * 第二原则演示：

**步骤：**

1. 添加一个新工程 Project B
2. 配置 Project B 依赖 spring-web.3.2.9.RELEASE
3. 当前工程直接依赖 Project B

配置完之后，当前工程 project A 有两条路径可以依赖 spring-web,选择哪一条 就取决于 对 webmvc 和 Project B 的配置先后顺序。

Project A==> spring-webmvc.4.0.0.RELEASE ==>spring-web.4.0.0.RELEASE

Project A==>   Project B 1.0.SNAPSHOT ==>spring-web.3.2.9.RELEASE


注意：在同一 pom 文件，第二原则不在适应。如下配置，最终引用的是 1.2 版本，而不是配置在前面的 1.1.1 版本.

<!--  在 1.2 之前添加 commons-logging -->

<dependency>

<groupId>commons-logging</groupId>

<artifactId>commons-logging</artifactId>

<version>1.1.1</version>

</dependency>

<dependency>

<groupId>commons-logging</groupId>

<artifactId>commons-logging</artifactId>

<version>1.2</version>

</dependency>

## 2.3可选依赖

可选依赖表示这个依赖不是必须的。通过在 <dependency> 添  <optional>true</optional> 表示，默认是不可选的。可选依赖不会被传递。

* 演示可选依赖的效果。
## **2.4**排除依赖

即排除指定的间接依赖。通过配置 <exclusions> 配置排除指定组件。

<!-- 排除指定项目 -->

<exclusions>

<exclusion>

<groupId>org.springframework</groupId>

<artifactId>spring-web</artifactId>

</exclusion>

</exclusions>

* 演示排除依赖
## **2.5**依赖范围

像 junit 这个组件 我们只有在运行测试用例的时候去要用到，这就没有必要在打包的时候把 junit.jar 包过构建进去，可以通过 Mave 的依赖范围配置<scope>来达到这种目的。maven 总共支持以下四种依赖范围：

**compile(默认)**: 编译范围，编译和打包都会依赖。

**provided：**提供范围，编译时依赖，但不会打包进去。如：servlet-api.jar

**runtime：**运行时范围，打包时依赖，编译不会。如：mysql-connector-java.jar

**test：**测试范围，编译运行测试用例依赖，不会打包进去。如：junit.jar

**system：**表示由系统中 CLASSPATH 指定。编译时依赖，不会打包进去。配合<systemPath> 一起使用。示例：java.home 下的 tool.jar

system 除了可以用于引入系统 classpath 中包，也可以用于引入系统非 maven  收录的第三方 Jar，做法是将第三方 Jar 放置在 项目的 lib 目录下，然后配置 相对路径，但因 system 不会打包进去所以需要配合 maven-dependency-plugin 插件配合使用。当然推荐大家还是通过 将第三方 Jar 手动 install 到仓库。

<!-- system 的通常使用方式-->

<dependency>

<groupId>com.sun</groupId>

<artifactId>tools</artifactId>

<version>${java.version}</version>

<scope>system</scope>

<optional>true</optional>

<systemPath>${java.home}/../lib/tools.jar</systemPath>

</dependency>

<!-- system 另外使用方式 ,将工程内的 jar 直接引入 -->

<dependency>

<groupId>jsr</groupId>

<artifactId>jsr</artifactId>

<version>3.5</version>

<scope>system</scope>

<optional>true</optional>

<systemPath>${basedir}/lib/jsr305.jar</systemPath>

</dependency>

<!-- 通过插件 将 system 的 jar 打包进去。 -->

<plugin>

<groupId>org.apache.maven.plugins</groupId>

<artifactId>maven-dependency-plugin</artifactId>

<version>2.10</version>

<executions>

<execution>

<id>copy-dependencies</id>

<phase>compile</phase>

<goals>

<goal>copy-dependencies</goal>

</goals>

<configuration>

<outputDirectory>${project.build.directory}/${project.build.finalName}/WEB-INF/lib</outputDirectory>

<includeScope>system</includeScope>

<excludeGroupIds>com.sun</excludeGroupIds>

</configuration>

</execution>

</executions>

</plugin>

#手动加入本地仓库

mvn install:install-file -Dfile=abc_client_v1.20.jar -DgroupId=tuling  -DartifactId=tuling-client -Dversion=1.20 -Dpackaging=jar

## **项目聚合与继承**

### **1、聚合**

是指将多个模块整合在一起，统一构建，避免一个一个的构建。聚合需要个父工程，然后使用 <modules> 进行配置其中对应的是子工程的相对路径

<modules>

<module>tuling-client</module>

<module>tuling-server</module>

</modules>

    * 演示聚合的配置
### **2、继承**

继承是指子工程直接继承父工程 当中的属性、依赖、插件等配置，避免重复配置。

1. 属性继承：
2. 依赖继承：
3. 插件继承：

上面的三个配置子工程都可以进行重写，重写之后以子工程的为准。

### **3、依赖管理**

通过继承的特性，子工程是可以间接依赖父工程的依赖，但多个子工程依赖有时并不一至，这时就可以在父工程中加入 <dependencyManagement> 声明该功程需要的 JAR 包，然后在子工程中引入。

<！-- 父工程中声明 junit 4.12 -->

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

4、项目属性：

通过 <properties> 配置 属性参数，可以简化配置。

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

## **项目构建配置**

1. 构建资源配置
2. 编译插件
3. profile 指定编译环境

**构建资源配置**

基本配置示例：

<defaultGoal>package</defaultGoal>

<directory>${basedir}/target2</directory>

<finalName>${artifactId}-${version}</finalName>

说明：

defaultGoal，执行构建时默认的 goal 或 phase，如 jar:jar 或者 package 等

directory，构建的结果所在的路径，默认为${basedir}/target 目录

finalName，构建的最终结果的名字，该名字可能在其他 plugin 中被改变


<resources>  配置示例

<resources>

<resource>

<directory>src/main/java</directory>

<includes>

<include>**/*.MF</include>

<include>**/*.XML</include>

</includes>

<filtering>true</filtering>

</resource>

<resource>

<directory>src/main/resources</directory>

<includes>

<include>**/*</include>

<include>*</include>

</includes>

<filtering>true</filtering>

</resource>

</resources>

说明：

* resources，build 过程中涉及的资源文件
    * **targetPath**，资源文件的目标路径
    * **directory**，资源文件的路径，默认位于${basedir}/src/main/resources/目录下
    * **includes**，一组文件名的匹配模式，被匹配的资源文件将被构建过程处理
    * **excludes**，一组文件名的匹配模式，被匹配的资源文件将被构建过程忽略。同时被 includes 和 excludes 匹配的资源文件，将被忽略。
    * **filtering**： 默认 false，true 表示 通过参数 对 资源文件中 的${key} 在编译时进行动态变更。替换源可 紧 -Dkey 和 pom 中的<properties> 值 或  <filters> 中指定的 properties 文件。
# **3.maven 生命周期**

---


## **知识点概要：**

1. 生命周期的概念与意义
2. maven 三大生命周期与其对应的 phase(阶段)
3. 生命周期与插件的关系
4. 生命周期与默认插件的绑定
## **3.1生命周期的概念与意义**

在项目构建时通常会包含清理、编译、测试、打包、验证、部署，文档生成等步骤，maven 统一对其进行了整理抽像成三个生命周期 (lifecycle)及各自对应的多个阶段(phase)。这么做的意义是：

1. 每个阶段都成为了一个扩展点，可以采用不同的方式来实现，提高了扩展性与灵活性。
2. 规范统一了 maven 的执行路径。

在执行项目构建阶段时可以采用 jar 方式构建也可以采用 war 包方式构建提高了灵活性。我们可以通过命令 mvn ${phase name}直接触发指定阶段的执行如：

* 演示 phase 的执行

#执行清理 phase

mvn clean

#执行 compile phase

mvn compile

# 也可以同时执行 清理加编译

mvn clean comile

![图片](https://uploader.shimo.im/f/GOtKtaQXzGh80iiW.png!thumbnail?fileGuid=htV36dpryC9GdJQY)

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

* 演示 phase 执行

# 执行编译

mvn compile

# 执行打包就包含了编译指令的执行

mvn package

## **3.3生命周期与插件的关系**

生命周期的 phase 组成了项目过建的完整过程，但这些过程具体由谁来实现呢？这就是插件，maven 的核心部分代码量其实很少，其大部分实现都是由插件来完成的。比如：test 阶段就是由maven-surefire-plugin 实现。在 pom.xml 中我们可以设置指定插件目标(gogal)与 phase 绑定，当项目构建到达指定 phase 时就会触发些插件 gogal 的执行。 一个插件有时会实现多个 phas 比如：maven-compiler-plugin插件分别实现了 compile 和 testCompile。

总结：

生命周期的 阶段 可以绑定具体的插件及目标

不同配置下同一个阶段可以对应多个插件和目标

phase==>plugin==>goal(功能)

## **3.4生命周期与插件的默认绑定**

在我们的项目当中并没有配置 maven-compiler-plugin 插件,但当我们执行 compile 阶段时一样能够执行编译操作，原因是 maven 默认为指定阶段绑定了插件实现。列如下以下两个操作在一定程度上是等价的。

* 演示

#

mvn compile

#直接执行 compile 插件目标

mvn org.apache.maven.plugins:maven-compiler-plugin:3.1:compile

lifecycle phase 的默认绑定见下表：。

clean Lifecycle 默认绑定

<phases>

<phase>pre-clean</phase>

<phase>clean</phase>

<phase>post-clean</phase>

</phases>

<default-phases>

<clean>

org.apache.maven.plugins:maven-clean-plugin:2.5:clean

</clean>

</default-phases>

site Lifecycle 默认绑定

<phases>

<phase>pre-site</phase>

<phase>site</phase>

<phase>post-site</phase>

<phase>site-deploy</phase>

</phases>

<default-phases>

<site>

org.apache.maven.plugins:maven-site-plugin:3.3:site

</site>

<site-deploy>

org.apache.maven.plugins:maven-site-plugin:3.3:deploy

</site-deploy>

</default-phases>


Default Lifecycle JAR 默认绑定

注：不同的项目类型 其默认绑定是不同的，这里只指列举了 packaging 为 jar 的默认绑定，全部的默认绑定参见：[https://maven.apache.org/ref/3.5.4/maven-core/default-bindings.html#](https://maven.apache.org/ref/3.5.4/maven-core/default-bindings.html#?fileGuid=htV36dpryC9GdJQY)。

<phases>

<process-resources>

org.apache.maven.plugins:maven-resources-plugin:2.6:resources

</process-resources>

<compile>

org.apache.maven.plugins:maven-compiler-plugin:3.1:compile

</compile>

<process-test-resources>

org.apache.maven.plugins:maven-resources-plugin:2.6:testResources

</process-test-resources>

<test-compile>

org.apache.maven.plugins:maven-compiler-plugin:3.1:testCompile

</test-compile>

<test>

org.apache.maven.plugins:maven-surefire-plugin:2.12.4:test

</test>

<package>

org.apache.maven.plugins:maven-jar-plugin:2.4:jar

</package>

<install>

org.apache.maven.plugins:maven-install-plugin:2.4:install

</install>

<deploy>

org.apache.maven.plugins:maven-deploy-plugin:2.7:deploy

</deploy>

</phases>


# **4.maven 自定义插件开发**

---


## **知识点：**

1. 插件的相关概念
2. 常用插件的使用
3. 开发一个自定义插件
## **4.1maven 插件相关概念**

**插件坐标定位：**

插件与普通 jar 包一样包含 一组件坐标定位属性即：

groupId、artifactId、version，当使用该插件时会从本地仓库中搜索，如果没有即从远程仓库下载

<!-- 唯一定位到 dependency 插件 -->

<groupId>org.apache.maven.plugins</groupId>

<artifactId>maven-dependency-plugin</artifactId>

<version>2.10</version>


**插件执行 execution：**

execution 配置包含一组指示插件如何执行的属性：

**id**： 执行器命名

**phase**：在什么阶段执行？

**goals**：执行一组什么目标或功能？

**configuration**：执行目标所需的配置文件？

* 演示一个插件的配置与使用

# 将插件依赖拷贝到指定目录

<plugin>

<groupId>org.apache.maven.plugins</groupId>

<artifactId>maven-dependency-plugin</artifactId>

<version>3.1.1</version>

<executions>

<execution>

<id>copy-dependencies</id>

<phase>package</phase>

<goals>

<goal>copy-dependencies</goal>

</goals>

<configuration>              <outputDirectory>${project.build.directory}/alternateLocation</outputDirectory>

<overWriteReleases>false</overWriteReleases>

<overWriteSnapshots>true</overWriteSnapshots>

<excludeTransitive>true</excludeTransitive>

</configuration>

</execution>

</executions>

</plugin>



## **4.2常用插件的使用**

除了通过配置的方式使用插件以外，Maven 也提供了通过命令直接调用插件目标其命令格式如下：

mvn groupId:artifactId:version:goal -D{参数名}

* 演示通过命令执行插件

# 展示 pom 的依赖关系树

mvn org.apache.maven.plugins:maven-dependency-plugin:2.10:tree

# 也可以直接简化版的命令，但前提必须是 maven 官方插件

mvn dependency:tree

其它常用插件：

# 查看 pom 文件的最终配置

mvn help:effective-pom

# 原型项目生成

archetype:generate

#快速创建一个 WEB 程序

mvn archetype:generate -DgroupId=tuling -DartifactId=simple-webbapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false

#快速创建一个 java 项目

mvn archetype:generate -DgroupId=tuling -DartifactId=simple-java -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

## **4.3开发一个自定义插件**

实现步骤：

    * 创建 maven 插件项目
    * 设定 packaging 为 maven-plugin
    * 添加插件依赖
    * 编写插件实现逻辑
    * 打包构建插件

插件 pom 配置

<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0"

xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

<modelVersion>4.0.0</modelVersion>

<groupId>tuling</groupId>

<version>1.0.SNAPSHOT</version>

<artifactId>tuling-maven-plugin</artifactId>

<packaging>maven-plugin</packaging>

<dependencies>

<dependency>

<groupId>org.apache.maven</groupId>

<artifactId>maven-plugin-api</artifactId>

<version>3.0</version>

</dependency>

<dependency>

<groupId>org.apache.maven.plugin-tools</groupId>

<artifactId>maven-plugin-annotations</artifactId>

<version>3.4</version>

</dependency>

</dependencies>

</project>

插件实现类：

package com.tuling.maven;

import javafx.beans.DefaultProperty;

import org.apache.maven.plugin.AbstractMojo;

import org.apache.maven.plugin.MojoExecutionException;

import org.apache.maven.plugin.MojoFailureException;

import org.apache.maven.plugins.annotations.LifecyclePhase;

import org.apache.maven.plugins.annotations.Mojo;

import org.apache.maven.plugins.annotations.Parameter;

/**

* @author Tommy

*         Created by Tommy on 2018/8/8

**/

@Mojo(name = "luban")

public class LubanPlugin extends AbstractMojo {

@Parameter

String sex;

@Parameter

String describe;

public void execute() throws MojoExecutionException, MojoFailureException {

getLog().info(String.format("luban sex=%s describe=%s",sex,describe));

}

}



# **5.nexus 私服搭建与核心功能**

---


### **知识点概要:**

1. 私服的使用场景
2. nexus 下载安装
3. nexus 仓库介绍
4. 本地远程仓库配置
5. 发布项目至 nexus 远程仓库
6. 关于 SNAPSHOT(快照)与 RELEASE(释放) 版本说明
## **5.1私服使用场景**

私服使用场景如下：

1、公司不能连接公网，可以用一个私服务来统一连接

2、公司内部 jar 组件的共享

## **5.2nexus 下载安装**

**nexus 下载地址：**

[https://sonatype-download.global.ssl.fastly.net/nexus/oss/nexus-2.14.5-02-bundle.tar.gz](https://sonatype-download.global.ssl.fastly.net/nexus/oss/nexus-2.14.5-02-bundle.tar.gz?fileGuid=htV36dpryC9GdJQY)

**解压并设置环境变量**

#解压

shell>tar -zxvf nexus-2.14.5-02-bundle.tar.gz

#在环境变量当中设置启动用户

shell> vim /etc/profile

#添加 profile 文件。安全起见不建议使用 root 用户，如果使用其它用户需要加相应权限

export RUN_AS_USER=root

**配置启动参数：**

shell> vi ${nexusBase}/conf/nexus.properties

#端口号

application-port=9999

启动与停止 nexus

#启动

shell>  ${nexusBase}/bin/nexus start

#停止

shell>  ${nexusBase}/bin/nexus stop

登录 nexus 界面

地址：http://{ip}:9999/nexus/

用户名:admin

密码：admin123

## **5.3nexus 仓库介绍**

3rd party：第三方仓库

Apache Snapshots：apache 快照仓库

Central: maven 中央仓库

Releases：私有发布版本仓库

Snapshots：私有 快照版本仓库

## **5.4本地远程仓库配置**

在 pom 中配置远程仓库

<repositories>

<repository>

<id>nexus-public</id>

<name>my nexus repository</name>

<url>http://192.168.0.147:9999/nexus/content/groups/public/</url>

</repository>

</repositories>

或者在 settings.xml 文件中配置远程仓库镜像 效果一样，但作用范围广了

<mirror>

<id>nexus-aliyun</id>

<mirrorOf>*</mirrorOf>

<name>Nexus aliyun</name>

<url>http://192.168.0.147:9999/nexus/content/groups/public/</url>

</mirror>

## **5.5发布项目至 nexus 远程仓库**

配置仓库地址

<distributionManagement>

<repository>

<id>nexus-release</id>

<name>nexus release</name>

<url>http://192.168.0.147:9999/nexus/content/repositories/releases/</url>

</repository>

<snapshotRepository>

<id>nexus-snapshot</id>

<name>nexus snapshot</name>

<url>http://192.168.0.147:9999/nexus/content/repositories/snapshots/</url>

</snapshotRepository>

</distributionManagement>

设置 setting.xml 中设置 server

<server>

<id>nexus-snapshot</id>

<username>deployment</username>

<password>deployment123</password>

</server>

<server>

<id>nexus-release</id>

<username>deployment</username>

<password>deployment123</password>

</server>

执行 deploy 命令

mvn deploy











