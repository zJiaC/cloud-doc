# Docker总结


## 问题
### 容器日期与宿主主机时间不一致解决
```
失败：
/etc/localtime:/etc/localtime:ro
成功：
- /etc/localtime:/etc/localtime
- /etc/timezone:/etc/timezone
```
PS:第一种仍然存在时区问题，第二种最终解决该问题。

## 收藏网址
- [Docker-从入门到实践](https://yeasy.gitbook.io/docker_practice/)
- [从Docker到Kubernetes进阶](https://www.qikqiak.com/k8s-book/)