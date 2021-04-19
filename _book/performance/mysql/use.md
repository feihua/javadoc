# 安装MySQL
注意：一定要用root用户操作如下步骤；先卸载MySQL再安装

# 1.安装包准备

（1）查看MySQL是否安装

```shell
rpm -qa|grep mysql
```
（2）如果安装了MySQL，就先卸载
```shell
rpm -e --nodeps mysql-libs-5.1.73-7.el6.x86_64
```
（3）上传mysql-libs.zip到hadoop102的/opt/software目录，并解压文件到当前目录
```shell
unzip mysql-libs.zip
```
（4）进入到mysql-libs文件夹下
```shell
ll
-rw-r--r--. 1 root root 18509960 3月  26 2015 MySQL-client-5.6.24-1.el6.x86_64.rpm
-rw-r--r--. 1 root root  3575135 12月  1 2013 mysql-connector-java-5.1.27.tar.gz
-rw-r--r--. 1 root root 55782196 3月  26 2015 MySQL-server-5.6.24-1.el6.x86_64.rpm
```
# 2.安装MySQL服务器

（1）安装MySQL服务端

```shell
 rpm -ivh MySQL-server-5.6.24-1.el6.x86_64.rpm
```
（2）查看产生的随机密码
```shell
cat /root/.mysql_secret
OEXaQuS8IWkG19Xs
```
（3）查看MySQL状态
```shell
service mysql status
```
（4）启动MySQL
```shell
service mysql start
```
# 3.安装MySQL客户端

（1）安装MySQL客户端

```bash
rpm -ivh MySQL-client-5.6.24-1.el6.x86_64.rpm
```
（2）链接MySQL（密码替换成产生的随机密码）
```bash
 mysql -uroot -pOEXaQuS8IWkG19Xs
```
（3）修改密码
```bash
mysql>SET PASSWORD=PASSWORD('000000');
```
（4）退出MySQL
```bash
mysql>exit
```
# 4.MySQL中user表中主机配置

配置只要是root用户+密码，在任何主机上都能登录MySQL数据库。

（1）进入MySQL

```bash
mysql -uroot -p000000
```
（2）显示数据库
```sql
mysql>show databases;
```
（3）使用MySQL数据库
```sql
mysql>use mysql;
```
（4）展示MySQL数据库中的所有表
```sql
mysql>show tables;
```
（5）展示user表的结构
```sql
mysql>desc user;
```
（6）查询user表
```sql
mysql>select User, Host, Password from user;
```
（7）修改user表，把Host表内容修改为%
```sql
mysql>update user set host='%' where host='localhost';
```
（8）删除root用户的其他host
```sql
mysql>
delete from user where Host='hadoop102';
delete from user where Host='127.0.0.1';
delete from user where Host='::1';
```
（9）刷新
```sql
mysql>flush privileges;
```
（10）退出
```sql
mysql>quit;
```
# 5.开启bin-log

打开mysql 的配置文件my.ini(别说找不到哦)

在mysqld配置项下面加上log_bin=mysql_bin

```properties
[mysqld]
log_bin = mysql_bin
```
重启mysql

在次show variables like 'log_bin'

