## memcached安装 ##
> yum install libevent libevent-devel  
> tar -zxvf memcached-1.x.x.tar.gz  
> cd memcached-1.x.x  
> ./configure --prefix=/usr/local/memcached  
> make  
> make install
## memcached启动 ##
> /usr/local/memcached/bin/memcached -p 11211 -m 64m -d -u root  
## memcached关闭进程 ##
> kill -9 pid  


