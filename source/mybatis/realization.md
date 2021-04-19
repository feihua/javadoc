**mybatis****核心概念**


**Configuration****、****SqlSessionFactory****、****Session****、****Executor****、****MappedStatement****、****StatementHandler****、****ResultSetHandler**










**整体认识****mybatis****源码包**




**Mybatis****集成****Spring**


MyBatis-Spring会帮助你将MyBatis代码无缝地整合到Spring中。 使用这个类库中的类, Spring将会加载必要的MyBatis工厂类和session类。 这个类库也提供一个简单的方式来注入MyBatis数据映射器和SqlSession到业务层的bean中。 而且它也会处理事务,翻译MyBatis的异常到Spring的DataAccessException异常(数据访问异常,译者注)中。最终,它并 不会依赖于MyBatis,Spring或MyBatis-Spring来构建应用程序代码。




1. 配置

<**dependency**>

<**groupId**>org.mybatis</**groupId**>

<**artifactId**>mybatis-spring</**artifactId**>

<**version**>1.3.0</**version**>

</**dependency**>




<**bean**![图片](https://uploader.shimo.im/f/n2Gi0r4e3jCRa0c2.png!thumbnail?fileGuid=FuyY885vp30f4yOb)**id****="sqlSessionFactory"**

![图片](https://uploader.shimo.im/f/oPpNH0D69K45HRBl.png!thumbnail?fileGuid=FuyY885vp30f4yOb)

**class****="org.mybatis.spring.SqlSessionFactoryBean"**>****<**property**![图片](https://uploader.shimo.im/f/pSPfc8T1af0mddbt.png!thumbnail?fileGuid=FuyY885vp30f4yOb)**name****="dataSource"**![图片](https://uploader.shimo.im/f/m2fhbQQDwTzsScM7.png!thumbnail?fileGuid=FuyY885vp30f4yOb)**ref****="dataSource"**![图片](https://uploader.shimo.im/f/jT1upyO3MtgrjwYf.png!thumbnail?fileGuid=FuyY885vp30f4yOb)/>*<!--<property name="mapperLocations"*

![图片](https://uploader.shimo.im/f/O3nuNxXtpdSNHQXt.png!thumbnail?fileGuid=FuyY885vp30f4yOb)![图片](https://uploader.shimo.im/f/7bDo6bj0B1JcabD0.png!thumbnail?fileGuid=FuyY885vp30f4yOb)

*value="classpath*:mybatis/UserMapper.xml" />-->*</**bean**>

![图片](https://uploader.shimo.im/f/O1G9wyng735YsJlW.png!thumbnail?fileGuid=FuyY885vp30f4yOb)


<**bean**![图片](https://uploader.shimo.im/f/eRqgEnFaJFJPYx1s.png!thumbnail?fileGuid=FuyY885vp30f4yOb)**class****="org.mybatis.spring.mapper.MapperScannerConfigurer"**>****<**property**![图片](https://uploader.shimo.im/f/6Fx5iHi6Hxp14zmn.png!thumbnail?fileGuid=FuyY885vp30f4yOb)**name****="basePackage"**![图片](https://uploader.shimo.im/f/BBDwD7uLt1rKeoz7.png!thumbnail?fileGuid=FuyY885vp30f4yOb)**value****="com.jiagouedu.mapper"**![图片](https://uploader.shimo.im/f/ApSeNhRy2BtSsp8O.png!thumbnail?fileGuid=FuyY885vp30f4yOb)/>

![图片](https://uploader.shimo.im/f/6aUKw7RmOE8bWfM8.png!thumbnail?fileGuid=FuyY885vp30f4yOb)![图片](https://uploader.shimo.im/f/4BwxR880YGnyCzyj.png!thumbnail?fileGuid=FuyY885vp30f4yOb)![图片](https://uploader.shimo.im/f/F5KxBuYyrpOXXB33.png!thumbnail?fileGuid=FuyY885vp30f4yOb)

</**bean**>

![图片](https://uploader.shimo.im/f/ZjlnNQOzNrmvFkKe.png!thumbnail?fileGuid=FuyY885vp30f4yOb)![图片](https://uploader.shimo.im/f/ZjlnNQOzNrmvFkKe.png!thumbnail?fileGuid=FuyY885vp30f4yOb)







org.mybatis.spring.SqlSessionTemplate等同于SqlSession

**通用****mapper&mybatis-plus**


Mybatis增强

![图片](https://uploader.shimo.im/f/QqQsWVid6z1BETnt.jpeg!thumbnail?fileGuid=FuyY885vp30f4yOb)
















反射+泛型技术 通用CRUD


**徒手实现****mybatis**


不健全，方便我们去了解mybatis

也就是以后你的简历上可以写你熟悉mybatis源码




不用必做的作业、mybatis的1：N N:N

熟悉mybatis、mybatis源码分析、徒手实现mybatis

