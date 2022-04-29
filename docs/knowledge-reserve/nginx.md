# Nginx

## 强制 HTTP 跳转 HTTPS

### [使用 Nginx 将 HTTP 重定向到 HTTPS](https://mincong.io/cn/redirect-traffic-to-https-nginx/)
- 配置重定向:
```
  server {
    listen       80;
    server_name  _;
    return       301 https://$host$request_uri;
  }
```

!> 这里的服务器名字(server name)是个通配符_：也就是无论 URL 中包含什么域名，当请求到达 Nginx 服务器以后，都会被重定向到 HTTPS。比如说，无论是 http://sever.info 还是 http://www.sever.info 都会被重定向到它们相应的 HTTPS 链接。


### [项目处于安全考虑，通过 nginx 强制 http 转换成 https，但遇到报 GET 不支持错误。](https://blog.csdn.net/qq_42760638/article/details/118515698)

```
	server {
        ***
        //返回301永久重定向，307也是临时重定向，但它可以保持原请求的方法，不做修改的访问新地址。
-        return       301 https://$host$request_uri;
+        return       307 https://$host$request_uri;
        ***
	}
```

### 3XX  重定向
  - 301 Moved Permanently : 永久性重定向
  - 302 Found : 临时性重定向
  - 303 See Other : 和- 302 有着相同的功能，但是 303 明确要求客户端应该采用 GET 方法获取资源。 注: 虽然 HTTP 协议规定 301、302 状态下重定向时不允许把 POST 方法改成 GET 方法，但是大多数浏览器都会在 301、302 和 303 状态下的重定向把 POST 方法改成 GET 方法。
  - 304 Not Modified : 如果请求报文首部包含一些条件，例如: If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since，如果不满足条件，则服务器会返回 304 状态码。
  - 307 Temporary Redirect : 临时重定向，与 302 的含义类似，但是 307 要求浏览器不会把重定向请求的 POST 方法改成 GET 方法。

!> [网络协议 - HTTP 协议详解](https://www.pdai.tech/md/develop/protocol/dev-protocol-http.html#url)    
