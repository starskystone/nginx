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

###目录结构   
./nginx -c nginx.conf的文件。如果不指定，默认为NGINX_HOME/conf/nginx.conf    
./nginx -s stop  停止   
./nginx -s quit退出   
./nginx -s reload 重新加载nginx.conf    
