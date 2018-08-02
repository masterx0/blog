# Java多线程基础详解

进程：每个进程都有独立的代码和数据空间，进程间切换会有较大开销

线程：同一进程中的线程共享代码和数据空间，但每个线程有独立的运行栈和程序计数器，线程见切换开销小

线程和进程一样具有五个阶段：创建、就绪、运行、阻塞、终止

实现Runnable接口比继承Thread类所具有的优势：

1.适合多个相同的程序代码的线程去处理同一个资源

2.可以避免单继承的限制

3.增加代码鲁棒性，代码和数据独立


![image | left](https://upload-images.jianshu.io/upload_images/12015617-e7696ed69e066956.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "")


