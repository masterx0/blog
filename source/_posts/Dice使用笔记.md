---
title: 'Dice使用笔记.md'
date: 2019-04-03 14:31:47
---
1. 将本地代码依照git-flow格式要求推送到dice的gittar仓库中
2. 在分析项目栏点击新建分支选择要打包的分支，点击提交后开始进入项目打包阶段，完成代码获取、打包类型识别、编译代码、制作 Docker 镜像几个步骤
3. 等待过程中可以点击项目动态查看部署状态

---
实现功能小结：
 - 源码自动编译打包，添加dockerfile生成docker image上传至平台的repository
 - 打包成功后，通过集群管理平台进行服务部署
 ![结构](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/124729/1546915237390-79885466-d6df-4d85-9268-6030016f4b2a.png)
  - 成功部署后界面如下图所示，实现了CI/CD基本要求
  ![Dice项目流水线](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/124729/1546917270389-316d16c2-1d64-4875-9955-66f7c9412984.png)


