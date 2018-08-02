# Docker的使用和理解

Docker是一个具有强大功能的C/S结构的区别于VM的，可以建立独立进程的开源框架。主要使用包括images、repository和container三个概念。
images字面理解镜像可以类比成Java中的类，repository则相对于存放类的package，container相当于由类创建出的对象。

repository:
通过docker pull 将images从repository中拷贝下来
images:
docker images 显示本地的images
container:
docker create <image-id> 为指定的image添加可读写层构成container
docker start <image-id> 启动指定的container
以上两个操作可以合并成docker run 一个命令
docker exec <image-id> 进入指定的container内部以便执行shell命令
docker stop <image-id> 关闭指定的container
docker rm <image-id> 删除指定的container(删除前需要执行关闭命令)

实践中通过docker pull mysql和docker pull tomcat拷贝了mysql运行环境以及tomcat容器运行环境，
解决了本机部署服务繁琐的问题，同时由于docker的特性模拟多个tomcat发布服务较之以往使用虚拟机
模拟对机器内存等资源的占用要少得多，值得接下来更深入的研究。 

