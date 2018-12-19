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

#### 单机部署多个 tomcat

1. win

   ```
    # 修改环境变量

    CATALINA_HOME:tomcat1安裝目录
    CATALINA_BASE:tomcat1安裝目录
    TOMCAT_HOME:tomcat1安裝目录
    CATALINA_2_BASE:tomcat2安装目录
    CATALINA_2_HOME:tomcat2安装目录
    TOMCAT_2_HOME:tomcat2安装目录

   # 修改第二个tomcat server.xml

   <Server port="9005" shutdown="SHUTDOWN">
   <Connector port="9080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8" />
    <Connector port="9009" protocol="AJP/1.3" redirectPort="8443" />

    # 修改第二个tomcat  catalina.bat startup.bat

    CATALINA_BASE 替换 CATALINA_2_BASE
    CATALINA_HOME 替换 CATALINA_2_HOME

   ```

2. linux

   ```
    # 修改环境变量(vim /etc/pofile)

    CATALINA_HOME:tomcat1安裝目录
    CATALINA_BASE:tomcat1安裝目录
    TOMCAT_HOME:tomcat1安裝目录
    CATALINA_2_BASE:tomcat2安装目录
    CATALINA_2_HOME:tomcat2安装目录
    TOMCAT_2_HOME:tomcat2安装目录

   # 修改第二个tomcat server.xml

   <Server port="9005" shutdown="SHUTDOWN">
   <Connector port="9080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8" />
    <Connector port="9009" protocol="AJP/1.3" redirectPort="8443" />

    # 修改第二个tomcat的catalina.sh,添加:

    export CATALINA_BASE=$CATALINA_2_BASE
    export CATALINA_HOME=$CATALINA_2_HOME

   ```
