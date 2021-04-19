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

![图片](https://uploader.shimo.im/f/5lJe4S8OGFqbiZ7O.png!thumbnail?fileGuid=vx8JhkJcdXPQKHWG)

## 2.2B+Tree(B-Tree变种)

1. 非叶子节点不存储data，只存储key，可以增大度
2. 叶子节点不存储指针
3. 顺序访问指针，提高区间访问的性能

![图片](https://uploader.shimo.im/f/ctIyJ4Fj0aZu9dyF.png!thumbnail?fileGuid=vx8JhkJcdXPQKHWG)


# 3.索引最左前缀原理

![图片](https://uploader.shimo.im/f/NXtiNgD0p16dRWJ0.png!thumbnail?fileGuid=vx8JhkJcdXPQKHWG)



