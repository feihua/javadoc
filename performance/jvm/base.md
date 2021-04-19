# 1.JVM整体架构

JVM由三个主要的子系统构成

1. 类加载器子系统
2. 运行时数据区（内存结构）
3. 执行引擎

![图片](https://uploader.shimo.im/f/289le9lcxQVUw6w2.png!thumbnail?fileGuid=hkyHgY6GQvrGCv9y)


# 2.JVM类加载器

## 2.1类加载过程

![图片](https://uploader.shimo.im/f/z2LGWkx31toE7HAT.png!thumbnail?fileGuid=hkyHgY6GQvrGCv9y)

类加载：类加载器将class文件加载到虚拟机的内存

1. 加载：在硬盘上查找并通过IO读入字节码文件
2. 连接：执行校验、准备、解析（可选）步骤
3. 校验：校验字节码文件的正确性
4. 准备：给类的静态变量分配内存，并赋予默认值
5. 解析：类装载器装入类所引用的其他所有类
6. 初始化：对类的静态变量初始化为指定的值，执行静态代码块
## 2.2类加载器种类（4种）

1. 启动类加载器：负责加载JRE的核心类库，如jre目标下的rt.jar,charsets.jar等
2. 扩展类加载器：负责加载JRE扩展目录ext中JAR类包
3. 系统类加载器：负责加载ClassPath路径下的类包
4. 用户自定义加载器：负责加载用户自定义路径下的类包
## 2.3类加载机制

全盘负责委托机制：当一个ClassLoader加载一个类时，除非显示的使用另一个ClassLoader，该类所依赖和引用的类也由这个ClassLoader载入

双亲委派机制：指先委托父类加载器寻找目标类，在找不到的情况下在自己的路径中查找并载入目标类

![图片](https://uploader.shimo.im/f/EDfCwfXGXZGojpLj.png!thumbnail?fileGuid=hkyHgY6GQvrGCv9y)


# 3.JVM内存结构

![图片](https://uploader.shimo.im/f/Ry79L8wYRR9yH9aE.png!thumbnail?fileGuid=hkyHgY6GQvrGCv9y)

# 4.JVM执行引擎

执行引擎：读取运行时数据区的Java字节码并逐个执行



