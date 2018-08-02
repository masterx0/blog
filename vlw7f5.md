# Redis的使用和理解

1）Redis提供5种数据结构，每种数据结构都有多种内部编码实现

2）纯内存存储、IO多路复用和单线程架构是造就Redis高性能的三个因素

3）理解Redis单线程命令处理机制是开发和运维Redis的核心

4）批量操作（mget、mset、hmset等）能够有效提高命令执行效率

5）在使用keys、hgetall、smembers、zrange等时间复杂度较高的命令时需要考虑数据规模对于阻塞的影响

6）move、dump+restore、migrate是Redis发展过程中三种迁移键的方式，其中move已经过时，migrate

    以原子性的方式实现了dump+restore并且支持批量操作

7）scan命令可以解决keys命令可能带来的阻塞问题，同时Redis还提供了hscan、sscan、zscan渐进式遍历hash、set和zset

8）区分Jedis直连和连接池的区别，在生产环境中使用连接池

9）Jedis.close()在直连下是关闭连接，在连接池则是归还连接

10）客户端输入缓冲区不能配置，强制限制在1G之内，但是不会受到maxmemory限制

11）monitor命令禁止在生成环境中使用，在大并发情况下存在输出缓冲区暴涨的可能性

12）info clients帮助开发和运维人员找到客户端可能存在的问题
