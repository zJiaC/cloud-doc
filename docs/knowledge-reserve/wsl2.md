# Docker Desktop(WSL2) 修改镜像存储位置

## 问题
- 默认存储在C盘占空间

## 解决
- docker-desktop 是存放程序的，docker-desktop-data 是存放镜像的，这两个 wsl 子系统都是默认放在系统盘的。
- 导出wsl子系统镜像:
```
wsl --export docker-desktop docker-desktop.tar
wsl --export docker-desktop-data docker-desktop-data.tar
```
- 删除现有的 wsl 子系统：
```
wsl --unregister docker-desktop
wsl --unregister docker-desktop-data
```
- 重新创建 wsl 子系统：
```
wsl --import docker-desktop d:\your-install-path docker-desktop.tar
wsl --import docker-desktop-data d:\your-install-path docker-desktop-data.tar
```