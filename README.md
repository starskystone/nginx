# nginx   
## nginx安装    
### 源吗编译安装    

安装make：yum -y install autoconf automake make  
安装g++: yum -y install gcc gcc-c++   

>安装nginx依赖的库

yum -y install pcre pcre-devel     
yum -y install zlib zlib-devel  
yum  -y install openssl openssl-devel  

wget  http://nginx.org/download/nginx-1.9.15.tar.gz   
tar -zxvf nginx-1.9.0.tar.gz  
cd nginx-1.9.0  
./configure   --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module   

### 目录结构   
./nginx -c nginx.conf的文件。如果不指定，默认为NGINX_HOME/conf/nginx.conf    
./nginx -s stop  停止   
./nginx -s quit退出   
./nginx -s reload 重新加载nginx.conf    

### nginx模型概念：
Nginx会按需同时运行多个进程：
一个**主进程**(master)和几个**工作进程**(worker)，配置了缓存时还会有缓存加载器进程(cache loader)和缓存管理器进程(cache manager)等。
所有进程均是仅含有一个线程，并主要通过“共享内存”的机制实现进程间通信。
主进程以root用户身份运行，而worker、cache loader和cache manager均应以非特权用户身份（user配置项）运行。

主进程主要完成如下工作：
1. 读取并验正配置信息；
2. 创建、绑定及关闭套接字；
3. 启动、终止及维护worker进程的个数；
4. 无须中止服务而重新配置工作特性；
5. 重新打开日志文件；

worker进程主要完成的任务包括：
1. 接收、传入并处理来自客户端的连接；
2. 提供反向代理及过滤功能；
3. nginx任何能完成的其它任务；

### nginx.conf配置文件结构：
#user  nobody;  #主模块命令， 指定Nginx的worker进程运行用户以及用户组，默认由nobody账号运行。      
worker_processes  1;#指定Nginx要开启的进程数。      
worker_rlimit_nofile 100000;  #worker进程的最大打开文件数限制     
#error_log  logs/error.log;     
#error_log  logs/error.log  notice;     
#error_log  logs/error.log  info;       
#pid        logs/nginx.pid;     
events {    
  use epoll;    
  worker_connections  1024;   
}
user :主模块命令， 指定Nginx的worker进程运行用户以及用户组，默认由nobody账号运行。       
worker_processes: 指定Nginx要开启的进程数。   
error log:用来定义全局错设日志文件的路径和日志名称。   
日志输出级别有debug，info，notice，warn，error，crit 可供选择，其中debug输出日志最为详细，面crit（严重）输出日志最少。默认是error    
pid: 用来指定进程id的存储文件位置。   
event：设定nginx的工作模式及连接数上限，   
nginx支持的工作模式有select ,poll,kqueue,epoll,rtsig,/dev/poll。   
其中select和poll都是标准的工作模式，kqueue和epoll是高效的工作模式，**对于linux系统，epoll是首选**。   
worker_connection是设置nginx每个进程最大的连接数，默认是1024，所以nginx最大的连接数max_client=worker_processes * worker_connections。   
进程最大连接数受到系统最大打开文件数的限制，需要设置ulimit。   

### nginx对http服务器相关属性的设置
http {
    include       mime.types;               主模块命令，对配置文件所包含文件的设定，减少主配置文件的复杂度，相当于把部分设置放在别的地方，然后在包含进来，保持主配置文件的简洁   
    default_type  application/octet-stream; 默认文件类型，当文件类型未定义时候就使用这类设置的。    
     log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '   指定nginx日志的格式   
    #                  '$status $body_bytes_sent "$http_referer" '    
    #                  '"$http_user_agent" "$http_x_forwarded_for"';    

    #access_log  logs/access.log  main;
    sendfile        on;   开启高效文件传输模式（zero copy 方式），避免内核缓冲区数据和用户缓冲区数据之间的拷贝。
    #tcp_nopush     on;  开启TCP_NOPUSH套接字（sendfile开启时有用）

    #keepalive_timeout  0;   客户端连接超时时间
    keepalive_timeout  65;

    #gzip  on;             设置是否开启gzip模块

### 下面是server段虚拟主机的配置
server {    
        listen       80;   虚拟主机的服务端口    
        server_name  localhost;   用来指定ip或者域名，多个域名用逗号分开    
        #charset koi8-r;    
        location / {          
               #地址匹配设置，支持正则匹配，也支持条件匹配，这里是默认请求地址，用户可以location命令对nginx进行动态和静态网页过滤处理   
            root   html;                   虚拟主机的网页根目录   
            index  index.html index.htm;   默认访问首页文件   
        }   
        #error_page  404              /404.html;    
        # redirect server error pages to the static page /50x.html            
        error_page   500 502 503 504  /50x.html;    
        location = /50x.html {    
            root   html;    
        }         
}   
