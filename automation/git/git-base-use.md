**概要：**

1. GIT体系概述
2. GIT 核心命令使用
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

![image-20210707104232607](https://gitee.com/liufeihua/images/raw/master/images/image-20210707104232607.png)

Git 基本使用过程

![image-20210707104313483](https://gitee.com/liufeihua/images/raw/master/images/image-20210707104313483.png)

### **1.1.3**版本管理模式区别

git 是一个分布式的版本管理系统，而要 SVN 是一个远程集中式的管理系统

集中式

![image-20210707104407048](https://gitee.com/liufeihua/images/raw/master/images/image-20210707104407048.png)

分布式

![image-20210707104420011](https://gitee.com/liufeihua/images/raw/master/images/image-20210707104420011.png)


# **2.**GIT 核心命令使用

---


主要内容:

1. git 客户端安装配置
2. 整体认识 GIT 的基本使用
3. 分支管理
4. 标签管理
5. 远程仓库配置



## 2.1安装git客户端安装

```powershell
官方客户端： httpsd://git-scm.com/downloads
其它客户端： https://tortoisegit.org/download/
```



## 2.2认识git的基本使用

```powershell
基于远程仓库克隆至本地
git clone <remote_url>

当前目录初始化为 git 本地仓库
git init  <directory>

基于 mvn 模板创建项目
mvn archetype:generate

本地添加
添加指定文件至暂存区
git add <fileName>

添加指定目录至暂存区
git add <directory>

添加所有
git add -A

将指定目录及子目录移除出暂存区
git rm --cached target -r

本地提交
提交至本地仓库
git commit file -m '提交评论'

快捷提交至本地仓库
git commit -am '快添加与提交'
```



## **2.3**分支管理

```powershell
查看当前分支
git branch [-avv]

基于当前分支新建分支
git branch <branch name>

基于提交新建分支
git branch <branch name> <commit id>
git branch -d {dev}

切换分支
git checkout <branch name>

合并分支
git merge <merge target>

解决冲突，如果因冲突导致自动合并失败，此时 status 为 mergeing 状态.
需要手动修改后重新提交（commit）
```



## **2.4**远程仓库管理

```powershell
查看远程配置
git remote [-v]

添加远程地址
git remote add origin http:xxx.xxx

删除远程地址
git remote remove origin 

上传新分支至远程
git push --set-upstream origin master 

将本地分支与远程建立关联
git branch --track --set-upstream-to=origin/test test
```



## **2.5**tag 管理

```powershell
查看当前
git tag

创建分支
git tag <tag name> <branch name>

删除分支
git tag -d <tag name>
```



## **2.6**日志管理

```powershell
查看当前分支下所有提交日志
git log

查看当前分支下所有提交日志
git log {branch}

单行显示日志
git log --oneline

比较两个版本的区别
git log master..experiment

以图表的方式显示提交合并网络
git log --pretty=format:'%h %s' --graph
```



