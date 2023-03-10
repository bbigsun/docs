##  简单学下 Docker 

> 为了方便新手小白学习 k8s，所以简单写个教程，仅介绍 k8s 入门所需要掌握的一些 Docker 知识。

?> 参考资料：https://www.runoob.com/docker/docker-tutorial.html

### 一、Docker 安装

- 操作系统为 CentOS 7.9。

1、配置 yum 镜像源【阿里云】

```bash
cat >/etc/yum.repos.d/docker.repo << EOF
[docker-ce-edge]
name=Docker CE Edge - \$basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/\$basearch/edge
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
EOF
```

2、安装 docker

```bash
yum -y install docker-ce
```

3、查看 docker 版本

```bash
docker --version
```

4、启动 docker

```bash
systemctl start docker
```

5、允许 docker 自启

```bash
systemctl enable docker
```

6、查看 docker 状态

```bash
systemctl status docker
```

7、配置 docker 镜像源【阿里云】

```bash
cat >> /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
}
EOF
```

8、重启 docker，使配置生效

```sh
systemctl restart docker
```

### 二、Docker 概述

- Docker 官网：[https://www.docker.com](https://www.docker.com/)

- Github Docker 源码：https://github.com/docker/docker-ce

#### Docker 简介

Docker 是一个开源的应用容器引擎，基于 Go 语言并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

Docker 从 17.03 版本之后分为 CE（Community Edition: 社区版） 和 EE（Enterprise Edition: 企业版），我们用社区版就可以了。

#### Docker 优点

- 快速一致的交付您的应用程序
- 响应式部署和扩展
- 在同一硬件上运行更多的工作负载

#### Docker 架构

Docker 包含三个基本概念：

- **镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
- **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
- **仓库（Repository）**：仓库可看成一个代码控制中心，用来保存镜像。

?> 一句话概括三者的关系，即：Docker 使用**仓库**存放**镜像**，使用**镜像**创建**容器**，使用**容器**运行**程序**。

### 三、Docker 使用

?> 参考资料：https://www.runoob.com/docker/docker-tutorial.html

#### Docker Hello World

```sh
docker run ubuntu /bin/echo "hello world"

# 查看docker全部命令
dokcer

# 查看docker具体命令用法
docker images --help
```

#### Docker image

```sh
# docker images  查看本地下载的镜像
[root@centos79 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu    latest              d13c942271d6        About an hour ago   72.8 MB

#  REPOSITORY：表示镜像的仓库源
#  TAG：镜像的标签
#  IMAGE ID：镜像ID
#  CREATED：镜像创建时间
#  SIZE：镜像大小


# docker search ubuntu 查找镜像
[root@centos79 ~]# docker search ubuntu
INDEX       NAME                                                                DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/ubuntu                                                    Ubuntu is a Debian-based Linux operating s...   13451     [OK]       
docker.io   docker.io/dorowu/ubuntu-desktop-lxde-vnc                            Docker image to provide HTML5 VNC interfac...   599                  [OK]
docker.io   docker.io/websphere-liberty                                         WebSphere Liberty multi-architecture image...   282       [OK] 

#  NAME: 镜像仓库源的名称
#  DESCRIPTION: 镜像的描述
#  OFFICIAL: 是否 docker 官方发布
#  stars: 类似 Github 里面的 star，表示点赞、喜欢的意思
#  AUTOMATED: 自动构建


# 获取指定版本镜像 repository:tag
docker pull ubuntu:15.10

# 删除镜像
docker rmi ubuntu:15.10
```

#### Docker Container

```sh
# docker run 启动容器【使用ubuntu:15.10镜像创建容器】【容器短暂存活】
docker run ubuntu:15.10 /bin/echo "hello world"
# 如果未提前下载镜像，运行镜像时，会自动下载【使用镜像创建容器】【容器一直存活】
docker run nginx
# 使用docker ps查看存活的容器

# 使用命令行模式进入容器
docker run -it ubuntu /bin/bash
## 参数说明
#  -i: 交互式操作。
#  -t: 终端。
#  ubuntu: ubuntu 镜像。
#  /bin/bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash。

#  exit 退出终端
root@2c20633e5576:/# exit

# 启动已经停止的容器
[root@centos79 ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                        PORTS               NAMES
2c20633e5576        ubuntu              "/bin/bash"              2 minutes ago       Exited (0) 3 seconds ago                          compassionate_kowalevski
# 根据容器ID启动容器
docker start 2c20633e5576
# docker ps 查看正在运行的容器

# 后台运行容器，并指定名字
docker run -itd --name ubuntu-test ubuntu /bin/bash 

[root@centos79 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
be23ba2e8f19        ubuntu              "/bin/bash"         3 seconds ago       Up 2 seconds                            ubuntu-test
2c20633e5576        ubuntu              "/bin/bash"         5 minutes ago       Up 2 minutes                            compassionate_kowalevski

# 停止容器
docker stop <容器 ID>

# 进入容器
# 在使用 -d 参数时，容器启动后会进入后台。此时想要进入容器，可以通过以下指令进入：
#   docker attach
#   docker exec：推荐大家使用 docker exec 命令，因为此退出容器终端，不会导致容器的停止。
docker attach <容器 ID>
docker exec -it <容器 ID> /bin/bash

# 导出容器
docker export <容器 ID> > ubuntu.tar
# 导入容器
cat docker/ubuntu.tar | docker import - test/ubuntu:v1

# 删除容器
docker rm -f <容器 ID>
```

#### Docker Repository

Docker 镜像仓库可以通过注册官方镜像仓库 Docker Hub 免费体验使用，由于是国外网站，速度较慢，国内云提供商也提供免费的镜像仓库，注册云账号即可使用。

- [官方镜像仓库](https://registry.hub.docker.com/)

- [阿里云镜像仓库](https://cr.console.aliyun.com) 

- [腾讯云镜像仓库]()

- [华为云镜像仓库]()


#### Docker Network

我们使用docker run创建Docker容器时，可以用 --net 选项指定容器的网络模式，Docker 有以下4种网络模式：

- bridge模式，使用--net=bridge指定，**默认**设置
- host模式，使用--net=host指定，容器内部网络空间共享宿主机空间，效果类似于直接在宿主机上启动一个进程，端口信息和宿主机共用
- container模式，使用--net=container:NAME_or_ID指定，指定容器与特定容器共享网络命名空间
- none模式，使用--net=none指定，网络模式为空，即保留网络命名空间，但是不做任何网络相关的配置（网卡、IP、路由等）

#### Docker Command

如果以下常用命令，仍不能满足你的日常需求，请查看 [Docker 命令大全](https://www.runoob.com/docker/docker-command-manual.html)。

```sh
# 容器生命周期管理
docker run --name mynginx -p 80:80 -d nginx:latest
docker start mynginx
docker stop mynginx
docker restart mynginx
docker kill mynginx
docker rm mynginx
docker pause mynginx
docker unpause mynginx
docker create --name mynginx -p 80:80 nginx:latest
docker exec -it mynginx /bin/bash

# 容器操作
docker ps
docker inspect nginx:latest
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mynginx
dokcer top mynginx
docker attach mynginx
docker events -f "images"="nginx:latest" --since="2021-10-01"
docker logs --since="2016-07-01" --tail=10 mynginx
docker wait
docker export -o nginx-`date +%Y%m%d`.tar 96f7f14e99ab
docker port mynginx

# 容器rootfs命令
docker commit -a "commit user" -m "commit info" 96f7f14e99ab mynginx:v1
docker cp /www/nginx 96f7f14e99ab:/www/
docker diff mynginx

# 镜像仓库
docker login -u docker_user -p docker_passwd
docker logout
docker pull nginx
docker push mynginx:v1
docker search nginx

# 本地镜像管理
docker images
docker rmi 96f7f14e99ab
docker tag nginx:latest mynginx:v1
docker build -f /path/to/Dockerfile -t mynginx:v1 .
docker history mynginx:v1
docker save -o mynginx_v1.tar mynginx:v1
docker load < mynginx_v1.tar.gz
docker import mynginx_v1.tar mynginx :v1

# Info|Version
docker info
docker version
docker -v
```

### 四、Dokcerfile

#### 常用指令

- FROM：构建镜像基于哪个镜像

- MAINTAINER：镜像维护者姓名或邮箱地址

- RUN：构建镜像时运行的指令

- CMD：运行容器时执行的shell环境

- VOLUME：指定容器挂载点到宿主机自动生成的目录或其他容器

- USER：为RUN、CMD、和 ENTRYPOINT 执行命令指定运行用户

- WORKDIR：为 RUN、CMD、ENTRYPOINT、COPY 和 ADD 设置工作目录，就是切换目录

- HEALTHCHECH：健康检查

- ARG：构建时指定的一些参数

- EXPOSE：声明容器的服务端口（仅仅是声明）

- ENV：设置容器环境变量

- ADD：拷贝文件或目录到容器中，如果是URL或压缩包便会自动下载或自动解压

- COPY：拷贝文件或目录到容器中，跟ADD类似，但不具备自动下载或解压的功能

- ENTRYPOINT：运行容器时执行的shell命令
