# RedHat7 solr单机安装部署 #
#### 前提:jdk安装 ####
1. 下载并拷贝
	> `wget https://mirrors.tuna.tsinghua.edu.cn/apache/lucene/solr/8.0.0/solr-8.0.0.zip` #下载solr压缩包
	>  `cp solr-8.0.0.zip /usr/local` #拷贝至指定目录如/usr/local
2. 添加用户组及用户	
	> - `groupadd solr` #添加用户组
	> - `useradd -g solr solr` #添加用户至用户组
	> - `passwd solr` # 修改用户密码
3. 切换solr用户解压solr压缩包以及环境变量配置
	> `unzip solr-8.0.0.zip` #解压solr压缩包   
	> `vi ~/.bash_profile` #环境变量配置  
	> > `export SOLR_HOME=/usr/local/solr-8.0.0`  
	> > `export JAVA_HOME=/usr/local/jdk1.8.0_181`  
	> > `PATH=$PATH:$HOME/.local/bin:$HOME/bin:$SOLR_HOME/bin:$JAVA_HOME/bin`  
	> `source ~/.bash_profile` #环境变量生效  
4. 进入solr根目录,查找并拷贝solr.xml文件至solr根目录 
    > `cd solr-8.0.0` #进入solr根目录  
    > `find ./ -name solr.xml` #查找solr.xml文件列表  
    > `cp ./server/solr/solr.xml ./` #拷贝solr.xml文件至solr根目录  
5. 单机启动solr
    > `solr start` #默认端口8983
6. 创建Core
	> `mkdir new_core` #solr根目录下创建一个core文件夹  
	> `cp -R ./server/solr/configsets/_default/conf new_core/` #拷贝配置文件至新core文件夹中  
	> 登录页面`http://ip:8983/solr`,点击左侧`Core Admin`菜单,之后在右侧点击`Add Core`按钮,core名称以及目录与创建的core文件夹名称相对应  
7. dataimport连接mysql数据库
	> 1. 下载mysql驱动jar包 
	> 2. 将mysql驱动jar包拷贝到$SOLR_HOME/server/solr-webapp/webapp/WEB-INF/lib/中
	> 3. 配置连接信息  
	>  vi $SOLR_HOME/new_core/conf/solrconfig.xml  
	>  查找`<!-- SearchHandler`注释,在其下方添加如下配置内容  
		&lt;requestHandler name="/dataimport" class="solr.DataImportHandler"&gt;  
		&nbsp;&nbsp;&nbsp;&nbsp;&lt;lst name="defaults"&gt;  
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;str name="config"&gt;data-config.xml&lt;/str&gt;  
		&nbsp;&nbsp;&nbsp;&nbsp;&lt;/lst&gt;  
		&lt;/requestHandler&gt;  

	> 4. 创建data-config.xml  
	> 在new_core/conf目录下创建data-config.xml  
		> `<?xml version="1.0" encoding="UTF-8"?>
			<dataConfig>
				<dataSource
					type="JdbcDataSource"
					driver="com.mysql.cj.jdbc.Driver" 
					url="jdbc:mysql://localhost:3306/solr?serverTimezone=UTC"
					user="root"
					password="root" /> 
			    <document>
			        <entity name="geo" query="select * from geo_code">
			            <field column="id" name="id" />
			            <field column="address" name="address" />
			            <field column="filter" name="filter" />
			        </entity>
			    </document>
			</dataConfig>`  
	> 5. 导入solr库  
	> 把`<lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-dataimporthandler-\d.*\.jar" />`加在`<lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-cell-\d.*\.jar" />`前面  
	> 6. 添加field  
	> 在页面左侧core下拉选项中选中`new_core`,点击`Schema`菜单,右侧点击`Add Field`按钮,name与数据库实体中field的name相对应,field type根据实际情况选择  
	> 7. 导入mysql中数据  
	> 在选中`new_core`之后点击左侧`Dataimport`菜单,右侧页面根据实际情况勾选选项,选择一个实体如`geo`点击`Execute`执行导入索引  
	> 8. 查看导入的索引  
	> 点击左侧`Query`菜单,右侧点击`Execute Query`按钮执行默认查询,查询内容将以json的形式展示在右侧空白处  
