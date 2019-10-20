---
title: 'WorkFlow定义笔记.md'
date: 2019-04-03 14:31:47
---
# 工作流workfow定义
## 节点node定义
 - StartNode 起始节点
 - TaskNode 任务节点
 - ForkNode 分支节点
 - JoinNode 合并节点
 - EndNode 结束节点
## 控制方法定义
 - start 初始化workflow
 - next 添加任务
 - decision 判断逻辑
 - fork 分支
 - or 或
 - join 合并
 - end 结束workflow
## workflow 基本常识
 - fork后必须有唯一的join与其对应
 - fork和join应该提供多线程支持提高执行效率
 - ForkNode记录各分支Node
 - JoinNode记录被合并的各分支Node
 - fork()或decision()后不可以立刻跟join(),需要有至少一个or()