# redhat7系统mysql8.0 yum安装 #
## [安装步骤] ##
1. 获取下载链接  
![mysql_rpm_url](https://github.com/chundonghan/learngit/blob/master/mysql_rpm_url.png)  
2. 下载rpm文件并添加到本地
	> - `wget https://dev.mysql.com/get/mysql80-community-release-el7-2.noarch.rpm`
	> - `yum localinstall mysql80-community-release-el7-2.noarch.rpm`  

3. 查看添加成功与否  
	> `yum search mysql`
4. 安装与启动
	> `yum install mysql-community-server`  #安装  
	> `service mysqld start`  #启动  
	> `service mysqld status`  #查看状态  
## [修改密码] ##
1. 启动mysql之后,查看默认密码并登录
	> `cat /var/log/mysqld.log | grep password`  #查看密码  
	> `mysql -u root -p`  输入密码  #登录  

2. 修改密码
	> `set global validate_password.policy=0;`  #使设置密码简单些  
	> `ALTER USER "root"@"localhost" IDENTIFIED  BY "1qazSE$";` #修改新密码为`1qazSE$`  
	> `exit`  #退出  
	> mysql -u root -p    然后输入密码即可登录

## [授权远程访问] ##
1. 重启mysql
2. 选择数据库  `use mysql`
3. 查看规则,修改规则
	> `select host,user from user \G;`	
	>`update user set host= '%' where user = 'root'; `
4. 修改加密方式
	> `update user set plugin='mysql_native_password' where user ='root';`
5. 刷新权限
	> `flush privileges;`
6. 如果外网连接失败的话，先按照[重置密码]执行一遍，再执行[修改密码].  
	注意:localhost->%
## [重置密码] ##
1. 开启免密码登陆 修改my.cnf文件   默认在/etc/my.cnf  
	`vim /etc/my.cnf`         在【mysqld】模块下面添加：`skip-grant-tables` 保存退出。
2. 重启服务，使配置生效 。    
	`service mysqld restart`
3. 登录   
	`mysql -u root -p`   #不输入密码直接敲回车键
4. 选择数据库  use mysql      把密码置空(因为免密登陆时不能直接修改密码) 
	`update user set authentication_string = '' where user = 'root';`
5. 退出再登录  
	`quit`  
	`mysql -u root -p` #直接敲回车键，因为刚刚置空密码了  
6. 重复[修改密码]步骤修改密码
## [卸载mysql] ##
1. 停止mysql
	`service mysqld stop`
2. 卸载mysql组件
	1. 查看MySQL组件
		rpm -qa | grep -i mysql
	2. 删除  
		`rpm -ev mysql-community-server-8.0.12-1.el7.x86_64`  
		`rpm -ev mysql-community-client-8.0.12-1.el7.x86_64`  
		`rpm -e --noscripts mysql-community-libs-8.0.12-1.el7.x86_64`  
		`rpm -e --noscripts mysql-community-common-8.0.12-1.el7.x86_64`
3. 删除相关文件夹 
	1. 查看mysql相关文件夹
		find / -name mysql
	2. 删除 
		rm -rf /etc/mysql  
		...


	

