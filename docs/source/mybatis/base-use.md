**三节课：**

熟悉mybatis

源码分析

带徒手mybatis

**传统JDBC的弊端：**


总结：

1、jdbc底层没有用连接池、操作数据库需要频繁的创建和关联链接。消耗很大的资源

2、写原生的jdbc代码在java中，一旦我们要修改sql的话，java需要整体编译，不利于系统维护

3、使用PreparedStatement预编译的话对变量进行设置123数字，这样的序号不利于维护

4、返回result结果集也需要硬编码。





**mybatis**介绍：


Mybatyis:Object relation mapping对象关系映射

**快速开始**mybatis（xml方式）：

1、maven

<dependency>

<groupId>org.mybatis</groupId>

<artifactId>mybatis</artifactId>

<version>x.x.x</version></dependency>


2、mybatis-config.xml

3、Mapper.xml


**Mybatis****全局配置详解：**

![图片](https://uploader.shimo.im/f/FQiVYStxXnO9bO35.jpeg!thumbnail?fileGuid=YIohP5jiQK0yZZpp)



**Mybatis**之annotation：



**public interface**UserMapper {

@Select(**"select * from user where id=#{id}"**)

**public**User selectUser(Integer id);

}



*Mybatis之注解和*xml优缺点:


Xml：增加xml文件、麻烦、条件不确定、容易出错，特殊字符转义

注释：不适合复杂sql，收集sql不方便，重新编译


Mybatis之#与**$区别：


参数标记符号

#预编译，防止sql注入(推荐)

$可以sql注入，代替作用


Mybatis之parameterType与parameterMap区别：


通过parameterType指定输入参数的类型，类型可以是简单类型、hashmap、pojo的包装类型





Mybatis之resultType与resultMap区别：


使用resultType进行输出映射，只有查询出来的列名和pojo中的属性名一致，该列才可以映射成功。

mybatis中使用resultMap完成高级输出结果映射。


Mybatis之plugin：


com.jiagouedu.mybatis.plugin.SqlPrintInterceptor



**自定义****reusltMap****（逆向工程中讲）：**



**Mybatis****逆向工程：**



**什么是逆向工程****:**


MyBatis的一个主要的特点就是需要程序员自己编写sql，那么如果表太多的话，难免会很麻烦，所以mybatis官方提供了一个逆向工程，可以针对单表自动生成mybatis执行所需要的代码（包括mapper.xml、mapper.java、po..）。一般在开发中，常用的逆向工程方式是通过数据库的表生成代码




1、引入jar

<**plugin**>

<**groupId**>org.mybatis.generator</**groupId**>

<**artifactId**>mybatis-generator-maven-plugin</**artifactId**>

<**version**>1.3.7</**version**>

<**dependencies**>

<**dependency**>

<**groupId**>mysql</**groupId**>

<**artifactId**>mysql-connector-java</**artifactId**>

<**version**>${mysql-connector-java.version}</**version**>

</**dependency**>

</**dependencies**>

</**plugin**>



2、配置mybatis-genrtator.xml

3、mybatis-generator:generate

XMLMAPPER|ANNOTATEDMAPPER



**去掉注释：**


<!--注释去掉-->

<commentGenerator >

<property name="suppressAllComments" value="true"/> </commentGenerator>




**生成注解方式****:**


[http://www.mybatis.org/generator/configreference/javaClientGenerator.html](http://www.mybatis.org/generator/configreference/javaClientGenerator.html?fileGuid=YIohP5jiQK0yZZpp)



