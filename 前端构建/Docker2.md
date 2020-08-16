# Docker

## docker 概念

### Docker 三个核心概念：

#### Image(镜像)、Container(容器)、Repository(仓库)

- **Image：** 有领“好人卡”倾向的广大程序猿一定对 **镜像** 的概念不会陌生。但和 windows 的那种 iso 镜像相比，Docker 中的镜像是分层的，可复用的，而非简单的一堆文件迭在一起（类似于一个压缩包的源码和一个 git 仓库的区别）。
- **Container：** 容器的存在离不开镜像的支持，他是镜像运行时的一个载体（类似于实例和类的关系）。依托 Docker 的虚拟化技术，给容器创建了独立的端口、进程、文件等“空间”，Container 就是一个与宿机隔离 “容器”。容器可宿主机之间可以进行 port、volumes、network 等的通信。
- **Repository：** Docker 的仓库和 git 的仓库比较相似，拥有仓库名、tag。在本地构建完镜像之后，即可通过仓库进行镜像的分发。

> 为什么使用docker？ 容器虚拟化 仅包含所需要的环境 高效轻量 敏捷灵活 自动化
>
> - docker 可以实现自动化部署到服务器 不需要安装node npm 等软件直接自动化部署 像虚拟机 

## 设置国内镜像

[阿里云镜像和配置](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

docker info 检测是否挂载镜像![下载.png](https://cdn.nlark.com/yuque/0/2020/png/1295555/1590992289888-4494e60a-0672-4493-b023-78ff37f9e12e.png?x-oss-process=image%2Fresize%2Cw_689)

## 开始

- docker start 启动docker
- docker stop 停止docker

### 镜像命令

#### docker images

> 查看本机镜像 -q只显示ID -a 显示所有镜像包含映象层

#### docker pull XXX

> 安装镜像

#### docker rmi XXX ...XXX

> 删除某个可为多个镜像 如果在容器运行则需要加 -f ;删除所有则为$(docker images -qa) 

#### docker  search XXX

> 查看指定的包 描述信息和下载量等

### 容器命令

#### docker  [OPTIONS] run XXX [COMMOND] 

> #### [OPTIONS]: (-it 交互模式打开终端) （-d 后台运行容器）
>
> #### （--name="xxx" 指定名称）(-P 随机端口映射) （-p 指定端口映射 如 -p 7777:8080）
>
>  退出 exit  容器不停止退出 ctrl +Q +P

#### docker ps

> 列出正在运行的容器 -a 列出历史加现在 -l为最近 -q只显示编号

#### docker stop/kill

> 停止容器或强制停止

#### docker rm XXX

> 删除已经停止的容器  ;删除所有则为$(docker ps -qa) 

- docker logs -f -t XXX 查看日志

### Commit

> 用于创建新的镜像

docker commit -m=“提交的描述信息” -a=“作者” 容器ID 要创建的目标镜像名:[标签名]

### 容器数据

docker容器数据卷和数据共享和持久化

#### 命令添加

```
docker run it -v /宿主机目录:/容器目录 镜像名
> 设置只读 :ro 只读即只能宿主机操作
docker run it -v /宿主机目录:/容器目录 镜像名 :ro
```

### dockerfile

```
//file
VOLUME["/dataVolumeContainer","/dataVolumeContainer2","/dataVolumeContainer3"]
//打包
docker build -f /目录 -t /名称
```

#### 是什么？

Dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本。

三部曲 =>编写dockerfile => build打包 => docker run