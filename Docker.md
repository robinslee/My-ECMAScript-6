# 玩转Docker

![](_images/docker_logo.png)

## Docker简介
* 由<https://www.docker.com>主导开发，托管于[GitHub](https://github.com/docker/docker)
* Go语言编写
* Docker提供了一种打包、输送、运行任意应用的容器解决方案
* Docker可以让开发者打包他们的应用以及依赖包，到一个轻量级、可移植的容器中，然后发布到任何装有Docker的电脑上（windows或Linux）上
* 容器是完全使用沙箱机制，相互之间不会有任何接口（类似iPhone的app），更重要的是容器性能开销极低
* 适用于Linux平台，也支持Mac、Windows平台
* 简单易用，用于开发、测试、运维

### 容器(Docker)与虚拟机异同
![](_images/vm-container.png)

[官方解释](https://www.docker.com/what-container)
* 容器和虚拟机都有隔离、分配资源的优点，但是具体功能不同。因为容器虚拟化了操作系统而非硬件，容器容易移植并且高效
* 容器抽象了应用层面的打包的代码和依赖。不同容器可以运行在同一台机器，与其他容器共享OS内核，各自运行在隔离的用户进程。容器比虚拟机占用更少的空间（容器镜像一般只有十几MB大小），并且几乎可以立即启动
* 虚拟机是物理硬件的抽象，把一台服务器虚拟成多台服务器。Hypervisor（虚拟机管理系统）允许多个虚拟机运行在一台机器，每个虚拟机包含一个完整的操作系统拷贝，一个或多个应用，必要的程序和库，至少占用若干个GB的空间。虚拟机通常启动非常慢

[中文资料]()
* 都是虚拟化技术，Docker可以解决所有虚拟机的问题，如虚拟机耗费太多的资源
* 虚拟机擅长彻底隔离整个运行环境，Hypervisor为每个APP启动一个GuestOS（虚拟机），是对硬件资源虚拟化
* 容器用于隔离不同的应用，直接使用硬件资源，效率更高、消耗资源更少，例如微服务

> 可以对比云端数据隔离三种方案，独立数据库/同库不同Schema/同库同Schema以字段TenantID隔离（[SaaS多租户数据隔离方案](https://blog.csdn.net/yown/article/details/51288549)）

```
APP - ENV(LIBS) - DOCKER - OS - HARDWARE
WAR - TOMCAT + JVM - DOCKER - LINUX - PC
```

### 为什么使用Docker
* 更快速的交付和部署。开发时创建并配置好镜像（包含应用和各种依赖），然后这个镜像可以在任意安装Docker的机器运行。而且Docker容器很轻量很快，启动是即时的，大量的节约开发、测试、部署的时间
* 更轻松的迁移和扩展，几乎可以在任意平台之间迁移，不用关心底层系统和硬件
* 更高的资源利用率。一台主机上可以同时运行数千个Docker容器，容器除了运行其中的应用外，基本不消耗额外系统资源。比如一台PC上运行10个Docker容器和10个虚拟机
* Docker的想法是创建软件程序可移植的轻量级容器，让其在任何安装了Docker的机器上运行，而不关心底层操作系统，犹如集装箱不关心何种船舶

**Docker的目标是简化部署流程，提高生产力**

### Docker基本概念
* **镜像（Image）**，只读的模板。包含一个完整的OS环境，安装了应用或其他应用。镜像用来创建一个或多个Docker容器，或者直接从其他地方下载一个做好的镜像来使用
* **容器（Container）**，用来运行应用。他是从镜像解压出来的运行实例，可以被开始、停止、删除，每个容器都是相互隔离的，保证平台的安全。可以把容器看作一个简易版的Linux环境（包括root用户权限、进程空间，用户空间和网络空间等）和运行在其中的应用程序及其依赖。文件系统隔离技术使得Docker成为了一个非常有潜力的虚拟化技术，在容器中对文件进行修改、删除、创建
* **仓库（Registry）**，集中存放镜像文件的场所，类似于NPM或者GitHub的托管服务。一般不严格区分仓库和仓库注册服务器（Registry），仓库注册服务器往往存放着多个仓库，每个仓库又包含了多个镜像。仓库分为Public和Private两种，最大的公开库是Docker Hub，存放了数量庞大的镜像供用户下载，国内有阿里云、网易云等，访问相对快速。当然用户也可以在本地创建一个私有仓库。当用户创建了自己的景象，就可以使用`push`命令将它上传到公有云或者私有仓库，这样其他机器就可以使用这个镜像，只需从仓库`pull`下来就可以了。

## Windows下安装Docker

### 系统要求
* 64位操作系统
* Windows10 10586以上版本内置了Hyper-V，只需开启就可以直接安装Docker
* Windows10 10586以下版本需要下载安装[Docker Toolbox](https://docs.docker.com/toolbox/overview/#whats-in-the-box)（VirtualBox），再安装Docker

### 下载地址
* [Docker Stable](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe)
* [Docker Toolbox](https://download.docker.com/win/stable/DockerToolbox.exe)

## 简单使用Docker
* 打开CMD.exe
* 尝试如下命令
```
docker info # 查看Docker信息
docker version # 查看Docker版本

docker run hello-world # 先查看本地有没有，没有会执行 docker pull hello-world
```
* 一般将Docker切换到Linux环境下
* 可以使用国内Docker镜像以加快下载速度，通过`docker info`查看

## 操作镜像
* 获取镜像，[Docker Hub](https://hub.docker.com/explore/)
```
docker pull [OPTIONS] NAME[:VER]

docker pull trainig/webapp:latest
docker pull registry.xxx.com/yyy/IMAGE:1.0
```
* 列出本地镜像
```
docker image ls
docker images
-
REPOSITORY  TAG IMAGE_ID CREATED  SIZE
```
* 删除本地镜像
```
docker image rm NAME|IMAGE_ID
```

## Docker容器操作
* 查看容器
```
docker ps   # 查看启动的容器
docker ps -a    # 查看所有容器（启动和未启动）
```

* 新建并启动容器
```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

OPTIONS:
    -d: deamon方式运行（后台运行）
    -t: 让Docker分配一个伪终端（Shell），并绑定到容器的标准输入上
    -i: 让容器标准输入与伪终端保持连接，并始终打开（否则看不到回显）
    -v: 创建数据卷
    -p: 映射端口（本地端口:容器内端口）
    --name: 为容器命令
```
示例:
```
# 注意参数在前
# 启动镜像作为后台服务并映射端口
docker run -d -p 8888:5000 IMAGE_ID|IMAGE_NAME

# 同上，启动伪终端
docker run -idt -p 8888:5000 MAGE_ID|IMAGE_NAME /bin/bash
```

* 启动 | 重启 | 终止 | 删除容器
```
docker stop [OPTIONS] CONTAINER [CONTAINER...]
docker start [OPTIONS] CONTAINER [CONTAINER...]
docker restart [OPTIONS] CONTAINER [CONTAINER...]
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

* 进入容器（获取Shell）
```
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

docker exec -i -t CONTAINER /bin/bash
```

* 本地主机和容器间相互拷贝数据
```
docker cp SRC DEST

# 容器内文件 -> 本地主机
docker cp CONTAINER:/opt/www/app.js D:/www/
# 本地主机文件 -> 容器
docker cp D:/www/app.js CONTAINER:/opt/www/
```

* 数据卷，共享本地主机的目录（实时双向映射）
> 需要设置Settings -> Share Drivers，勾选盘符并重启Docker
```
docker run -v LOCAL_FOLDER:CONTAINER_FOLDER
docker run -v C:/www:/opt/www/ CONTAINER
```

* 一个例子
```
docker run -itd -p 80:80 -p 3306:3306 lamp --privileged=true -v C:/data/www:/user/www registry.xxx.com/yyy/lamp:v2 /usr/sbin/init

# 映射端口
# 启动一个伪终端，并获取管理员权限
# 数据卷映射
```

## 制作镜像并推送到线上仓库

1. 获取镜像，解压并运行为容器
2. 修改容器内容，成为新的容器
3. 压缩容器，成为新的镜像
4. 将镜像上传到仓库（Registry）

```
# 启动运行一个容器，并进入容器
docker run -i -t --name NEW-CONTAINER-NAME /bin/bash
# 修改 安装等等操作

# 转换容器为镜像（类似于Git中的commit，镜像提交到本地仓库）
docker commit -a "AUTHOR" -m "DESCRIPTION" CONTAINER_ID NAME:VER

# 登录到Docker仓库（DockerHub或其他第三方仓库）
docker login --username=USERNAME registry.xxx.com
# 输入密码

# 为镜像命名（打标签）
docker tag IMAGE_ID registry.xxx.com/yyy/NAME:VER

# 推送到线上仓库
docker push registry.xxx.com/yyy/NAME:VER
```

## 本地镜像导出和加载（离线操作）
* 导出镜像到本地
```
docke save -o DEST_FILE SRC_IMAGE

docker save -o D:/www/NAME-ver.tar IMAGE
```
* 加载镜像

> 可以用ssh工具将文件传输到其他机器

```
# 必须是压缩格式为tar的文件 "<"和"-i"等效
docker load < FILE.tar

docker load -i D:/www/NAME-ver.tar
docker images
```

## Dockerfile

> Docker的目标是帮助开发和运维人员