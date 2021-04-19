


**mybatis****核心概念**


**Configuration****、****SqlSessionFactory****、****Session****、****Executor****、****MappedStatement****、****StatementHandler****、****ResultSetHandler**





**整体认识****mybatis****源码包**




**Debug****一行行看代码**

![图片](https://uploader.shimo.im/f/NFSYFvz72WiG6FHy.jpeg!thumbnail?fileGuid=pSZLJa4xIhYMZJMl)









宏观 微观 画图

![图片](https://uploader.shimo.im/f/dvucstYE0bfYA8YV.jpeg!thumbnail?fileGuid=pSZLJa4xIhYMZJMl)
































**Mybatis****之****Configuration**


org.apache.ibatis.builder.xml.XMLConfigBuilder#parseConfiguration




**Properties****文件解析：**


org.apache.ibatis.builder.xml.XMLConfigBuilder#propertiesElement


**Setting 文件解析：**

org.apache.ibatis.builder.xml.XMLConfigBuilder#settingsElement


**environments 文件解析：**


org.apache.ibatis.builder.xml.XMLConfigBuilder#environmentsElement




**Mybatis****之****Session**

![图片](https://uploader.shimo.im/f/BIBlgFqcuMKk0Bkx.jpeg!thumbnail?fileGuid=pSZLJa4xIhYMZJMl)





















**Mybatis****之****Mapper**




:Type interface com.jiagouedu.UserMapper is not known to the MapperRegistry.

![图片](https://uploader.shimo.im/f/m00xx5bDXEuun75E.jpeg!thumbnail?fileGuid=pSZLJa4xIhYMZJMl)



**Mybatis****之****Sql**


* txt.txt文件

**Mybatis****之****Executor**

![图片](https://uploader.shimo.im/f/RCcV4PTnOH0G21ZY.png!thumbnail?fileGuid=pSZLJa4xIhYMZJMl)










































其实是不干事情的

org.apache.ibatis.executor.statement.StatementHandler

org.apache.ibatis.executor.statement.PreparedStatementHandler

org.apache.ibatis.executor.resultset.ResultSetHandler

org.apache.ibatis.executor.resultset.DefaultResultSetHandler

**Mybatis****之****cache****：**


每当我们使用MyBatis开启一次和数据库的会话，MyBatis会创建出一个SqlSession对象表示一次数据库会话。 在对数据库的一次会话中，我们有可能会反复地执行完全相同的查询语句，如果不采取一些措施的话，每一次查询都会查询一次数据库,而我们在极短的时间内做了完全相同的查询，那么它们的结果极有可能完全相同，由于查询一次数据库的代价很大，这有可能造成很大的资源浪费。

为了解决这一问题，减少资源的浪费，MyBatis会在表示会话的SqlSession对象中建立一个简单的缓存，将每次查询到的结果结果缓存起来，当下次查询的时候，如果判断先前有个完全一样的查询，会直接从缓存中直接将结果取出，返回给用户，不需要再进行一次数据库查询了。

cache key: id +sql+limit+offsetxxx


作用域：一级缓存session


全局：二级缓存

![图片](https://uploader.shimo.im/f/tLMk6b8RNqUt9hKZ.jpeg!thumbnail?fileGuid=pSZLJa4xIhYMZJMl)



















**今天源码分析涉及到的模式是（学员整理）：**

1.sqlSessionFactory工厂

2.build建造者

3. getInstance  ，Cache  单例
4. 委派  （单词忘了）装饰

5.InterceptorChain责任链

6Proxy代理

7.ExecuteCommand命令

8.doQuery模板







**Mybatis****作业**

annotations中的@select源码加载执行

![图片](https://uploader.shimo.im/f/hZspc114Dl6dR1p2.jpeg!thumbnail?fileGuid=pSZLJa4xIhYMZJMl)



















提交地址：

[http://git.jiagouedu.com/java-vip/tuling-mybatis/issues/new](http://git.jiagouedu.com/java-vip/tuling-mybatis/issues/new?fileGuid=pSZLJa4xIhYMZJMl)

要求：流程图+找出代码调用流程

