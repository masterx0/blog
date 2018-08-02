# Nginx的使用和理解

# <a name="kd9zhk"></a>一、Nginx介绍

## <a name="gqg3gw"></a>1 概述

​	Nginx是一款轻量级的web服务器、反向代理服务器及电子邮件（IMAP/POP3）代理服务器，并在一个BSD-like写一下发行。有俄罗斯的程序设计师Igor Saysoev所开发，供俄罗斯大型的入口网站及搜索引擎Rambler使用。其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、新浪、网易、腾讯等。

## <a name="73m9kf"></a>2 负载均衡策略

* 使用硬件负责均衡策略实现，如时候用F5、A10等负载均衡器，价格昂贵，投资大。
* 使用软件进行负载均衡
    * 使用阿里云服务器负载均衡SLB
    * 使用我们今天所学习的Nginx+KeepAlived
    * 其他软件负载均衡，如LVS、haproxy等

## <a name="t64ogx"></a>3 Nginx的优点

* Nginx 可以在大多数 UnixLinux OS 上编译运行，并有 Windows 移植版。 Nginx 的1.4.0稳定版已经于2013年4月24日发布，一般情况下，对于新建站点，建议使用最新稳定版作为生产版本，已有站点的升级急迫性不高。Nginx 的源代码使用 2-clause BSD-like license。
* Nginx 是一个很强大的高性能Web和反向代理服务器，它具有很多非常优越的特性。
* 在连接高并发的情况下，Nginx是Apache服务器不错的替代品：Nginx在美国是做虚拟主机生意的老板们经常选择的软件平台之一。能够支持高达 50,000 个并发连接数的响应，感谢Nginx为我们选择了 epoll and kqueue作为开发模型。

# <a name="ehbtly"></a>二、Nginx环境搭建和使用

## <a name="d45ses"></a>1 环境搭建

（1）下载

```shell
[root@localhost /]# cd /usr/local/src/
[root@localhost src]# wget http://nginx.org/dowmload/nginx-1.6.2.tar.gz
```

（2）解压

```
[root@localhost src]# tar -zxvf nginx-1.6.2.tar.gz
[root@localhost src]# cd nginx-1.6.2
[root@localhost nginx-1.6.2]# cd src/
```

（3）编译和安装依赖的库文件：

```
[root@localhost src]# yum install gcc gcc-c++ make automake autoconf libtool pcre* zlib openssl openssl-devel
```

（4）进行configure配置，查看是否报错

```
[root@localhost src]# cd nginx-1.6.2
[root@localhost nginx-1.6.2]# ./configure --prefix=/usr/local/nginx
```

（5）编译安装Nginx

```
[root@localhost nginx-1.6.2]# make && make install
```

（6）启动Nginx

```
[root@localhost nginx-1.6.2]#cd /usr/local/nginx/
[root@localhost nginx]# ll
总用量 16
drwxr-xr-x 2 root root 4096 10月 24 14:15 conf
drwxr-xr-x 2 root root 4096 10月 24 14:15 html
drwxr-xr-x 2 root root 4096 10月 24 14:15 logs
drwxr-xr-x 2 root root 4096 10月 24 14:15 sbin
[root@localhost nginx]#
```

​	启动命令在sbin目录下

```Java
[root@localhost nginx]# /usr/local/nginx/sbin/nginx
```

（7）访问：[http://你的虚拟机IP:80](http://xn--IP-0p3cl35ek5e6u0a2kz:80) 即可看到欢迎页面

​	若启动失败，请查看80端口是否被占用 lsof -i :80

## <a name="7go9ny"></a>2 Nginx配置文件

参考文档：[http://blog.csdn.net/tjcyjd/article/details/50695922](http://blog.csdn.net/tjcyjd/article/details/50695922)

### <a name="2ggslo"></a>2.1Nginx配置文件：

```
[root@localhost conf]# cd /usr/local/nginx/conf
[root@localhost conf]# vi nginx.conf
```

​	Nginx虚拟主机配置：
```
server {
    listen       80;
    server_name  localhost;
    location / {      #相对于nginx.conf的上级目录
        root   html;   #指/usr/local/nginx/html
        index  index.html index.htm;  #指/usr/local/nginx/html/index.html
    }
}
```
​	（1）自定义：

```
server{
		listen 1234;
		server_name localhost;
		location / {
			root chen.com
			index index.html
		}		
	}
```

​	（2）完成以下操作：

```
[root@localhost conf]# cd ..
[root@localhost nginx]# mkdir chen.com
[root@localhost nginx]# cd chen.com/
[root@localhost chen.com]# vi index.html
```

​	内容为：

```
<html>
        <body>
                hello chen
        </body>
</html>
```

​	（3）重启ninix

```
[root@localhost nginx]# /usr/local/nginx/sbin/nginx -s reload
```

​	（4）浏览器访问：

location语法：表示uri方式定位，基础语法有三种：

location = pattern {} 精确匹配

location pattern {} 一般匹配

location ~ pattern {} 正则匹配

想请教见参考文档：《nginx配置文件说明.conf》

## <a name="gr1glp"></a>3 Nginx反向代理与负载均衡

​	Nginx反向代理proxy与负载均衡upstream

​	官方配置：[http://nginx.org/en/docs](http://nginx.org/en/docs)

### <a name="h0hrlm"></a>3.1 配置反向代理

​	proxy：proxy\_pass  url地址

​	注意：反向代理之后获取客户端ip地址为ngnix服务器地址，这里需要nginx进行forward，设置真实的ip地址。

```
#设置客户端真实ip地址 X-real-ip 为自己的命名   remote_addr浏览器所在ip地址
proxy_set_header X-real-ip $remote_addr
```

模拟实现步骤：

（1）准备一台Tomcat

[http://10.0.31.145:8080/](http://10.0.31.145:8080/)

​	在ROOT中新增一个test.jsp

```
[root@localhost ROOT]# pwd
/root/tools/apache-tomcat-7.0.73/webapps/ROOT
[root@localhost ROOT]# vi test.jsp
```

​	内容如下：

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Test Page</title>
</head>
<body>
145 Test1 Page!!!<br/>
remote ip :  <%=request.getHeader("X-real-ip") %>  <br/>
nginx server ip : <%=request.getRemoteAddr()%>
</body>
</html>
```

​	此时访问http://10.0.31.145:8080/test.jsp

​	其中，这里显示的nginx server ip 与我们的nginx服务器没有关系，是我们的本机电脑ip。

（2）配置反向代理tomcat服务器：拦截.jsp结尾的请求转向到tomcat

​	在nginx.conf中 server端口配置了88中新加入：（默认是80，我这里改成了88）-在你想要代理的端口好的server配置中，可以新增多个location标签
```
    location ~ \.jsp$ {
        proxy_set_header X-real-ip $remote_addr;
        proxy_pass http://10.0.31.145:8080;
    }
```
​	重启nginx服务器

```
[root@localhost conf]# /usr/local/nginx/sbin/nginx -s reload
```

​	浏览器中访问：[http://10.0.31.144:88/test.jsp，观察结果](http://10.0.31.144:88/test.jsp%EF%BC%8C%E8%A7%82%E5%AF%9F%E7%BB%93%E6%9E%9C)

​	至此，就实现了通过144的nginx代理145的tomcat。浏览器所在地址为211。

### <a name="snmrrh"></a>3.2  配置负载均衡

​	upstream : upstream

（1）在准备一台tomcat

​	[http://10.0.31.144:8080/test.jsp](http://10.0.31.144:8080/test.jsp)

（2）注释掉之前的反向代理配置！（如果不注释，我们的所有.jps请求就只能访问到145上了）。如果要配置多台反向代理，应该在上面的proxy\_pass [http://10.0.31.145:8080](http://10.0.31.145:8080);改成 下面upstream的名字 myapp。

​	 继续配置nginx.conf，在http之内，在server之外加入以下配置：

```shell
    http{
    	.....
    	....
    	##webapp 权重相同，有10个请求，5个走144，5个走145 
    	upstream myapp {
       		server 10.0.31.144:8080 weight=1 max_fails=2 fail_timeout=30s;
       		server 10.0.31.145:8080 weight=1 max_fails=2 fail_timeout=30s;
    	}
    	server{
     		.....
     		.....
     		#配置反向代理tomcat服务器：拦截.jsp结尾的请求转向到tomcat
        	location ~ \.jsp$ {
           		proxy_set_header X-real-ip $remote_addr;
           		proxy_pass http://myapp;
       		}
            .....
            .....
    	}
    	.......
    	....
    }
```

（3）再次访问http://10.0.31.144:88/test.jsp 进行验证我们发现，各有一半的请求访问了144和145​。

至此，反向代理和负载均衡的例子介绍完毕。

