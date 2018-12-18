#### 命令

nginx.exe -t 验证配置

nginx.exe -s reload

nginx.exe -s stop

#### 本地调试

需要改 host 文件 windows(C:\Windows\System32\drivers\etc\hosts)

格式：

```

ip  域名

```

#### 关于反向代理

反向代理（Reverse Proxy）方式是指以代理服务器来接受 internet 上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给 internet 上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

#### nginx 配置 被调用方支持跨域

```
server{
 listen 80;
 server_name "b.com";

 location /{
   proxy_pass http://localhost:8080;
   add_header Access-Control-Request-Method *;
   add_header Access-Control-Max-Age 3600;
   add_header Access-Control-Allow-Credentials true;
   add_header Access-Control-Allow-Origin $http_origin;
   add_header Access-Control-Allow-Headers $http_access_control_request_headers;

   if ($request_method = OPTIONS){
     return 200;
   }
 }
}
```
