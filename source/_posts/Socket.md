---
title: Socket
date: 2020-07-01 12:03:12
tags:
---

# Socket

---

# I/O模型

- 阻塞式IO
   - 应用进程被阻塞，直到数据从内核缓冲区复制到应用进程缓冲区中才返回
- 非阻塞式IO
   - 应用进程执行系统调用之后，内核返回一个错误码。应用进程可以继续执行，但是需要不断的执行系统调用来获知 I/O 是否完成，这种方式称为轮询（polling）
- IO多路复用(事件驱动式IO)
   - 使用 select 或者 poll 等待数据，并且可以等待多个套接字中的任何一个变为可读。这一过程会被阻塞，当某一个套接字可读时返回，之后再使用 recvfrom 把数据从内核复制到进程中
- 信号驱动式IO
   - 应用进程使用 sigaction 系统调用，内核立即返回，应用进程可以继续执行，也就是说等待数据阶段应用进程是非阻塞的。内核在数据到达时向应用进程发送 SIGIO 信号，应用进程收到之后在信号处理程序中调用 recvfrom 将数据从内核复制到应用进程中
- 异步IO
   - 应用进程执行 aio_read 系统调用会立即返回，应用进程可以继续执行，不会被阻塞，内核会在所有操作完成之后向应用进程发送信号。异步 I/O 与信号驱动 I/O 的区别在于，异步 I/O 的信号是通知应用进程 I/O 完成，而信号驱动 I/O 的信号是通知应用进程可以开始 I/O
- 区别

![](https://camo.githubusercontent.com/d89aed2ba6c5390aad0626b013c288d8849c4f39/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f313439323932383130353739315f332e706e67#align=left&display=inline&height=427&margin=%5Bobject%20Object%5D&originHeight=427&originWidth=761&status=done&style=none&width=761)
# I/O复用

- select/poll/epoll 都是 I/O 多路复用的具体实现，select 出现的最早，之后是 poll，再是 epoll
   - select 的 timeout 参数精度为微秒，而 poll 和 epoll 为毫秒，因此 select 更加适用于实时性要求比较高的场景，比如核反应堆的控制。select 可移植性更好，几乎被所有主流平台所支持
   - poll 没有最大描述符数量的限制，如果平台支持并且对实时性要求不高，应该使用 poll 而不是 select
   - 只需要运行在 Linux 平台上，有大量的描述符需要同时轮询，并且这些连接最好是长连接时使用epoll
