### tomcat性能优化
#### 执行器（线程池）
`<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
    maxThreads="800" minSpareThreads="100"  maxQueueSize="100"  prestartminSpareThreads="true"/>`  
参数说明：  
> maxThreads：最大线程数  
> minSpareThreads：最小活跃线程数  
> maxQueueSize：最大的等待队里数，超过则请求拒绝  
> prestartminSpareThreads：是否在启动时就生成minSpareThreads个线程 默认false  

`<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8" executor="tomcatThreadPool"/>`  
#### 禁用AJP连接器
`<!--
    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
-->`

#### JVM参数的优化
- Young 年轻区  
- Tenured 年老区  
- Perm 永久区  

Virtual区: 最大内存和初始内存的差值，就是Virtual区  
> -Xms：设置JVM初始内存大小(默认是物理内存的1/64)  
> -Xmx：设置JVM可以使用的最大内存(默认是物理内存的1/4，建议：物理内存80%)  
> -XX:PermSize: 为JVM启动时Perm(永久代)的内存大小  jdk1.8以上用-XX:MetaspaceSize代替  
> -XX:MaxPermSize: 为最大可占用的Perm内存大小 jdk1.8以上用-XX:MaxMetaspaceSize代替  
> -XX:NewSize  设置年轻代大小  
> -XX:MaxNewSize:设置最大的年轻代大小  
> -XX:NewRatio=4:设置年轻代（包括Eden和两个Survivor区）与终身代的比值（除去永久代）  
> -XX:+DisableExplicitGC 忽略手动调用GC  

windows:  
$CATALINA_HOME\bin\catalina.bat  
`set "JAVA_OPTS=%JAVA_OPTS% -Dfile.encoding=UTF-8 -server -Xms1024m -Xmx1536m -XX:NewSize=512m -XX:MaxNewSize=1024m -XX:MetaspaceSize=256m -XX:MaxMetaspaceSize=256m -XX:NewRatio=2 -XX:+DisableExplicitGC"`  
linux:  
$CATALINA_HOME/bin/catalina.sh  
`JAVA_OPTS="-Dfile.encoding=UTF-8 -server -Xms1024m -Xmx1536m -XX:NewSize=512m -XX:MaxNewSize=1024m -XX:MetaspaceSize=256m -XX:MaxMetaspaceSize=256m -XX:NewRatio=2 -XX:+DisableExplicitGC"`  

#### 查看JVM参数
jps -v


