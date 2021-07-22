# 1.索引到底是什么

索引是帮助MySQL高效获取数据的排好序的数据结构

![图片](https://uploader.shimo.im/f/KuZa1LbVBAxuD1fR.png!thumbnail?fileGuid=vx8JhkJcdXPQKHWG)

数据结构教学网站：[https://www.cs.usfca.edu/~galles/visualization/Algorithms.html](https://www.cs.usfca.edu/~galles/visualization/Algorithms.html?fileGuid=vx8JhkJcdXPQKHWG)

# 2.索引底层数据结构与算法

1. 二叉树
2. 红黑树
3. HASH
4. BTREE
## 2.1B-Tree

1. 度(Degree)-节点的数据存储个数
2. 叶节点具有相同的深度
3. 叶节点的指针为空
4. 节点中的数据key从左到右递增排列

![image-20210722230401904](https://gitee.com/liufeihua/images/raw/master/images/image-20210722230401904.png)

## 2.2B+Tree(B-Tree变种)

1. 非叶子节点不存储data，只存储key，可以增大度
2. 叶子节点不存储指针
3. 顺序访问指针，提高区间访问的性能

![image-20210722230433114](https://gitee.com/liufeihua/images/raw/master/images/image-20210722230433114.png)

## 2.3MyISAM索引文件和数据文件是分离的(非聚集)

![image-20210722230539701](https://gitee.com/liufeihua/images/raw/master/images/image-20210722230539701.png)

## 2.4InnoDB索引实现(聚集)

![image-20210722230726002](https://gitee.com/liufeihua/images/raw/master/images/image-20210722230726002.png)

# 3.索引最左前缀原理

![image-20210722230458268](https://gitee.com/liufeihua/images/raw/master/images/image-20210722230458268.png)



