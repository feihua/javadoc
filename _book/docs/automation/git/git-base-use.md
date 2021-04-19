**概要：**

1. GIT体系概述
2. GIT 核心命令使用
3. GIT 底层原理
4. 基于 gogs 搭建 WEB 管理服务
# **1.**GIT体系概述

---


**提问：**

1. 大家公司是用什么工具来管理代码版本？SVN、CVS、GIT
2. GIT 和 SVN 有什么区别呢？
## 1.1GIT 与 svn 主要区别：

1. 存储方式不一样
2. 使用方式不一样
3. 管理模式不一样
### **1**.1.1存储方式区别

GIT 把内容按元数据方式存储类似k/v 数据库，而 SVN 是按文件(新版 svn 已改成元数据存储)

### **1.1.2**使用方式区别

从本地把文件推送远程服务，SVN 只需要commint而 GIT 需要add、commint、push 三个步骤

SVN 基本使用过程

![图片](https://uploader.shimo.im/f/MhuXnzrDjwzLDSQ3.png!thumbnail?fileGuid=tQCdwGV9HkDqXwQX)

Git 基本使用过程

![图片](https://uploader.shimo.im/f/1dZcUlEq8bQKO7du.png!thumbnail?fileGuid=tQCdwGV9HkDqXwQX)

### **1.1.3****版本管理模式区别**

git 是一个分布式的版本管理系统，而要 SVN 是一个远程集中式的管理系统

集中式

![图片](https://uploader.shimo.im/f/B8QmxXD14ZypacMW.png!thumbnail?fileGuid=tQCdwGV9HkDqXwQX)

分布式

![图片](https://uploader.shimo.im/f/ouYCLnjcld44HW3Z.png!thumbnail?fileGuid=tQCdwGV9HkDqXwQX)


# **2.**GIT 核心命令使用

---


主要内容:

1. git 客户端安装配置
2. 整体认识 GIT 的基本使用
3. 分支管理
4. 标签管理
5. 远程仓库配置
## 2.1安装git客户端安装

```plain
官方客户端： httpsd://git-scm.com/downloads
其它客户端： https://tortoisegit.org/download/
```
## 2.2认识git的基本使用

1. git 项目创建与克隆
2. 文件提交与推送

完整模拟从项目添加到 push 过程

* 创建项目
* 初始化 git 仓库
* 提交文件
* 远程关联
* push 至远程仓库

**本地初始化 GIT 仓库:**

基于远程仓库克隆至本地

```plain
git clone <remote_url>
```
当前目录初始化为 git 本地仓库
```plain
git init  <directory>
```
基于 mvn 模板创建项目
```plain
mvn archetype:generate
```
本地添加

添加指定文件至暂存区

```plain
git add <fileName>
```
添加指定目录至暂存区
```plain
git add <directory>
```
添加所有
```plain
git add -A
```
将指定目录及子目录移除出暂存区
```plain
git rm --cached target -r
```
添加勿略配置文件 .gitignore
本地提交

提交至本地仓库

```plain
git commit file -m '提交评论'
```
#快捷提交至本地仓库
```plain
git commit -am '快添加与提交'
```
## **2.3**分支管理

查看当前分支

```plain
git branch [-avv]
```
基于当前分支新建分支
```plain
git branch <branch name>
```
基于提交新建分支
```plain
git branch <branch name> <commit id>
git branch -d {dev}
```
切换分支
```plain
git checkout <branch name>
```
合并分支
```plain
git merge <merge target>
```
解决冲突，如果因冲突导致自动合并失败，此时 status 为 mergeing 状态.
需要手动修改后重新提交（commit）

## **2.4**远程仓库管理

查看远程配置

```plain
git remote [-v]
```
添加远程地址
```plain
git remote add origin http:xxx.xxx
```
删除远程地址
```plain
git remote remove origin 
```
上传新分支至远程
```plain
git push --set-upstream origin master 
```
将本地分支与远程建立关联
```plain
git branch --track --set-upstream-to=origin/test test
```
## **2.5**tag 管理

查看当前

```plain
git tag
```
创建分支
```plain
git tag <tag name> <branch name>
```
删除分支
```plain
git tag -d <tag name>
```
## **2.6**日志管理

查看当前分支下所有提交日志

```plain
git log
```
查看当前分支下所有提交日志
```plain
git log {branch}
```
单行显示日志
```plain
git log --oneline
```
比较两个版本的区别
```plain
git log master..experiment
```
以图表的方式显示提交合并网络
```plain
git log --pretty=format:'%h %s' --graph
```
# **3.**git 底层原理

---


* GIT 存储对像
* GIT 树对像
* GIT 提交对像
* GIT 引用
## **3.1**GIT存储对像(hashMap)

Git 是一个内容寻址文件系统，其核心部分是一个简单的键值对数据库（key-value data store），你可以向数据库中插入任意内容，它会返回一个用于取回该值的 hash 键。

git 键值库中插入数据

```plain
echo 'luban is good man' | git hash-object -w --stdin
79362d07cf264f8078b489a47132afbc73f87b9a
```
基于键获取指定内容

```plain
git cat-file -p 79362d07cf264f8078b489a47132afbc73f87b9a
```
Git 基于该功能 把每个文件的版本中内容都保存在数据库中，当要进行版本回滚的时候就通过其中一个键将期取回并替换。
* 模拟演示 git 版写入与回滚过程

查找所有的 git 对像

```plain
find .git/objects/ -type f
```
写入版本 1
```plain
echo 'version1' > README.MF; git hash-object -w README.MF;
```
写入版本 2
```plain
echo 'version2' > README.MF; git hash-object -w README.MF;
```
写入版本 3
```plain
echo 'version3' > README.MF; git hash-object -w README.MF;
```
回滚指定版本
```plain
git cat-file -p c11e96db44f7f3bc4c608aa7d7cd9ba4ab25066e > README.MF
```
所以我们平常用的 git add 其实就是把修改之后的内容 插入到键值库中。当我们执行git add README.MF等同于执行了git hash-object -w README.MF把文件写到数据库中。
我们解决了存储的问题，但其只能存储内容同并没有存储文件名，如果要进行回滚 怎么知道哪个内容对应哪个文件呢？接下要讲的就是树对象，它解决了文件名存储的问题 。

## **3.2**GIT树对像

树对像解决了文件名的问题，它的目的将多个文件名组织在一起，其内包含多个文件名称与其对应的 Key 和其它树对像的用引用，可以理解成操作系统当中的文件夹，一个文件夹包含多个文件和多个其它文件夹。

![图片](https://uploader.shimo.im/f/jum4YgQ810wWzu9h.png!thumbnail?fileGuid=tQCdwGV9HkDqXwQX)

每一个分支当中都关联了一个树对像，他存储了当前分支下所有的文件名及对应的 key.

通过以下命令即可查看

查看分支树

```plain
git cat-file -p master^{tree} 
```
## **3.3git**提交对象

一次提交即为当前版本的一个快照，该快照就是通过提交对像保存，其存储的内容为：一个顶级树对象、上一次提交的对像啥希、提交者用户名及邮箱、提交时间戳、提交评论。

```plain
 git cat-file -p b2395925b5f1c12bf8cb9602f05fc8d580311836
tree 002adb8152f7cd49f400a0480ef2d4c09b060c07
parent 8be903f5e1046b851117a21cdc3c80bdcaf97570
author tommy <tommy@tuling.com> 1532959457 +0800
committer tommy <tommy@tuling.com> 1532959457 +0800
```
通过上面的知识，我们可以推测出从修改一个文件到提交的过程总共生成了三个对像：

一个内容对象 ==> 存储了文件内容

一个树对像 ==> 存储了文件名及内容对像的 key

一个提交对像 ==> 存储了树对像的 key 及提交评论。

* 演示文件提交过程
## 3.4GIT引用

当我们执行 git branch {branchName} 时创建了一个分支，其本质就是在 git 基于指定提交创建了一个引用文件，保存在 .git\refs\heads\ 下。

* 演示分支的创建

git branch dev

cat.git\refs\heads\dev

git 总共 有三种类型的引用：

1. 分支引用
2. 远程分支引用
3. 标签引用

查询比较两个版本

```plain
 git log master..experiment
```
版本提交历史网络
```plain
git log --pretty=format:'%h %s' --graph
```
查看分支树
```plain
git cat-file -p master^{tree}
```
# **4.基于 gogs 快速搭建企业私有 GIT 服务**

---


概要：

1. gogs 介绍与安装
2. gogs 基础配置
3. gogs 定时备份与恢复

gitlab ==> 功能多一些

## **4.1gogs 介绍安装**

Gogs 是一款开源的轻量级 Git web 服务，其特点是简单易用完档齐全、国际化做的相当不错。其主要功能如下:

1. 提供 Http 与 ssh 两种协议访问源码服务
2. 提供可 WEB 界面可查看修改源码代码
3. 提供较完善的权限管理功能、其中包括组织、团队、个人等仓库权限
4. 提供简单的项目 viki 功能
5. 提供工单管理与里程碑管理。

**下载安装**

官网：[https://gogs.io](https://gogs.io?fileGuid=tQCdwGV9HkDqXwQX)

下载：[https://gogs.io/docs/installation](https://gogs.io/docs/installation?fileGuid=tQCdwGV9HkDqXwQX)选择 linx amd64 下载安装

文档：[https://gogs.io/docs/installation/install_from_binary](https://gogs.io/docs/installation/install_from_binary?fileGuid=tQCdwGV9HkDqXwQX)

安装：

解压之后目录：

![图片](https://uploader.shimo.im/f/9VZv9MUTRy6P262d.png!thumbnail?fileGuid=tQCdwGV9HkDqXwQX)

运行：

#前台运行

./gogs web

#后台运行

$nohup ./gogs web &

默认端口：3000

初次访问 http://<host>:3000 会进到初始化页,进行引导配置。

可选择 mysql 或 sqlite 等数据。这里选的是 sqllite

*注：mysql 索引长度的问题没有安装成功,需要用 mysql5.7 以上版本*

## **4.2gogs 基础配置**

**邮件配置说明：**

邮件配置是用于注册时邮件确认，和找回密码时候的验证邮件发送。其配置分为两步：

第一：创建一个开通了 smtp 服务的邮箱帐号，一般用公司管理员邮箱。我这里用的是 QQ 邮箱。

第二：在{gogs_home/custom/conf/app.ini  文件中配置。

**QQ 邮箱开通 smtp 服务**

1、点击设置

![图片](https://uploader.shimo.im/f/q8N2B5rW5WLgNMr3.png!thumbnail?fileGuid=tQCdwGV9HkDqXwQX)

2、开启 smtp

![图片](https://uploader.shimo.im/f/sAkQMSnKelx1pHsU.png!thumbnail?fileGuid=tQCdwGV9HkDqXwQX)


**邮件设置**

设置文件：{gogs_home/custom/conf/app.ini

![图片](https://uploader.shimo.im/f/27VAmX9JjYGvnbus.png!thumbnail?fileGuid=tQCdwGV9HkDqXwQX)


ENABLED = true

HOST=smtp.qq.com:465

FROM=tuling<2877438881@qq.com>

USER=

PASSWD=


**ENABLED**=true 表示启用邮件服务

**host**为 smtp 服务器地址，（需要对应邮箱开通 smtp 服务 且必须为 ssl 的形式访问）

**from**发送人名称地址

**user**发送帐号

**passwd**开通 smtp 帐户时会有对应的授权码

**重启后可直接测试**

管理员登录==》控制面版==》应用配置管理==》邮件配置==》发送测试邮件

## **4.3gogs 定时备份与恢复**

备份与恢复：

#查看备份相关参数

./gogs backup -h

#默认备份,备份在当前目录

./gogs backup

#参数化备份  --target 输出目录 --database-only 只备份 db

./gogs backup --target=./backupes --database-only --exclude-repos

#恢复。执行该命令前要先删除 custom.bak

./gogs restore --from=gogs-backup-20180411062712.zip


#自动备份脚本

#!/bin/sh -e

gogs_home="/home/apps/svr/gogs/"

backup_dir="$gogs_home/backups"

cd `dirname $0`

# 执行备份命令

./gogs backup --target=$backup_dir

echo 'backup sucess'

day=7

#查找并删除 7 天前的备份

find $backup_dir -name '*.zip' -mtime +7 -type f |xargs rm -f;

echo 'delete expire back data!'

#添加定时任务 每天 4：00 执行备份

# 打开任务编辑器

crontab -e

# 输入如下命令 00 04 * * * 每天凌晨 4 点执行 do-backup.sh 并输出日志至 #backup.log

00 04 * * * /home/apps/svr/gogs/do-backup.sh >> /home/apps/svr/gogs/backup.log 2>&1

## **4、客户端公钥配置与添加**

**Git 配置**

#Git 安装完之后，需做最后一步配置。打开 git bash，分别执行以下两句命令

git config --global user.name “用户名”

git config --global user.email “邮箱”

#git 自动记住用户和密码操作

git config --global credential.helper store

**SSH 公钥创建**

1、打开 git bash

2、执行生成公钥和私钥的命令：ssh-keygen -t rsa 并按回车 3 下

3、执行查看公钥的命令：cat ~/.ssh/id_rsa.pub

4、拷贝 id_rsa.pub 内容至至服务~~/.ssh/authorized_keys 中

