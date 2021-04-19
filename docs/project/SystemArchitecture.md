# 1.电商演变

![图片](https://uploader.shimo.im/f/8PfrW3wLqrtlfPCw.png!thumbnail?fileGuid=vxxxyRRtwGcTVttP)

# 2.运行效果

项目下载地址：http://git.jiagouedu.com/java-vip/tuling-shop.git

前端：wukong/ aaa111

后端：admin/123456

前提条件：Redis、mysql、zookeeper

创建数据库脚本

代码下载来之后编译。

启动顺序：shop-goods、shop-member、shop-trade、shop-pay、

shop-admin、shop-web

![图片](https://uploader.shimo.im/f/ur2RPPgDl5VNLGXK.png!thumbnail?fileGuid=vxxxyRRtwGcTVttP)

![图片](https://uploader.shimo.im/f/dFaPc4e1UWNcV7OC.png!thumbnail?fileGuid=vxxxyRRtwGcTVttP)

# 3.环境详解

192.168.0.15：base 服务

192.168.0.16：app 服务

Depoly.sh 下载、解压

env-set.sh 赋值、杀进程、启动

Pom.sh

每次解压会从 app-conf 目录下 copy 环境的配置

第三方 jar 包如何上传到私服。

mvn deploy:deploy-file -DgroupId=com.alipay -DartifactId=alipay-sdk-java -Dversion=3.3.0 -Dpackaging=jar -Dfile=F:\workspace\vipdev\tuling-shop\alipay-sdk-java-3.3.0.jar -Durl=http://192.168.0.15:7777/nexus/content/repositories/thirdparty/

# 4.架构详解

新增加能：

1、完善部分模块(shop-web 和 shop-admin)
2、冗余代码
3、模块分离（shop-admin、shop-web）

4、支付功能（shop-pay、shop-pay-client）
5、分布式事务

6、版本模块

Jeeshop 开源网站改过来的 单体应用

半个时间 分布式网站

秒杀、商品详细、库存、分库分表、分布式事务。

![图片](https://uploader.shimo.im/f/ZN7171C0mdKcFG63.png!thumbnail?fileGuid=vxxxyRRtwGcTVttP)

![图片](https://uploader.shimo.im/f/L3HISayQ5DkUsbBR.png!thumbnail?fileGuid=vxxxyRRtwGcTVttP)

*Client 都是 dubbo 对外暴露接口

问题？为什么要这么设计？解耦 对外接口（不需要业务）

并且我们接口升级之后 不会影响我们的服务

Dubbo 所有接口模块

不同的业务按不同的 dubbo 模块

优点：解耦，不会暴露给调用方具体的业务代码、职责单一。

缺点：带来一定工作量 繁琐

拆的还不够细。

