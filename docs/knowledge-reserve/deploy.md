# 项目部署

## 问题
### Docker 容器日期与宿主主机时间不一致解决
```
失败：
/etc/localtime:/etc/localtime:ro
成功：
- /etc/localtime:/etc/localtime
- /etc/timezone:/etc/timezone
```
!> 第一种仍然存在时区问题，第二种最终解决该问题。

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

### 已非 jar 包形式部署 Spring boot 项目挂载项目
- 前情提要：
    - 非 jar 形式通过脚本启动项目
      ```
        #!/bin/bash
        APP_ENTRY=${app.entry}
        
        DIR=$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )
        BASE=$DIR/.
        CP=$BASE/classes:$BASE/lib/*
        
        JAVA_OPTS="$JAVA_OPTS -server -Xms512m -Xmx1g"
        
        java $JAVA_OPTS -cp "$CP" $APP_ENTRY

      ```
- 解决：
    - 换成 jar 包执行:
      - 若是通过执行 jar 包启动项目然后挂载外部配置文件就需要提前单独准备好配置文件
      ```
        FROM java:8
        RUN mkdir -p /opt/erp
        VOLUME /tmp
        ADD bill.api-1.0.0.jar /opt/erp/app.jar
        COPY /classes /opt/erp/conf
        WORKDIR /opt/erp
        RUN bash -c 'touch /opt/erp/app.jar'
        ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/opt/erp/app.jar"]

      ```
    - 直接解压 zip 包然后直接执行脚本启动
      - CMD 可灵活调整，这里针对性选择删除 com 以及 static 等静态文件，执行解压操作仅会对不存在的进行补充。
      ```
        FROM java:8
        MAINTAINER zJiaC
        RUN mkdir -p /opt/erp
        ADD *.zip /opt/api.zip
        WORKDIR /opt/api
        CMD rm -rf /opt/api/classes/com && rm -rf /opt/api/classes/static/*.json && rm -rf /opt/api/classes/static/*.jsonp && rm -rf /opt/api/classes/static/*.html && rm -rf /opt/api/classes/static/classic && rm -rf /opt/api/classes/static/modern && unzip -n /opt/api.zip -d /opt/api && chmod +x /opt/api/run && ./run
      ```

## 收藏网址
- [Docker-从入门到实践](https://yeasy.gitbook.io/docker_practice/)
- [从 Docker 到 Kubernetes 进阶](https://www.qikqiak.com/k8s-book/)