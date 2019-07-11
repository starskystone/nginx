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

