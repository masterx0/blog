# zookeeper的使用和理解

### 1.zookeeper集群中的角色:
　　» 领导者（leader），负责进行投票的发起和决议，更新系统状态
　　» 学习者（learner），包括跟随者（follower）和观察者（observer），follower用于接受客户端请求并想客户端返回结果，在选主过程中参与投票
　　» 观察者（observer）可以接受客户端连接，将写请求转发给leader，但observer不参加投票过程，只同步leader的状态，observer的目的是为了扩展系统，提高读取速度
　　» 客户端（client），请求发起方

- 每个Server在工作过程中有三种状态：
  - LOOKING：当前Server不知道leader是谁，正在搜寻
  - LEADING：当前Server即为选举出来的leader　　
  - FOLLOWING：leader已经选举出来，当前Server与之同步

### 2.Zookeeper 的读写机制

​	» Zookeeper是一个由多个server组成的集群
　　» 一个leader，多个follower
　　» 每个server保存一份数据副本
　　» 全局数据一致
　　» 分布式读写
　　» 更新请求转发，由leader实施
### 3.Zookeeper 的保证　
　　» 更新请求顺序进行，来自同一个client的更新请求按其发送顺序依次执行
　　» 数据更新原子性，一次数据更新要么成功，要么失败
　　» 全局唯一数据视图，client无论连接到哪个server，数据视图都是一致的
　　» 实时性，在一定事件范围内，client能读到最新数据
