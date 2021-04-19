# 1.什么是线程

线程是进程的一个实体，是CPU调度和分派的基本单位，它是比进程更小的能独立运行的基本单位。线程自己基本上不拥有系统资源，只拥有一点在运行中必不可少的资源 (如程序计数器，一组寄存器和栈 )，但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源。

# 2.线程实现的三种方式

Runnable、 Thread、 Callable

总结

最后再来看看它们三个之间的总结。

实现

Runnable接口相比继承 Thread类有如下优势

1）可以避免由于 Java的单继承特性而带来的局限

2）增强程序的健壮性，代码能够被多个线程共享，代码与数据是独立的

3）线程池只能放入实现 Runable或 Callable类线程，不能直接放入继承 Thread的类

实现

Runnable接口和实现 Callable接口的区别

1）Runnable是自从 java1.1就有了，而 Callable是 1.5之后才加上去的

2）实现 Callable接口的任务线程能返回执行结果，而实现 Runnable接口的任务线程不能返回结果

3 Callable接口的 call()方法允许抛出异 常，而 Runnable接口的 run()方法的异常只能在内部消化，不能继

续上抛

4）加入线程池运行 Runnable使用 ExecutorService的 execute方法， Callable使用 submit方法

注： Callable接口支持返回执行结果，此时需要调用 FutureTask.get()方法实现，此方法会阻塞主线程直到获

取返回结果，当不调用此方法时，主线程不会阻塞

# 3.线程的生命周期&状态

![图片](https://uploader.shimo.im/f/y4NxM01ds514yj8Q.png!thumbnail?fileGuid=Wgg98VPwK3rJr6P6)

![图片](https://uploader.shimo.im/f/2vtheh7kuMcDsChD.png!thumbnail?fileGuid=Wgg98VPwK3rJr6P6)


# 4.线程的执行顺序

# 5.线程与线程池对比

# 6.线程池体系介绍

# 7.线程池源码分析

