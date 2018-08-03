# GitFlow

- 默认git分支分为production、development、feature、hotfix和release五个分支
- 通过git flow feature start branchName来启动例如feature分支的修改，在修改后通过git flow feature finish branchName结束修改，将修改合并到develop或者master分支，同时删除掉feature该分支
- 总结来说，gitflow并不是用来替代git指令的，而是将原本几行git指令完成的任务封装成一行指令，在日常迭代开发中简化操作