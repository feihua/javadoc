交易中心

1. 交易中心业务
2. 技术难点

营销

1. 营销中心业务
2. 技术难点
# 1.交易模块

![图片](https://uploader.shimo.im/f/oagEnfwyyUHPgpHe.png!thumbnail?fileGuid=TDrjqrjtcvRH3PKP)

![图片](https://uploader.shimo.im/f/zNt5laJl1vmZwyle.png!thumbnail?fileGuid=TDrjqrjtcvRH3PKP)

## 1.1t_order 订单表

|字段名|数据类型|是否主键|描述|
|:----|:----|:----|:----|
|id|int|是|订单编号。|
|account|String|    |账号|
|payType|varchar|    |付款方式|
|carry|varchar|    |运送方式|
|rebate|DECIMAL(9,2)|    |折扣|
|createdate|datetime|    |创建日期|
|remark|varchar|    |备注、支付宝的WIDsubject|
|status|String|    |init:未审核；pass:已审核；<br>send：已发货；sign：已签收；cancel:已取消；file:已归档；<br>finish：交易完成；|
|refundStatus|String|    |退款状态(直接借用了支付宝的退款状态)。<br>WAIT_SELLER_AGREE：等待卖家同意退款；<br>WAIT_BUYER_RETURN_GOODS：卖家同意退款，等待买家退货；<br>WAIT_SELLER_CONFIRM_GOODS：买家已退货，等待卖家收到退货；<br>REFUND_SUCCESS：卖家收到退货，退款成功，交易关闭|
|paystatus|String|    |n:未支付；<br>p:部分支付；<br>y:全部支付|
|lowStocks|String|    |n:库存不足；y:库存充足。默认n|
|amount|Double|    |订单总金额|
|amountExchangeScore|Int|    |订单总兑换积分|
|fee|Double|    |运费总金额|
|ptotal|Double|    |商品总金额|
|quantity|Int|    |商品总数量|
|updateAmount|String|    |n:没有修改过；y:修改过；默认:n|
|expressCode|String|    |配送方式编码|
|expressName|String|    |配送方式名称|
|expressNo|String|    |快递运单号|
|expressCompanyName|String|    |快递公司名称|
|confirmSendProductRemark|String|    |确认发货备注|
|otherRequirement|String|    |客户的附加要求|
|closedComment|String|    |此订单的所有订单项对应的商品都进行了评论，则此值为y，表示此订单的评论功能已经关闭，默认为null，在订单状态为已发货后，则用户可以对订单进行评价。|
|score|Int|    |订单获赠的积分|

## 1.2t_orderdetail订单明细表

|字段名|数据类型|是否主键|描述|
|:----|:----|:----|:----|
|ID|int|是|ID号|
|orderID|int|    |与t_order表的id字段关联|
|orderdetailID|String|    |订单项ID|
|productID|int|    |商品ID|
|giftID|String|    |商品赠品ID|
|productName|String|    |商品名称|
|price|money|    |价格|
|number|int|    |数量|
|total0|Double|    |总金额（数量*价格）|
|fee|Double|    |配送费|
|isComment|String|    |是否评价过。n:未评价,y:已评价；默认n|
|lowStocks|String|    |n:库存不足；y:库存充足。默认n|
|s|String|    |商品规格信息|

## 1.3t_discount折扣表

|字段名|数据类型|是否主键|描述|
|:----|:----|:----|:----|
|ID|int|是|ID号|
|discount|decimal(9,1)|    |折扣,比如9.5折|
|name|String|    |折扣宣传名称|

## 1.4t_orderpay 支付记录表

|字段名|数据类型|是否主键|描述|
|:----|:----|:----|:----|
|id|int|是|自增|
|orderid|String|    |订单ID|
|paystatus|String|    |支付状态。y:支付成功,n:支付失败|
|payamount|Double|    |支付金额|
|createtime|String|    |支付时间|
|paymethod|String|    |支付方式|
|confirmdate|String|    |确认日期|
|confirmuser|String|    |确认人|
|remark|String|    |备注|
|tradeNo|String|    |支付宝交易号，以后用来发货|

## 1.5t_ordership 订单配送表

|字段名|数据类型|是否主键|描述|
|:----|:----|:----|:----|
|id|int|是|自增|
|orderid|String|    |订单ID|
|shipname|String|    |收货人姓名|
|shipaddress|String|    |收货人详细地址|
|provinceCode|String|    |省份代码|
|province|String|    |省份|
|cityCode|String|    |城市代码|
|city|String|    |城市|
|areaCode|String|    |区域代码|
|area|String|    |区域|
|phone|String|    |手机|
|tel|String|    |座机|
|zip|String|    |邮编|
|sex|String|    |性别|
|remark|String|    |备注|
|    |    |    |    |

## 1.6t_orderlog订单日志表

|字段名|数据类型|允许为空|描述|
|:----|:----|:----|:----|
|id|int|    |自增|
|orderid|String|    |订单ID|
|account|String|    |操作人|
|createdate|date|    |记录时间，默认是当前时间|
|content|String|    |日志内容|
|accountType|String|    |w:会员;m:后台管理人员;p:第三方支付系统异步通知|

## 1.7t_express快递配送表

|参数|名称|类型|备注|
|:----|:----|:----|:----|
|id|自增长|int|唯一|
|code|快递编码|String|三个值可选：EXPRESS（快递）、POST（平邮）、EMS（EMS）|
|name|快递名称|String|    |
|fee|物流费用|Double|    |
|order1|排序|Int|    |


交易中

库存（超卖）、重复支付、唯一主键、秒杀（库存）

、购物车

库存超卖、秒杀：锁、队列 for update

重复支付：幂等性（前端通知、后端通知）（下单 通知多次 调用多次 ）Redis incr

唯一主键：雪花算法、redis、数据库特性、UUID等等（分库分表实战）

下单：RPC调用

|显示状态|订单状态|支付状态|发货状态|
|:----|:----|:----|:----|
|已付款|活动订单|已支付|未发货|
|已发货|活动订单|已支付|已发货|
|待自提|活动订单|已支付|自提点签收|
|已签收|活动订单|已支付|用户签收|
|已拒收|活动订单|已支付|用户拒收|
|配送成功|活动订单|已支付|配送成功|
|配送失败|活动订单|已支付|配送失败|
|交易成功|已完成|已支付|配送成功|
|交易失败|已完成|已支付|配送失败|
|取消中|取消中|已支付|未发货|
|已取消|订单取消|未发货||

消息中间件：异步、解耦

![图片](https://uploader.shimo.im/f/36gPFBQW435EmbAc.png!thumbnail?fileGuid=TDrjqrjtcvRH3PKP)



购物车-技术点

面试题：不未登入时，购物车redis key该怎么做？

Userid+goodsid+shopid

?goodsid+shopid 未登录商品 购物车

Redis 同一台电脑上 跟换游览器没有关系

![图片](https://uploader.shimo.im/f/Roeh0V9sve7um1P9.png!thumbnail?fileGuid=TDrjqrjtcvRH3PKP)



时序图

存储在redis+mysql  redis存储的（shopid+goodsid）

增加一个商品购物车 插入一次数据库。

Userid xxxx  id  pc ios rpc调用goodsid 商品服务

下单 购物车清空

购物车数据结构：B2C(跨店铺)

com.jiagouedu.web.action.front.orders.CartInfo

com.jiagouedu.web.action.front.orders. CartGroup(一个店铺catgroup)

>cartPkg(一个店铺下可能会产生多个包裹)

>List<Product>(商品明细)

2个技术点：

排序的问题 排序（<String key>put数据）重写排序算法

实时性的问题  10点 5% 10：1  10%  xxx奶粉 囤货 1千奶粉 羊毛党（抓）结算 查一次海关系统（下单）

拆单 业务上问题

2个iphone 长沙  仓库武汉（库存 1 个）（河南 1个 广州 1个）

Goodsid+shopid+userid pkg（包裹）

JD 充值 100 95 （9.5折）99.

总结：

![图片](https://uploader.shimo.im/f/dmOkpo37xq0GIiSS.png!thumbnail?fileGuid=TDrjqrjtcvRH3PKP)

师，上节课的 黑科技上传了麽 上传了

技术难点说了一大堆，解决方案一个没有

库存锁定、扣减

下单：

U  l  扣减

10 1   0

支付：

9 0   1

ERP系统

30分钟支付时间 锁定

# 2.营销

商品级别、订单级别、全站级别。

![图片](https://uploader.shimo.im/f/BUK0l2UIHP9iBdX2.png!thumbnail?fileGuid=TDrjqrjtcvRH3PKP)

技术实现？

营销活动存储（下节课 技术点）

数据的回滚 ，用户手上 订单

![图片](https://uploader.shimo.im/f/bL2CPXDmnsfNaYPD.png!thumbnail?fileGuid=TDrjqrjtcvRH3PKP)

![图片](https://uploader.shimo.im/f/foSGl7FlvJS1sa27.png!thumbnail?fileGuid=TDrjqrjtcvRH3PKP)





