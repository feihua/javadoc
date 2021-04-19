# 1.商品中心

![图片](https://uploader.shimo.im/f/EtFD28vWlLVrWrqq.png!thumbnail?fileGuid=XYvt6CvQxtTXVwgQ)

商品中心：

## 1.1分类+商品（1对多）



![图片](https://uploader.shimo.im/f/oGJd58RRSU6u4tcv.png!thumbnail?fileGuid=XYvt6CvQxtTXVwgQ)

t_catalog商品分类表

|参数|名称|类型|备注|
|:----|:----|:----|:----|
|id|自增长|int|唯一|
|name|分类名称|String|    |
|code|编码，简码|String|唯一|
|pid|父ID|Int|    |
|order1|排序|Int|    |
|type|类型|String|类型，a:文章目录；p:产品目录|
|showInNav|是否显示在首页的导航条上|String|y:显示,n:不显示；默认n。<br>仅对type=p有效|

t_product商品表

|字段名|数据类型|默认值|描述|
|:----|:----|:----|:----|
|id|int|    |唯一，商品ID|
|catalogID|varchar|    |商品类别catalog表id|
|name|varchar|    |商品名称|
|introduce|text|    |商品简介|
|price|DECIMAL(9,2)|    |定价|
|nowPrice|DECIMAL(9,2)|    |现价|
|picture|String|    |小图片地址|
|score|Int|0|赠送积分|
|maxPicture|String|    |大图片地址|
|isnew|String|n|是否新品。n：否，y：是|
|sale|String|n|是否特价。n：否，y：是|
|activityID|String|    |绑定的活动ID|
|giftID|String|    |绑定的礼品ID|
|hit|int|0|浏览次数|
|unit|String|    |商品单位。默认“item:件”|
|createAccount|String|    |录入人账号|
|createtime|datetime|    |录入时间|
|updateAccount|String|    |最后修改人账号|
|updatetime|String|    |最后修改时间|
|isTimePromotion|String|n|是否限时促销。n：否，y：是|
|status|Int|0|商品状态。1：新增，2：已上架，3：已下架|
|productHTML|LONGTEXT|    |商品介绍|
|images|String|    |商品多张图片集合，逗号分割|
|sellcount|Int|销售数量|默认：0|
|stock|Int|剩余库存数|默认：0|
|searchKey|String|    |搜索关键词|
|title|String|    |页面标题|
|description|String|    |页面描述|
|keywords|String|    |页面关键词|

## 1.2分类+商品+品牌：

![图片](https://uploader.shimo.im/f/5jqmS7QlEd3rVmwE.png!thumbnail?fileGuid=XYvt6CvQxtTXVwgQ)

我要查看苹果所有的产品（手机苹果、电脑苹果）需求

![图片](https://uploader.shimo.im/f/ld8PClILPUpCBVVA.png!thumbnail?fileGuid=XYvt6CvQxtTXVwgQ)

## 1.3分类+商品+品牌+属性：

![图片](https://uploader.shimo.im/f/yP18elO7A9E23Xpq.png!thumbnail?fileGuid=XYvt6CvQxtTXVwgQ)

更加快速找到我们想购买的商品

![图片](https://uploader.shimo.im/f/uy1QecCIRp86rGXI.png!thumbnail?fileGuid=XYvt6CvQxtTXVwgQ)

t_attribute商品属性(参数)表

|参数|名称|类型|备注|
|:----|:----|:----|:----|
|id|自增长|int|唯一|
|name|属性/参数名称|String|    |
|catalogID|类别ID|Int|    |
|pid|父ID|Int|该字段具有双重含义。<br>0表示属性大类，一般情况下产品只有两层attribute，一层为属性名称类别，一层为属性；-1：参数|
|order1|排序|Int|    |

t_attribute_link商品属性(参数)中间表

|参数|名称|类型|备注|
|:----|:----|:----|:----|
|id|自增长|int|唯一|
|attrID|属性(参数)ID|Int|    |
|productID|商品ID|Int|    |
|value|商品参数值|String|名称从属性表中取得|

## 1.4分类+商品+品牌+属性+规格：

t_spec商品规格表

|字段名|数据类型|默认值|描述|
|:----|:----|:----|:----|
|id|int|    |唯一|
|productID|String|    |商品ID|
|specColor|String|    |颜色|
|specSize|String|    |尺寸|
|specStock|Int|    |此规格的商品库存数|
|specPrice|Double|    |此规格的商品价格|
|specStatus|String|    |y:显示规格；n:不显示规格|

![图片](https://uploader.shimo.im/f/PxZg2XN433H1NiOT.png!thumbnail?fileGuid=XYvt6CvQxtTXVwgQ)

SPU ：SPU(Standard Product Unit)：标准化产品单元。是商品信息聚合的最小单位，是一组可复用、易检索的标准化信息的集合，该集合描述了一个产品的特性。通俗点讲，属性值、特性相同的商品就可以称为一个SPU。

举例：Iphone6

SKU： SKU=Stock Keeping Unit（库存量单位）。即库存进出计量的基本单元，可以是以件，盒，托盘等为单位。

举例：Iphone6+土豪金+32G

C2C店铺

![图片](https://uploader.shimo.im/f/OptfFcBOSb30B7Kg.png!thumbnail?fileGuid=XYvt6CvQxtTXVwgQ)


## 1.5技术点

商品检索：ES、SOLR （数据源来自数据库，那就意味着同步）、分词

商品展示：商品+图片+库存+店铺+商品相关的信息

图片：GFS、TFS、FastDFs底层原理？特点：文件小、图片小

并发下问题

缓存：查询的速度、内存》硬盘（数据源来自数据库，那就意味着同步）

增量：增加、修改、下架

全量：预热数据（某个活动所有商品加载缓存中）

静态化: 把html+CDN

缺点:更新上需要更新HTML

# 2.会员中心

会员基础服务：登录、注册、购物、评价、晒单

会员成长体系：购物、评价、晒单

会员等级（注册会员、铜牌会员、银牌会员、金牌会员、钻石会员 ）

## 2.1t_account会员表

|字段名|数据类型|主键|唯一|描述|
|:----|:----|:----|:----|:----|
|id|int|Y|    |会员ID|
|nickname|varchar|    |y|昵称|
|account|varchar|    |y|用户名<br>_out_当前时间戳|
|password|varchar|    |    |密码|
|accountType|String|    |    |会员类型。<br>qq,sinawb,alipay|
|trueName|String|    |    |真实姓名|
|sex|String|    |    |性别。m:男,f：女,s:保密|
|birthday|Date|    |    |出生年月日|
|province|String|    |    |省份|
|city|varchar|    |    |所在城市|
|address|varchar|    |    |联系地址|
|postcode|varchar|    |    |邮政编码|
|cardNO|varchar|    |    |证件号码|
|cardType|varchar|    |    |证件类型|
|grade|int|    |    |等级|
|amount|money|    |    |消费额|
|tel|varchar|    |    |电话|
|email|varchar|    |y|Email地址|
|emailIsActive|String|    |    |邮箱是否已激活。y:已激活,n:未激活。默认n|
|freeze|String|    |    |是否冻结 n：未冻结，y：已冻结；默认n|
|freezeStartdate|Date|    |    |冻结的开始日期|
|freezeEnddate|Date|    |    |冻结的结束日期|
|lastLoginTime|Date|    |    |最后登录时间|
|lastLoginIp|String|    |    |最后登录IP|
|lastLoginArea|String|    |    |最后登陆地点|
|diffAreaLogin|String|    |    |是否是异地登陆y:是,n:否|
|regeistDate|Date|    |    |注册日期|
|addressID|Int|    |    |配送信息ID|
|openId|String|    |y|QQ登陆返回|
|accessToken|String|    |    |QQ登陆返回|
|alipayUseId|String|    |y|支付宝快捷登陆返回的用户ID|
|sinaWeiboID|String|    |y|新浪微博登陆返回的用户ID|
|rank|String|    |    |会员等级。和t_accountType.code进行挂钩。默认R1|
|score|Int|    |    |会员积分。默认0|

## 2.2t_accountRank会员级别表

|字段名|数据类型|是否主键|描述|
|:----|:----|:----|:----|
|id|int|是|自增|
|code|String|    |级别编码<br>R1：普通会员，0-499<br>R2：铜牌会员，积分范围500-999<br>R3：银牌会员，1000-1999<br>R4：金牌会员，2000-4000<br>R5：钻石会员，大于4000|
|name|String|    |级别名称|
|minScore|Int|    |最小积分|
|maxScore|Int|    |最大积分|
|remark|String|    |备注|

## 2.3t_address配送地址信息表

|字段名|数据类型|是否主键|描述|
|:----|:----|:----|:----|
|id|Int|是|自增|
|account|String|    |会员账号|
|name|String|    |收货人姓名|
|province|String|    |省份|
|city|String|    |城市|
|area|String|    |区域|
|pcadetail|String|    |省市区的地址中文合并|
|address|String|    |收货人详细地址|
|zip|String|    |收货人邮编|
|phone|String|    |收货人电话号码|
|mobile|String|    |收货人手机号码|
|isdefault|String|默认n|是否默认；n=不是,y=默认|

技术点：

单点登录、业务功能、会员迁移（分库分表）

Login.jd.com item.jd.com 分布式会话解决方案

分库：hash取模、list、range

16个库（800万数据量） hash、不好扩展 1.28亿用户 均匀（解决热点数据）

分库分库 如何设计一个不扩容方案。

可视化的黑客小工具

调式bug比较有用

测试排查问题（查看最新0代码）

系统比较庞大

不需要重启

Admin admin

会员部门 xxx部门

非常非常简单 原理

