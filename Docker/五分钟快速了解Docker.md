> 这些是我的笔记，都是记录的核心概念和使用方法

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/2055627634-5b335ff4100b6_articlex.png)

# 概述
使用docker搭建开发环境将近1年了，自我感觉docker的强大并非如此，不过没有机会将docker部署生产环境，有位架构师曾说过，最新的未必是最好的，架构要选择最佳的，请大家也慎重。

# 核心概念
## 镜像 Image
Docker镜像类似于虚拟机镜像。每个镜像包括了一个基本的操作系统环境

## 容器 Container
Docker容器类似于一个轻量级的沙箱，Docker利用容器来运行和隔离应用。可以把容器看做一个简易版的linux系统环境

## 仓库 Repository
Docker仓库类似于代码仓库，它是Dokcer集中存放镜像文件的场所

# 数据管理
> 生产环境中使用Docker的过程中，往往需要对数据进行持久化，或者需要在多个容器之间进行数据共享，这必然要涉及容器的数据管理操作

容器中管理数据主要有两种方式：
* 数据卷
* 数据卷容器


## 挂载一个主机目录作为数据卷
```
docker run -d -P --name web -v /localhost-path:/server-path NAME:TAG
```

# 端口映射

## 端口映射实现访问容器
## 从外部访问容器应用
> 要让外部访问这些应用时，可以通过-P或-p参数来指定端口映射。当使用-P标记时，docker会随机映射一个49000～49900的端口到内部容器开放的网络端口
```
docker run -d -P NAME:TAG
```
## 映射所有接口地址
```
docker run -d -p 5000:5000 NAME:TAG
```
> -p 可以标记多个端口
```
docker run -d -p 5000:5000 -p 3000:80 NAME:TAG
```
## 映射到指定地址的指定端口
```
docker run -d -p 127.0.0.1:5000:5000 NAME:TAG
```
## 映射到指定地址的任意端口
> 使用IP::ContainerPort 绑定localhost的任意端口到容器的5000端口，本地主机会自动分配一个端口
```
docker run -d -p 127.0.0.1::5000 NAME:TAG
```
还可以使用udp标记来指定udp端口
```
docker run -d -p 127.0.0.1:5000:5000/udp NAME:TAG
```
## 查看映射端口配置
```
docker port nostalgic_morse PORT
```

# 容器互联

## 自定义容器命名
> 使用--name标记可以为容器自定义命名
```
docker run -d -P --name web NAME:TAG
```
## 查看容器名字
```
docker inspect -f "{{ .Name }}" 容器id
```

## 容器互联
> 使用--link参数可以让容器之间安全的进行交互
```
docker run -d --name db NAME:TAG
docker run -d -P --name web --link db:db NAME:TAG
```

# 使用Docker镜像
## 下载镜像
```shell
docker pull NAME[:TAG]
```
## 创建容器
```shell
docker run -it NAME:TAG SERVICE
```

## 查看镜像
```shell
docker images
```
* REPOSITOPY 来自于哪个仓库
* TAG 镜像的标签信息
* IMAGE ID 镜像的ID
* CREATED 创建时间
* SIZE 镜像大小

## 给镜像打标签
```shell
docker tag NAME:TAG NEWNAME:TAG
```

## 查看镜像详细信息
```shell
docker inspect NAME:TAG
```

## 查看镜像历史
```shell
docker history NAME:TAG
```

## 搜索镜像
```shell
docker search NAME
```

## 删除镜像
```shell
docker rmi IMAGE
```

## 删除容器
```shell
docker rm 容器ID
```
## 创建镜像

## 基本已有镜像的容器创建
```
docker commit [OPTIONS] CONTAINER [REPOSITOPUY[:TAG]]

-a , --author="" // 作者信息
-c , --change=[] 
-m , --message="" // 提交消息
-p , --pause=true // 提交时暂停容器运行

docker commit -m "added a new file " -a "crazy" 58fe3bd5b3e6 test:0.1
```

## 基于本地模板导入
```
cat 本地包 | docker import - 导入的镜像名称
```

## 导出镜像
```
docker save -o 导出文件名 导出的镜像
```
## 载入镜像
```
docker load --input 本地镜像名称
// 或者
docker load < 本地镜像名称
```
## 查看镜像详细信息
```
docker inspect NAME:TAG
```

# Docker相关的快捷命令

## 停用全部运行中的容器:
```
docker stop $(docker ps -q)
```
## 删除全部容器
```
docker rm $(docker ps -aq)
```

## 一条命令实现停用并删除容器
```
docker stop $(docker ps -q) & docker rm $(docker ps -aq)
```
## 删除所有未打 tag 的镜像
```
docker rmi $(docker images -q | awk '/^<none>/ { print $3 }')
```
## 删除所有镜像
```
docker rmi $(docker images -q)
```

# 致谢
感谢看完我的这篇白话文，这是我学习docker的过程，都是一些关键的命令和概念，希望可以帮到你，有什么问题可以评论区讨论，互相学习