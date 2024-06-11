## 参考网站



## 简介

Nginx 是一款轻量级的 Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，其特点是占有内存少，并发能力强。

特点 ：占有内存少，并发能力强

作用：正向代理，反向代理

负载均衡策略：内置策略和扩展策略。内置策略为轮询，加权轮询，Ip hash

## 安装

详细步骤略，见参考地址



下载地址

http://nginx.org/en/download.html 

启动方式 解压后执行 start nginx.exe 

访问localhost 验证 （默认端口80)

![image-20240611105422649](C:\Users\23215\AppData\Roaming\Typora\typora-user-images\image-20240611105422649.png)

linux 环境源码安装 (已设置虚拟机固定ip 192.168.137.200)

```
wget https://nginx.org/download/nginx-1.16.1.tar.gz
//解压
tar -xzf nginx-1.16.1.tar.gz
//进入资源文件
./configure
//编译
make
//安装
make install
//默认安装路径
cd  /usr/local/nginx
cd sbin
//启动
./nginx

或者 yum安装

```



![image-20240611170333611](C:\Users\23215\AppData\Roaming\Typora\typora-user-images\image-20240611170333611.png)



## 基础用法

### nginx目录

![image-20240611171926383](C:\Users\23215\AppData\Roaming\Typora\typora-user-images\image-20240611171926383.png)

conf:nginx所有配置文件目录

​    CGI(Common Gateway Interface)通用网关【接口】，主要解决的问题是从客户端发送一个请求和数据，服务端获取到请求和数据后可以调用调用CGI【程序】处理及相应结果给客户端的一种标准规范。

​	fastcgi.conf:fastcgi相关配置文件

​	fastcgi.conf.default:fastcgi.conf的备份文件

​	fastcgi_params:fastcgi的参数文件

​	fastcgi_params.default:fastcgi的参数备份文件

​	scgi_params:scgi的参数文件

​	scgi_params.default：scgi的参数备份文件

​    uwsgi_params:uwsgi的参数文件

​	uwsgi_params.default:uwsgi的参数备份文件

​	mime.types:记录的是HTTP协议中的Content-Type的值和文件后缀名的对应关系

​	mime.types.default:mime.types的备份文件

​	nginx.conf:这个是Nginx的核心配置文件，这个文件非常重要，也是我们即将要学习的重点

​	nginx.conf.default:nginx.conf的备份文件

​	koi-utf、koi-win、win-utf这三个文件都是与编码转换映射相关的配置文件，用来将一种编码转换成另一种编码

html:存放nginx自带的两个静态的html页面

​	50x.html:访问失败后的失败页面

​	index.html:成功访问的默认首页

logs:记录入门的文件，当nginx服务器启动后，这里面会有 access.log error.log 和nginx.pid三个文件出现。

sbin:是存放执行程序文件nginx

​	nginx是用来控制Nginx的启动和停止等相关的命令。





### 配置文件

nginx.conf为配置文件

修改后执行nginx -s reload 生效

### 关闭进程

`nginx -s stop`(快速停止nginx) 或 `nginx -s quit`(完整有序的停止nginx) （需要在安装包路径下）

或者taskkill /f /t /im nginx.exe

```
taskkill是用来终止进程的，
/f是强制终止 .
/t终止指定的进程和任何由此启动的子进程。
/im示指定的进程名称 .
```

常用命令

```
cd /usr/local/nginx/sbin/
./nginx  启动
./nginx -s stop  停止
./nginx -s quit  安全退出
./nginx -s reload  重新加载配置文件
ps aux|grep nginx  查看nginx进程
```



```
#全局配置
#用于配置与具体业务无关的参数 例如
#worker_processes 4;表示要起4个线程
worker_processes 4;
#指定指定Nginx Worker进程运行用户以及用户组，默认由nobody账号运行
user nobody nobody;
#定义全局错误日志文件的路径和日志级别
error_log logs/error.log notice;
#指定Nginx Worker进程PID文件存放路径
pid logs/nginx.pid;

#events配置块,用于配置影响Nginx服务与用户之间连接的属性，例如 worker_connections 配置每个线程的最大连接数
events{
# 指定工作模式，使用epoll模型
use epoll;
#指定每个进程允许的最大连接数
worker_connections 65536;
}

#HTTP服务器配置 可以嵌套多个server块，每个server块用于配置一个虚拟主机
http {

    sendfile on;#开启sendfile功能，提高文件传输效率
    keepalive_timeout 65;#设置客户端连接保持时间
    #server必须位于http内部，用于配置Nginx的一个主机；其中listen指定监听的端口号；
    #server_name指定主机名；location用于指定主机上的资源位置
	server {
		listen 82;
		server_name  localhost;#指定主机名或者域名


		location / {
			root /usr/local/nginx/html/;#指定根目录
		}
        error_page 500 502 503 504 /50x.html;#指定错误页面
        location = /50x.html {#指定错误页面的位置
            root html;
        }
	}
}
```



## 问题

1.windows环境下 nginx可以多次启动 导致 拿不到logs下的nginx.pid文件（该文件内容只有一个数字表示当前进程pid）  无法 nginx -s reload 和stop

  tasklist | findstr "nginx.exe"  找到nginx的进程查看是否启动多次

 taskkill /F /IM nginx.exe /T   终止所有nginx进程 

start nginx.exe 重启

2.虚拟机centos配置固定ip

[Hyper-V Centos7 网络设置 虚拟机固定IP - jadedoo - 博客园 (cnblogs.com)](https://www.cnblogs.com/jadedoo/p/9967111.html)



