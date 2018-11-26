#### Tomcat7.0 服务器（Apache 服务器）

1. 解压安装包
2. 添加环境变量，CATALINA_HOME ,变量值包的根目录
3. 启动服务器 bin 下 startup.dat 或者命令行运行 catalina.bat run
4. 测试首页 浏览器：http://localhost:8080/
5. shutdomn.dat 关闭 tomcat 服务器
6. /webapps 服务器发布的 web 的 app

#### 修改 tomcat(conf/server.xml) URIEncoding

```
 <Connector port="8080" protocol="HTTP/1.1"
            connectionTimeout="20000"
   redirectPort="8443" URIEncoding=”utf-8” />
```
