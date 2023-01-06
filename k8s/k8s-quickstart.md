# Kubernetes

> 当你根据这篇文章将 k8s 完整学习一遍之后，恭喜你！你的 k8s 应该算是入门了，剩下的就要靠你自己去学了。

## 一、Kubernetes 概述和架构

### Kubernetes 简介

Kubernetes，首字母 K，尾字母 s，中间 8 个字母，简称 K8s。

### Kubernetes 功能

***自动装箱***

- 基于容器对应用运行环境的资源配置要求自动部署应用容器

***自我修复***

- 当容器失败时，会对容器进行重启

- 当所部署的 Node 节点有问题时，会对容器进行重新部署和重新调度

- 当容器未通过监控检查时，会关闭此容器直到容器正常运行时，才会对外提供服务

***水平扩展***

- 通过简单的命令、用户 UI 界面或基于 CPU 等资源使用情况，对应用容器进行规模扩大或规模剪裁

- 当我们有大量的请求来临时，我们可以增加副本数量，从而达到水平扩展的效果

***服务发现***

- 用户不需使用额外的服务发现机制，就能够基于 Kubernetes 自身能力实现服务发现和负载均衡

***滚动更新***

- 可以根据应用的变化，对应用容器运行的应用，进行一次性或批量式更新

***版本回退***

- 可以根据应用部署情况，对应用容器运行的应用，进行历史版本即时回退

***密钥和配置管理***

- 在不需要重新构建镜像的情况下，可以部署和更新密钥和应用配置，类似热部署。

***存储编排***

- 自动实现存储系统挂载及应用，特别对有状态应用实现数据持久化非常重要

- 存储系统可以来自于本地目录、网络存储 (NFS、Gluster、Ceph 等）、公共云存储服务

***批处理***

- 提供一次性任务，定时任务；满足批量数据处理和分析的场景

### Kubernetes 架构和组件

***Kuebrnetes 架构***

- Kubernetes 架构主要包含两部分：Master（主控节点）和 Work node（工作节点）。

<img src="./images/k8s架构1.png"  >

<img src="./images/k8s架构2.png"  >

<img src="images/k8s架构3.png"  >

***Kubernetes 组件***

- **Master**：主控节点
  - API Server：集群统一入口，以 restful 风格进行操作，同时交给 etcd 存储。
    - 提供认证、授权、访问控制、API 注册和发现等机制。
  - scheduler：节点的调度，选择 node 节点应用部署。
  - controller-manager：处理集群中常规后台任务，一个资源对应一个控制器。
  - etcd：存储系统，用于保存集群中的相关数据。

- **Worker node**：工作节点
  - Kubelet：master 派到 node 节点代表，管理本机容器。
    - 一个集群中每个节点上运行的代理，它保证容器都运行在 Pod 中。
    - 负责维护容器的生命周期，同时也负责 Volume(CSI) 和 网络 (CNI) 的管理。
  - kube-proxy：提供网络代理，负载均衡等操作。
- **Container Runtime**：容器运行环境
  - 容器运行环境是负责运行容器的软件。
  - Kubernetes 支持多个容器运行环境：Docker、containerd、cri-o、rktlet 以及任何实现 Kubernetes CRI （容器运行环境接口） 的软件。
- fluentd：是一个守护进程，它有助于提升集群层面日志。

### Kubernetes 核心概念

***Pod***

- Pod 是 K8s 中最小的单元
- 一组容器的集合
- 共享网络【一个 Pod 中的所有容器共享同一网络】
- 生命周期是短暂的（服务器重启后，就找不到了）

***Volume***

- 声明在 Pod 容器中可访问的文件目录
- 可以被挂载到 Pod 中一个或多个容器指定路径下
- 支持多种后端存储抽象【本地存储、分布式存储、云存储】

***Controller***

- 确保预期的 pod 副本数量【ReplicaSet】
- 无状态应用部署【Deployment】
  - 无状态就是指，不需要依赖于网络或者 ip

- 有状态应用部署【StatefulSet】
  - 有状态需要特定的条件

- 确保所有的 node 运行同一个 pod 【DaemonSet】

- 一次性任务和定时任务【Job 和 CronJob】

***Deployment***

- 定义一组 Pod 副本数目，版本等
- 通过控制器【Controller】维持 Pod 数目【自动回复失败的 Pod】
- 通过控制器以指定的策略控制版本【滚动升级、回滚等】

***Service***

- 定义一组 pod 的访问规则
- Pod 的负载均衡，提供一个或多个 Pod 的稳定访问地址
- 支持多种方式【ClusterIP、NodePort、LoadBalancer】

***Label***

- label：标签，用于对象资源查询，筛选

***Namespace***

- 命名空间，逻辑隔离
- 一个集群内部的逻辑隔离机制【鉴权、资源】
- 每个资源都属于一个 namespace
- 同一个 namespace 所有资源不能重复
- 不同 namespace 可以资源名重复

***API***

- 我们通过 Kubernetes 的 API 来操作整个集群
- 同时我们可以通过 kubectl 、ui、curl 最终发送 http + json/yaml 方式的请求给 API Server，然后控制整个 K8S 集群，K8S 中所有的资源对象都可以采用 yaml 或 json 格式的文件定义或描述

### Kubernetes 工作原理

***Kubernetes 工作图解***

---

<img src="./images/k8s原理1.png">

***Kubernetes 工作流程***

1. xxx
2. xxx
3. xxx

## 二、Kubernetes 集群部署

### 使用 kubeadm 部署

kubeadm 是官方社区推出的一个用于快速部署 kubernetes 集群的客户端工具。

这个工具能通过两条指令完成一个 kubernetes 集群的部署：

```sh
# 创建一个 Master 节点
kubeadm init

# 将一个 Worker node 节点加入到当前集群中
kubeadm join < Master 节点的 IP 和端口 >
```

####  *安装要求*

在开始之前，部署 Kubernetes 集群机器需要满足以下几个条件：

- 一台或多台机器，操作系统 CentOS7.9。
- 硬件配置：2GB 或更多 RAM，**2 个 CPU** 或更多 CPU，硬盘 20GB 或更多。
- 可以访问外网，需要拉取镜像，如果服务器不能上网，需要提前下载镜像并导入节点。
- 禁止 swap 分区。

#### *安装步骤*

- **准备虚拟机**：准备三台虚拟机，并安装操作系统 CentOS 7.9。
- **系统初始化**：对三个刚安装好的操作系统进行初始化操作。
- **安装 k8s 组件**：在三台虚拟机上安装 `docker` `kubelet` `kubeadm` `kubectl`。
- **kubeadm init**：使用 `kubeadm init`命令，创建一个 Master 节点。
- **kubeadm join** ：使用 `kubeadm join`命令，将一个 Worker node 节点加入到当前集群中。
- **集群联网测试**：配置 CNI 网络插件，拉取 nginx 进行网络测试。

#### *准备虚拟机*

如果不会创建虚拟机，可以查看 [如何创建虚拟机？](pre-linux.md)

| 主机名称   | 节点类型 | IP             | 配置      |
| ---------- | -------- | -------------- | --------- |
| k8smaster1 | master   | 192.168.60.151 | 2U 2G 20G |
| k8snode1   | node     | 192.168.60.152 | 2U 2G 20G |
| k8snode2   | node     | 192.168.60.153 | 2U 2G 20G |

```sh
# 根据规划设置主机名【k8smaster1 节点上操作】
hostnamectl set-hostname ks8master1
# 根据规划设置主机名【k8snode1 节点上操作】
hostnamectl set-hostname k8snode1
# 根据规划设置主机名【k8snode2 节点操作】
hostnamectl set-hostname k8snode2

# 在三台虚拟机上配置本地 hosts
cat >> /etc/hosts << EOF
192.168.60.151 k8smaster1.example.com
192.168.60.152 k8snode1.example.com
192.168.60.153 k8snode2.example.com
EOF
```

#### *系统初始化*

对三台虚拟机进行初始化操作：

```sh
# 关闭防火墙
systemctl stop firewalld
# 禁用防火墙
systemctl disable firewalld

# 关闭 selinux
# 临时关闭【立即生效】告警，不启用，Permissive，查看使用 getenforce 命令
setenforce 0  
# 永久关闭【重启生效】
sed -i 's/SELINUX=enforcing/\SELINUX=disabled/' /etc/selinux/config  

# 关闭 swap
# 临时关闭【立即生效】查看使用 free 命令
swapoff -a 
# 永久关闭【重启生效】
sed -ri 's/.*swap.*/#&/' /etc/fstab

# 将桥接的 IPv4 流量传递到 iptables 的链
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
# 使 k8s 配置生效
sysctl --system  

# 时间同步
yum install ntpdate -y
ntpdate time.windows.com
```

#### *安装 k8s 组件*

- **kubeadm**：k8s 集群部署客户端工具
- **kubelet**：k8s 集群核心组件
- **kubectl**：k8s 集群命令行工具
- **docker**：k8s 集群默认 CRI（容器运行时）

**1、安装 docker**

```sh
# 配置 docker 的 yum 源【阿里云】
cat >/etc/yum.repos.d/docker.repo<<EOF
[docker-ce-edge]
name=Docker CE Edge - \$basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/\$basearch/edge
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
EOF

# yum 方式安装 docker
yum -y install docker-ce
# 查看 docker 版本
docker --version

# 配置 docker 的镜像源【阿里云】
cat >> /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
}
EOF

# 运行 docker 自启
systemctl enable docker
# 启动 docker
systemctl start docker
# 查看 docker 状态
systemctl status docker
```

**2、安装 kubeadm，kubelet 和 kubectl**

```sh
# 配置 k8s 的 yum 源【阿里云】
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# 安装 kubelet、kubeadm、kubectl，同时指定版本 1.18.0
yum install -y kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
# 设置开机自启【这里暂时先不启动 kubelet】
systemctl enable kubelet
```

####  *kubeadm init*

在 `k8smaster1` 上执行 `kubeadm init` 命令：

```sh
kubeadm init --apiserver-advertise-address=192.168.60.151 --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.18.0 --service-cidr=10.96.0.0/12  --pod-network-cidr=10.244.0.0/16
```

参数说明：

- --apiserver-advertise-address：当前主机 IP
- --image-repository：指定镜像源仓库
- --kubernetes-version：指定安装版本
- --service-cidr：service 可用 IP 范围
- --pod-network-cidr：pod 可用 IP 范围

*由于默认拉取镜像地址 k8s.gcr.io 国内无法访问，这里指定阿里云镜像仓库地址，【执行上述命令会比较慢，因为后台其实已经在拉取镜像了】，我们 可以使用 docker images 命令查看已经拉取的镜像。*

部署成功后，系统提示运行以下命令使用 `kubectl`：

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

执行完成后，我们使用下面命令，查看我们正在运行的节点：

```sh
kubectl get nodes
```

#### *kubeadm join*

在 `k8snode1` 和 `k8snode2` 上，执行 `kubeadm join` 命令向集群中添加新节点：

*注意，以下的命令是在 `k8smaster1` 上执行 `kubeadm init` 命令后给出的，**需要复制自己机器上生成的***

```sh
kubeadm join 192.168.60.151:6443 --token 8j6ui9.gyr4i156u30y80xf \
    --discovery-token-ca-cert-hash sha256:eda1380256a62d8733f4bddf926f148e57cf9d1a3a58fb45dd6e80768af5a500
```

如果你未保存 join 命令或者 token 过期（默认 token 有效期为 24 小时）导致 node 节点无法加入集群。这时就需要在 ` k8smatser1` 上重新创建 token，命令如下：

```sh
kubeadm token create --print-join-command
```

当我们把两个 node 节点都加入集群后，在 `k8smaster1` 节点上执行下面命令查看 node 节点情况：

```sh
kubectl get nodes
```

#### *集群联网测试*

查看 node 节点状态后，发现 node 节点状态是 NotReady，因为此时集群还未联网，下面我们需要部署网络插件，来进行联网访问：

```sh
# 下载网络插件 flannel
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# 使用 yml 文件部署 flannel。执行命令后，需要耐心的等待一会儿...
kubectl apply -f kube-flannel.yml

# 查看 pods 状态。运行成功后的结果为 Ready 状态
kubectl get pods -n kube-system
```

【提示】如果上述操作完成后，还存在某个节点处于 NotReady 状态，可以在 Master 将该节点删除，重新加入集群。

```sh
### 将 k8snode1 删除后重新加入集群 ###
# 在 k8smaster1 节点上操作，删除 k8snode1 节点
kubectl delete node k8snode1

# 在 k8snode1 节点上操作，重置 k8snode1 节点
kubeadm reset
# 在 k8snode1 节点上操作，k8snode1 节点加入集群
kubeadm join 192.168.60.151:6443 --token 8j6ui9.gyr4i156u30y80xf     --discovery-token-ca-cert-hash sha256:eda1380256a62d8733f4bddf926f148e57cf9d1a3a58fb45dd6e80768af5a500
```

创建一个 nginx pod，测试网络是否联通：

```sh
# 使用 nginx 镜像创建一个 pod
kubectl create deployment nginx --image=nginx、

# 查看 pod 状态，如果出现 Running 状态的时候，表示已经成功运行了
kubectl get pod

# 下面我们就需要将端口暴露出去，让其它外界能够访问
kubectl expose deployment nginx --port=80 --type=NodePort

# 查看容器对外映射的本地端口，容器的 80 端口映射到了本地的 30529 端口
kubectl get pod,svc

# 通过 curl 命令测试网络
curl http://192.168.60.151:30529/
```

使用宿主机浏览器，访问如下地址，查看 nginx 是否成功启动：

```sh
http://192.168.60.151:30529/
```

#### *错误汇总*

**错误一**

在执行 Kubernetes init 方法的时候，出现这个问题

```
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR NumCPU]: the number of available CPUs 1 is less than the required 2
```

是因为 VMware 设置的核数为 1，而 K8S 需要的最低核数应该是 2，调整核数重启系统即可

**错误二**

我们在给 k8snode1 节点使用 kubernetes join 命令的时候，出现以下错误

```
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR Swap]: running with swap on is not supported. Please disable swap
```

错误原因是我们需要关闭 swap【可能是永久关闭 swap 时没有重启生效】

```sh
# 关闭 swap
# 临时关闭【立即生效】
swapoff -a 
# 永久关闭【重启生效】
sed -ri 's/.*swap.*/#&/' /etc/fstab
```

**错误三**

在给 k8snode1 节点使用 kubernetes join 命令的时候，出现以下错误

```
The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get http://localhost:10248/healthz: dial tcp [::1]:10248: connect: connection refused
```

解决方法，首先需要到 k8smaster1 节点，创建一个文件

```sh
# 创建文件夹
mkdir /etc/systemd/system/kubelet.service.d

# 创建文件
vim /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

# 添加如下内容
Environment="KUBELET_SYSTEM_PODS_ARGS=--pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true --fail-swap-on=false"

# 重置
kubeadm reset
```

接着删除刚刚创建的配置目录

```sh
rm -rf $HOME/.kube
```

然后在 k8smaster1 重新初始化

```sh
kubeadm init --apiserver-advertise-address=92.168.60.151:6443 --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.18.0 --service-cidr=10.96.0.0/12  --pod-network-cidr=10.244.0.0/16
```

初始完成后，我们再到 k8snode1 节点，执行 kubeadm join 命令，加入到 k8smaster1【下面这条命令是 k8smaster1 初始化后自动生成的】

```sh
kubeadm join 192.168.60.151:6443 --token c7a7ou.z00fzlb01d76r37s \
    --discovery-token-ca-cert-hash sha256:9c3f3cc3f726c6ff8bdff14e46b1a856e3b8a4cbbe30cab185f6c5ee453aeea5
```

添加完成后，我们使用下面命令，查看节点是否成功添加

```sh
kubectl get nodes
```

**错误四**

我们再执行查看节点的时候， kubectl get nodes 会出现问题

```sh
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "kubernetes")
```

这是因为我们之前创建的配置文件还存在，也就是这些配置

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

我们需要做的就是把配置文件删除，然后重新执行一下

```sh
rm -rf $HOME/.kube
```

然后再次创建一下即可

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

这个问题主要是因为我们在执行 kubeadm reset 的时候，没有把 $HOME/.kube 给移除掉，再次创建时就会出现问题了

**错误五**

安装的时候，出现以下错误

```sh
Another app is currently holding the yum lock; waiting for it to exit...
```

是因为 yum 上锁占用，解决方法

```sh
yum -y install docker-ce
```

**错误六**

在使用下面命令，添加 k8snode1 节点到集群上的时候

```sh
kubeadm join 192.168.60.151:6443 --token jkcz0t.3c40t0bqqz5g8wsb  --discovery-token-ca-cert-hash sha256:bc494eeab6b7bac64c0861da16084504626e5a95ba7ede7b9c2dc7571ca4c9e5
```

然后出现了这个错误

```sh
[root@k8smaster1 ~]# kubeadm join 192.168.60.151:6443 --token jkcz0t.3c40t0bqqz5g8wsb     --discovery-token-ca-cert-hash sha256:bc494eeab6b7bac64c0861da16084504626e5a95ba7ede7b9c2dc7571ca4c9e5
W1117 06:55:11.220907   11230 join.go:346] [preflight] WARNING: JoinControlPane.controlPlane settings will be ignored when control-plane flag is not set.
[preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR FileContent--proc-sys-net-ipv4-ip_forward]: /proc/sys/net/ipv4/ip_forward contents are not set to 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher
```

出于安全考虑，Linux 系统**默认是禁止数据包转发**的。所谓**转发即当主机拥有多于一块的网卡时，其中一块收到数据包，根据数据包的目的 ip 地址将包发往本机另一网卡，该网卡根据路由表继续发送数据包**。这通常就是路由器所要实现的功能。也就是说 **/proc/sys/net/ipv4/ip_forward** 文件的值不支持转发

- 0：禁止
- 1：转发

所以我们需要将值修改成 1 即可

```sh
echo “1” > /proc/sys/net/ipv4/ip_forward
```

修改完成后，重新执行命令即可

### 使用源码部署

参考资料：https://blog.csdn.net/qq_40942490/article/details/114022294

> 源码部署这里，正在重构，写的不是很清楚。建议理解为主。

####  *安装要求*

在开始之前，部署 Kubernetes 集群机器需要满足以下几个条件：

- 一台或多台机器，操作系统 CentOS7.9。
- 硬件配置：2GB 或更多 RAM，**2 个 CPU** 或更多 CPU，硬盘 20GB 或更多.
- 可以访问外网，需要拉取镜像，如果服务器不能上网，需要提前下载镜像并导入节点。
- 禁止 swap 分区。

#### *安装步骤*

- **准备虚拟机**：准备三台虚拟机，并安装操作系统 CentOS 7.9。
- **系统初始化**：对三个刚安装好的操作系统进行初始化操作。
- **部署 etcd 集群**：在三台虚拟机上部署 `etcd` 集群。
- **安装 docker**：在三台虚拟机上安装 `docker`（容器进行时）。
- **部署 mastber 组件**：在 master 节点上安装 `kube-apiserver`、`kube-controller-manager`、`kube-scheduler` 。
- **部署 node 组件**：在 node 节点上安装 `kubelet`、`kube-proxy` 。
- **部署 CNI 网络插件**：使用 master 节点部署 CNI 网络插件，用于节点之间的网络连通。
- **测试 kubernetes 集群**：通过创建一个 nginx pod，测试 k8s 集群是否能正常工作。

#### *准备虚拟机*

如果不会创建虚拟机，可以查看 [如何创建虚拟机？](pre-linux.md)

| 主机名称   | 节点类型 | IP             | 配置      |
| ---------- | -------- | -------------- | --------- |
| k8smaster1 | master   | 192.168.60.151 | 2U 2G 20G |
| k8snode1   | node     | 192.168.60.152 | 2U 2G 20G |
| k8snode2   | node     | 192.168.60.153 | 2U 2G 20G |

```sh
# 根据规划设置主机名【k8smaster1 节点上操作】
hostnamectl set-hostname ks8master1
# 根据规划设置主机名【k8snode1 节点上操作】
hostnamectl set-hostname k8snode1
# 根据规划设置主机名【k8snode2 节点操作】
hostnamectl set-hostname k8snode2

# 在三台虚拟机上配置本地 hosts
cat >> /etc/hosts << EOF
192.168.60.151 k8smaster1.example.com
192.168.60.152 k8snode1.example.com
192.168.60.153 k8snode2.example.com
EOF
```

#### *系统初始化*

在每台机器上执行下面的命令：

```sh
# 关闭防火墙
systemctl stop firewalld
# 禁用防火墙
systemctl disable firewalld

# 关闭 selinux
# 临时关闭【立即生效】告警，不启用，Permissive，查看使用 getenforce 命令
setenforce 0  
# 永久关闭【重启生效】
sed -i 's/SELINUX=enforcing/\SELINUX=disabled/' /etc/selinux/config  

# 关闭 swap
# 临时关闭【立即生效】查看使用 free 命令
swapoff -a 
# 永久关闭【重启生效】
sed -ri 's/.*swap.*/#&/' /etc/fstab

# 将桥接的 IPv4 流量传递到 iptables 的链
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
# 使 k8s 配置生效
sysctl --system  

# 时间同步
yum install ntpdate -y
ntpdate time.windows.com
```

#### *部署 etcd 集群*

etcd 是一个分布式键值存储系统，Kubernetes 使用 etcd 进行数据存储，所以先准备一个 etcd 数据库，为了解决 Etcd 单点故障，采用集群方式部署，这里使用 3 台虚拟机组建集群，可容忍一台机器故障，当然也可以使用 5 台虚拟机组建集群，可以容忍 2 台机器故障。

**1、在 k8smaster1 上为 etcd 自签证书**

创建工作目录：

```sh
mkdir -p TLS/{etcd,k8s}
cd TLS/etcd/
```

准备 cfssl 证书生成工具：

```sh
wget https://pkg.cfssl.org/R1.2/cfssl_linux-amd64
wget https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
wget https://pkg.cfssl.org/R1.2/cfssl-certinfo_linux-amd64

chmod +x cfssl_linux-amd64 cfssljson_linux-amd64 cfssl-certinfo_linux-amd64

mv cfssl_linux-amd64 /usr/local/bin/cfssl
mv cfssljson_linux-amd64 /usr/local/bin/cfssljson
mv cfssl-certinfo_linux-amd64 /usr/local/bin/cfssl-certinfo
```

使用自签 CA 生成 etcd 证书：

```sh
### 创建自签 CA ###
cat > ca-config.json<<EOF
{
    "signing": {
        "default": {
            "expiry": "87600h"
        },
        "profiles": {
            "www": {
                "expiry": "87600h",
                "usages": [
                    "signing",
                    "key encipherment",
                    "server auth",
                    "client auth"
                ]
            }
        }
    }
}
EOF

cat > ca-csr.json<<EOF
{
    "CN": "etcd CA",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "Beijing",
            "BL": "Beijing"
        }
    ]
}
EOF

### 签发 etcd 证书 ###
cfssl gencert -initca ca-csr.json | cfssljson -bare ca - && ls *pem
```

使用自签 CA 签发 etcd HTTPS 证书：

> 创建证书申请文件：（文件 hosts 字段中 IP 为所有 etcd 节点的集群内部通信 IP，一个都不能少！为了 方便后期扩容可以多写几个预留的 IP）

```sh
### 创建自签 CA ###
cat > server-csr.json << EOF
{
    "CN": "etcd",
    "hosts": [
        "192.168.60.151",
        "192.168.60.152",
        "192.168.60.153"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "name": [
        {
            "C": "CN",
            "L": "Beijing",
            "SL": "Beijing"
        }
    ]
}
EOF

###  签发 etcd https 证书 ###
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=www server-csr.json | cfssljson -bare server && ls server*pem
```

**2、在 k8smaster1 上部署 etcd**

下载 etcd 二进制包：

```sh
wget https://github.com/etcd-io/etcd/releases/download/v3.4.9/etcd-v3.4.9-linux-amd64.tar.gz
```

安装 etcd：

```sh
mkdir -p /opt/etcd/{bin,cfg,ssl} 
tar -zxvf etcd-v3.4.9-linux-amd64.tar.gz
mv etcd-v3.4.9-linux-amd64/{etcd,etcdctl} /opt/etcd/bin/
cp ~/TLS/etcd/ca*pem ~/TLS/etcd/server*pem /opt/etcd/ssl/
```

创建配置文件：

```sh
##### k8smaster1 etcd-1 配置文件 #####
cat > /opt/etcd/cfg/etcd.conf << EOF
#[Member]
ETCD_NAME="etcd-1"
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="https://192.168.60.151:2380"
ETCD_LISTEN_CLIENT_URLS="https://192.168.60.151:2379"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://192.168.60.151:2380"
ETCD_ADVERTISE_CLIENT_URLS="https://192.168.60.151:2379"
ETCD_INITIAL_CLUSTER="etcd-1=https://192.168.60.151:2380,etcd-2=https://192.168.60.152:2380,etcd-3=https://192.168.60.153:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF

# 名词解释
# ETCD_NAME：节点名称，集群中唯一
# ETCD_DATA_DIR：数据目录
# ETCD_LISTEN_PEER_URLS：集群通信监听地址
# ETCD_LISTEN_CLIENT_URLS：客户端访问监听地址
# ETCD_INITIAL_ADVERTISE_PEER_URLS：集群通告地址
# ETCD_ADVERTISE_CLIENT_URLS：客户端通告地址
# ETCD_INITIAL_CLUSTER：集群节点地址
# ETCD_INITIAL_CLUSTER_TOKEN：集群 Token
# ETCD_INITIAL_CLUSTER_STATE：加入集群的当前状态，new 是新集群，existing 表示加入 已有集群
```

创建 etcd.service：

```sh
cat > /usr/lib/systemd/system/etcd.service << EOF
[Unit]
Description=Etcd Server
After=network.target
After=network-online.target
Wants=network-online.target
[Service]
Type=notify
EnvironmentFile=/opt/etcd/cfg/etcd.conf
ExecStart=/opt/etcd/bin/etcd \
--cert-file=/opt/etcd/ssl/server.pem \
--key-file=/opt/etcd/ssl/server-key.pem \
--peer-cert-file=/opt/etcd/ssl/server.pem \
--peer-key-file=/opt/etcd/ssl/server-key.pem \
--trusted-ca-file=/opt/etcd/ssl/ca.pem \
--peer-trusted-ca-file=/opt/etcd/ssl/ca.pem \
--logger=zap
Restart=on-failure
LimitNOFILE=65536
[Install]
WantedBy=multi-user.target
EOF
```

**3、在 k8smaster1 上转发 etcd 到 node 节点**

```sh
###### 转发到 k8snode1 ######
scp -r /opt/etcd/ root@192.168.60.152:/opt/
scp -r /usr/lib/systemd/system/etcd.service root@192.168.60.152:/usr/lib/systemd/system/
###### 转发到 k8snode2 ######
scp -r /opt/etcd/ root@192.168.60.153:/opt/
scp -r /usr/lib/systemd/system/etcd.service root@192.168.60.153:/usr/lib/systemd/system/
```

**4、修改 node 节点上 etcd 的配置文件：修改 IP 和名字**

```sh
##### k8sndoe1 etcd-2 配置文件 #####
cat > /opt/etcd/cfg/etcd.conf << EOF
#[Member]
ETCD_NAME="etcd-2"
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="https://192.168.60.152:2380"
ETCD_LISTEN_CLIENT_URLS="https://192.168.60.152:2379"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://192.168.60.152:2380"
ETCD_ADVERTISE_CLIENT_URLS="https://192.168.60.152:2379"
ETCD_INITIAL_CLUSTER="etcd-1=https://192.168.60.151:2380,etcd-2=https://192.168.60.152:2380,etcd-3=https://192.168.60.153:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF

##### k8sndoe2 etcd-3 配置文件 #####
cat > /opt/etcd/cfg/etcd.conf << EOF
#[Member]
ETCD_NAME="etcd-3"
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="https://192.168.60.153:2380"
ETCD_LISTEN_CLIENT_URLS="https://192.168.60.153:2379"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://192.168.60.153:2380"
ETCD_ADVERTISE_CLIENT_URLS="https://192.168.60.153:2379"
ETCD_INITIAL_CLUSTER="etcd-1=https://192.168.60.151:2380,etcd-2=https://192.168.60.152:2380,etcd-3=https://192.168.60.153:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF
```

**5、启动 etcd 并设置开机启动**

在三台虚拟机上依次执行以下命令，启动 etcd：

```sh
systemctl daemon-reload
systemctl start etcd
systemctl enable etcd
```

启动 etcd 后，在 k8smatser1 上查看 etcd 集群状态：

```sh
/opt/etcd/bin/etcdctl --cacert=/opt/etcd/ssl/ca.pem --cert=/opt/etcd/ssl/server.pem --key=/opt/etcd/ssl/server-key.pem --endpoints="https://192.168.60.151:2379,https://192.168.60.152:2379,https://192.168.60.153:2379" endpoint status --write-out=table
```

#### *安装 docker*

docker 是 k8s 集群的容器进行时，所有 k8s 节点均要安装。这里使用源码包安装 docker，如果为了节约时间，也可以使用 yum 包管理工具来安装 docker。

**1、在 k8smaster1 上安装 docker**

```sh
wget https://download.docker.com/linux/static/stable/x86_64/docker-20.10.3.tgz
tar -zxvf docker-20.10.3.tgz
mv docker /usr/bin
```

使用 systemd 管理 docker：

```sh
cat > /usr/lib/systemd/system/docker.service << EOF
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
[Service]
Type=notify
ExecStart=/usr/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
[Install]
WantedBy=multi-user.target
EOF
```

为 docker 镜像仓库配置阿里云加速：

```sh
mkdir /etc/docker
cat > /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
}
EOF
```

**2、在 k8smaster1 上转发 docker 到 node 节点**

```sh
##### 转发到 k8snode1 #####
scp -r /usr/bin/docker/ root@192.168.60.152:/usr/bin/
scp -r /usr/lib/systemd/system/docker.service root@192.168.60.152:/usr/lib/systemd/system/
scp -r /etc/docker/ root@192.168.60.152:/etc/
##### 转发到 k8snode2 #####
scp -r /usr/bin/docker/ root@192.168.60.153:/usr/bin/
scp -r /usr/lib/systemd/system/docker.service root@192.168.60.153:/usr/lib/systemd/system/
scp -r /etc/docker/ root@192.168.60.153:/etc/
```

**3、启动 docker 并设置开机自启**

在三台虚拟机上依次执行以下命令：

```sh
systemctl daemon-reload
systemctl start docker
systemctl enable docker
systemctl status docker
```

#### *部署 master 组件*

master 节点核心组件为以下 3 个，以下操作均在 k8smatser1 上进行：

- kube-apiserver
- kuber-controller-manager
- kube-scheduler

下载 k8s 组件二进制包（包含 master 组件和 node 组件）：

```sh
# 下载地址：https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.20.md
# kubernetes-server-linux-amd64.tar.gz 包含了 master 和 node 的所有组件
# 这里提供两个网速较快的下载地址
wget https://storage.googleapis.com/kubernetes-release/release/v1.20.1/kubernetes-server-linux-amd64.tar.gz
wget https://dl.k8s.io/v1.19.0/kubernetes-server-linux-amd64.tar.gz
```

使用二进制包部署 k8s 组件：

```sh
mkdir -p /opt/kubernetes/{bin,cfg,ssl,logs}
tar -zxvf kubernetes-server-linux-amd64.tar.gz
cd kubernetes/server/bin
# master 节点所需组件
cp kube-apiserver kube-scheduler kube-controller-manager /opt/kubernetes/bin
# node 节点所需组件
cp kubelet kube-proxy /opt/kubernetes/bin
# master 与 node 节点都需要的组件
cp kubectl /usr/bin/

# 使用 scp 命令，转发到 node 节点
##### 转发到 k8snode1 #####
scp -r /opt/kubernetes root@192.168.60.152:/opt
scp -r /usr/bin/kubectl root@192.168.60.152:/usr/bin
##### 转发到 k8snode2 #####
scp -r /opt/kubernetes root@192.168.60.153:/opt
scp -r /usr/bin/kubectl root@192.168.60.153:/usr/bin
```

接下来只需要，配置一下相关组件即可。

**1、配置 kube-apiserver**

```sh
cd ~/TLS/k8s
```

使用自签 CA 签发 kube-apiserver 证书：

```sh
### 创建自签 CA ###
cat > ca-config.json << EOF
{
  "signing": {
    "default": {
      "expiry": "87600h"
    },
    "profiles": {
      "kubernetes": {
         "expiry": "87600h",
         "usages": [
            "signing",
            "key encipherment",
            "server auth",
            "client auth"
        ]
      }
    }
  }
}
EOF

cat > ca-csr.json << EOF
{
    "CN": "kubernetes",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "Beijing",
            "ST": "Beijing",
            "O": "k8s",
            "OU": "System"
        }
    ]
}
EOF

###  签发 kube-apiserver 证书 ###
cfssl gencert -initca ca-csr.json | cfssljson -bare ca - && ls *pem
```

使用自签 CA 签发 kube-apiserver HTTPS 证书：

```sh
### 创建自签 CA ###
cat > server-csr.json << EOF
{
    "CN": "kubernetes",
    "hosts": [
      "10.0.0.1",
      "127.0.0.1",
      "192.168.60.151",
      "192.168.60.152",
      "192.168.60.153",
      "kubernetes",
      "kubernetes.default",
      "kubernetes.default.svc",
      "kubernetes.default.svc.cluster",
      "kubernetes.default.svc.cluster.local"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "BeiJing",
            "ST": "BeiJing",
            "O": "k8s",
            "OU": "System"
        }
    ]
}
EOF

### 签发 kube-apiserver https 证书 ###
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes server-csr.json | cfssljson -bare server && ls server*pem
```

把刚生成的证书拷贝到 /opt/kubernetes/ssl/ 下：

```sh
cp ~/TLS/k8s/ca*pem ~/TLS/k8s/server*pem /opt/kubernetes/ssl/

##### 转发到 k8snode1 #####
scp -r /opt/kubernetes/ssl root@192.168.60.152:/opt/kubernetes
##### 转发到 k8snode2 #####
scp -r /opt/kubernetes/ssl root@192.168.60.153:/opt/kubernetes
```

创建 token 文件：

```sh
cat > /opt/kubernetes/cfg/token.csv << EOF
c47ffb939f5ca36231d9e3121a252940,kubelet-bootstrap,10001,"system:node-bootstrapper"
EOF
```

>  格式：token，用户名，UID，用户组 token 也可自行生成替换【建议暂时不要替换，直接 copy 代码，熟练掌握后再自行创建】：

```sh
head -c 16 /dev/urandom | od -An -t x | tr -d ' '
```

生成 kube-apiserver 配置文件：

```sh
cat > /opt/kubernetes/cfg/kube-apiserver.conf << EOF
KUBE_APISERVER_OPTS="--logtostderr=false \\
--v=2 \\
--log-dir=/opt/kubernetes/logs \\
--etcd-servers=https://192.168.60.151:2379,https://192.168.60.152:2379,https://192.168.60.153:2379 \\
--bind-address=192.168.60.151 \\
--secure-port=6443 \\
--advertise-address=192.168.60.151 \\
--allow-privileged=true \\
--service-cluster-ip-range=10.0.0.0/24 \\
--enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,ResourceQuota,NodeRestriction \\
--authorization-mode=RBAC,Node \\
--enable-bootstrap-token-auth=true \\
--token-auth-file=/opt/kubernetes/cfg/token.csv \\
--service-node-port-range=30000-32767 \\
--kubelet-client-certificate=/opt/kubernetes/ssl/server.pem \\
--kubelet-client-key=/opt/kubernetes/ssl/server-key.pem \\
--tls-cert-file=/opt/kubernetes/ssl/server.pem  \\
--tls-private-key-file=/opt/kubernetes/ssl/server-key.pem \\
--client-ca-file=/opt/kubernetes/ssl/ca.pem \\
--service-account-key-file=/opt/kubernetes/ssl/ca-key.pem \\
--etcd-cafile=/opt/etcd/ssl/ca.pem \\
--etcd-certfile=/opt/etcd/ssl/server.pem \\
--etcd-keyfile=/opt/etcd/ssl/server-key.pem \\
--audit-log-maxage=30 \\
--audit-log-maxbackup=3 \\
--audit-log-maxsize=100 \\
--audit-log-path=/opt/kubernetes/logs/k8s-audit.log"
EOF

# 注：上面两个、\ 第一个是转义符，第二个是换行符，使用转义符是为了使用 EOF 保留换 行符。
# –logtostderr：启用日志
# —v：日志等级
# –log-dir：日志目录
# –etcd-servers：etcd 集群地址
# –bind-address：监听地址
# –secure-port：https 安全端口
# –advertise-address：集群通告地址
# –allow-privileged：启用授权
# –service-cluster-ip-range：Service 虚拟 IP 地址段
# –enable-admission-plugins：准入控制模块
# –authorization-mode：认证授权，启用 RBAC 授权和节点自管理
# –enable-bootstrap-token-auth：启用 TLS bootstrap 机制
# –token-auth-file：bootstrap token 文件
# –service-node-port-range：Service nodeport 类型默认分配端口范围
# –kubelet-client-xxx：apiserver 访问 kubelet 客户端证书
# –tls-xxx-file：apiserver https 证书
# –etcd-xxxfile：连接 Etcd 集群证书
# –audit-log-xxx：审计日志
```

使用 systemd 管理 apiserver：

```sh
cat > /usr/lib/systemd/system/kube-apiserver.service << EOF
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-apiserver.conf
ExecStart=/opt/kubernetes/bin/kube-apiserver \$KUBE_APISERVER_OPTS
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF
```

启动并设置开机启动：

```sh
systemctl daemon-reload
systemctl start kube-apiserver
systemctl enable kube-apiserver
systemctl status kube-apiserver 
```

授权 kubelet-bootstrap 用户允许请求证书：

```sh
kubectl create clusterrolebinding kubelet-bootstrap \
--clusterrole=system:node-bootstrapper \
--user=kubelet-bootstrap
```

**2、配置 kube-controller-manager**

生成 kube-controller-manager 配置文件：

```sh
cat > /opt/kubernetes/cfg/kube-controller-manager.conf << EOF
KUBE_CONTROLLER_MANAGER_OPTS="--logtostderr=false \\
--v=2 \\
--log-dir=/opt/kubernetes/logs \\
--leader-elect=true \\
--master=127.0.0.1:8080 \\
--bind-address=127.0.0.1 \\
--allocate-node-cidrs=true \\
--cluster-cidr=10.244.0.0/16 \\
--service-cluster-ip-range=10.0.0.0/24 \\
--cluster-signing-cert-file=/opt/kubernetes/ssl/ca.pem \\
--cluster-signing-key-file=/opt/kubernetes/ssl/ca-key.pem  \\
--root-ca-file=/opt/kubernetes/ssl/ca.pem \\
--service-account-private-key-file=/opt/kubernetes/ssl/ca-key.pem \\
--experimental-cluster-signing-duration=87600h0m0s"
EOF

# –master：通过本地非安全本地端口 8080 连接 apiserver。
# –leader-elect：当该组件启动多个时，自动选举（HA）
# –cluster-signing-cert-file/–cluster-signing-key-file：自动为 kubelet 颁发证书的 CA，与 apiserver 保持一致
```

使用 systemd 管理 kube-controller-manager：

```sh
cat > /usr/lib/systemd/system/kube-controller-manager.service << EOF
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-controller-manager.conf
ExecStart=/opt/kubernetes/bin/kube-controller-manager \$KUBE_CONTROLLER_MANAGER_OPTS
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF
```

启动并设置开机启动：

```sh
systemctl daemon-reload
systemctl start kube-controller-manager
systemctl enable kube-controller-manager
systemctl status kube-controller-manager
```

**3、部署 kube-scheduler**

生成 kube-scheduler 配置文件：

```sh
cat > /opt/kubernetes/cfg/kube-scheduler.conf << EOF
KUBE_SCHEDULER_OPTS="--logtostderr=false \
--v=2 \
--log-dir=/opt/kubernetes/logs \
--leader-elect \
--master=127.0.0.1:8080 \
--bind-address=127.0.0.1"
EOF

# 参数说明
# –master：通过本地非安全本地端口 8080 连接 apiserver。
# –leader-elect：当该组件启动多个时，自动选举（HA）
```

使用 systemd 管理 kube-scheduler：

```sh
cat > /usr/lib/systemd/system/kube-scheduler.service << EOF
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-scheduler.conf
ExecStart=/opt/kubernetes/bin/kube-scheduler \$KUBE_SCHEDULER_OPTS
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF
```

启动并设置开机启动：

```sh
systemctl daemon-reload
systemctl start kube-scheduler
systemctl enable kube-scheduler
systemctl status kube-scheduler
```

**4、使用 kubectl 命令行工具查看集群状态**

所有组件都已经启动成功，通过 kubectl 工具查看当前集群组件状态：

```sh
kubectl get cs
```

#### *部署 node 组件*

node 核心组件主要为以下 2 个，以下操作主要在 k8snode1 上执行：

- kubelet
- kube-proxy

**1、安装 kubelet**

```sh
mkdir -p /opt/kubernetes/{bin,cfg,ssl,logs}
```

生成 kubelet.conf 配置文件：

```sh
cat > /opt/kubernetes/cfg/kubelet.conf << EOF
KUBELET_OPTS="--logtostderr=false \\
--v=2 \\
--log-dir=/opt/kubernetes/logs \\
--hostname-override=m1 \\
--network-plugin=cni \\
--kubeconfig=/opt/kubernetes/cfg/kubelet.kubeconfig \\
--bootstrap-kubeconfig=/opt/kubernetes/cfg/bootstrap.kubeconfig \\
--config=/opt/kubernetes/cfg/kubelet-config.yml \\
--cert-dir=/opt/kubernetes/ssl \\
--pod-infra-container-image=lizhenliang/pause-amd64:3.0"
EOF

# –hostname-override：显示名称，集群中唯一
# –network-plugin：启用 CNI
# –kubeconfig：空路径，会自动生成，后面用于连接 apiserver
# –bootstrap-kubeconfig：首次启动向 apiserver 申请证书
# –config：配置参数文件
# –cert-dir：kubelet 证书生成目录
# –pod-infra-container-image：管理 Pod 网络容器的镜像
```

生成 kubelet-config.yml 配置文件：

```sh
cat > /opt/kubernetes/cfg/kubelet-config.yml << EOF
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
address: 0.0.0.0
port: 10250
readOnlyPort: 10255
cgroupDriver: cgroupfs
clusterDNS:
- 10.0.0.2
clusterDomain: cluster.local 
failSwapOn: false
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 2m0s
    enabled: true
  x509:
    clientCAFile: /opt/kubernetes/ssl/ca.pem 
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 5m0s
    cacheUnauthorizedTTL: 30s
evictionHard:
  imagefs.available: 15%
  memory.available: 100Mi
  nodefs.available: 10%
  nodefs.inodesFree: 5%
maxOpenFiles: 1000000
maxPods: 110
EOF
```

生成 bootstrap.kubeconfig 文件：
```sh
# apiserver IP:PORT
KUBE_APISERVER="https://192.168.60.151:6443" 
# 与 token.csv 里保持一致
TOKEN="c47ffb939f5ca36231d9e3121a252940" 
```

生成 kubelet bootstrap kubeconfig 配置文件：

```sh
kubectl config set-cluster kubernetes \
  --certificate-authority=/opt/kubernetes/ssl/ca.pem \
  --embed-certs=true \
  --server=${KUBE_APISERVER} \
  --kubeconfig=bootstrap.kubeconfig
kubectl config set-credentials "kubelet-bootstrap" \
  --token=${TOKEN} \
  --kubeconfig=bootstrap.kubeconfig
kubectl config set-context default \
  --cluster=kubernetes \
  --user="kubelet-bootstrap" \
  --kubeconfig=bootstrap.kubeconfig
kubectl config use-context default --kubeconfig=bootstrap.kubeconfig

mv bootstrap.kubeconfig /opt/kubernetes/cfg
```

systemd 管理 kubelet：

```sh
cat > /usr/lib/systemd/system/kubelet.service << EOF
[Unit]
Description=Kubernetes Kubelet
After=docker.service
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kubelet.conf
ExecStart=/opt/kubernetes/bin/kubelet \$KUBELET_OPTS
Restart=on-failure
LimitNOFILE=65536
[Install]
WantedBy=multi-user.target
EOF
```

启动并设置开机启动：

```sh
systemctl daemon-reload
systemctl start kubelet
systemctl enable kubelet
systemctl status kubelet
```

在 k8smaster1 上批准 kubelet 证书申请并加入集群：

```sh
# 查看 kubelet 证书请求
kubectl get csr

###    输出结果
###    NAME                                                   AGE    SIGNERNAME                                    REQUESTOR           CONDITION
###    node-csr-uCEGPOIiDdlLODKts8J658HrFq9CZ--K6M4G7bjhk8A   6m3s   kubernetes.io/kube-apiserver-client-kubelet   kubelet-bootstrap   Pending

# 批准申请
kubectl certificate approve node-csr-uCEGPOIiDdlLODKts8J658HrFq9CZ--K6M4G7bjhk8A

# 查看节点
kubectl get node
```

注：由于网络插件还没有部署，节点会没有准备就绪 NotReady

**2、部署 kube-proxy**

```sh
cat > /opt/kubernetes/cfg/kube-proxy.conf << EOF
KUBE_PROXY_OPTS="--logtostderr=false \\
--v=2 \\
--log-dir=/opt/kubernetes/logs \\
--config=/opt/kubernetes/cfg/kube-proxy-config.yml"
EOF
```

```sh
cat > /opt/kubernetes/cfg/kube-proxy-config.yml << EOF
kind: KubeProxyConfiguration
apiVersion: kubeproxy.config.k8s.io/v1alpha1
bindAddress: 0.0.0.0
metricsBindAddress: 0.0.0.0:10249
clientConnection:
  kubeconfig: /opt/kubernetes/cfg/kube-proxy.kubeconfig
hostnameOverride: m1
clusterCIDR: 10.0.0.0/24
EOF
```

生成 kube-proxy.kubeconfig 文件【**k8smaster1 生成再传到 k8snode1 和 k8snode2**】：

```sh
# 切换工作目录
cd ~/TLS/k8s
```

```sh
# 创建证书请求文件
cat > kube-proxy-csr.json << EOF
{
  "CN": "system:kube-proxy",
  "hosts": [],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "BeiJing",
      "ST": "BeiJing",
      "O": "k8s",
      "OU": "System"
    }
  ]
}
EOF
```

```sh
# 生成证书
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes kube-proxy-csr.json | cfssljson -bare kube-proxy
```

```sh
# 生成 kubeconfig 文件
KUBE_APISERVER="https://192.168.60.151:6443"
```

```sh
kubectl config set-cluster kubernetes \
  --certificate-authority=/opt/kubernetes/ssl/ca.pem \
  --embed-certs=true \
  --server=${KUBE_APISERVER} \
  --kubeconfig=kube-proxy.kubeconfig
kubectl config set-credentials kube-proxy \
  --client-certificate=./kube-proxy.pem \
  --client-key=./kube-proxy-key.pem \
  --embed-certs=true \
  --kubeconfig=kube-proxy.kubeconfig
kubectl config set-context default \
  --cluster=kubernetes \
  --user=kube-proxy \
  --kubeconfig=kube-proxy.kubeconfig
kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig

##### 转发到 k8snode1 #####
scp -r kube-proxy.kubeconfig root@192.168.60.152:/opt/kubernetes/cfg/
##### 转发到 k8snode2 #####
scp -r kube-proxy.kubeconfig root@192.168.60.153:/opt/kubernetes/cfg/
```

systemd 管理 kube-proxy：

```sh
cat > /usr/lib/systemd/system/kube-proxy.service << EOF
[Unit]
Description=Kubernetes Proxy
After=network.target
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-proxy.conf
ExecStart=/opt/kubernetes/bin/kube-proxy \$KUBE_PROXY_OPTS
Restart=on-failure
LimitNOFILE=65536
[Install]
WantedBy=multi-user.target
EOF
```

启动并设置开机启动：

```sh
systemctl daemon-reload
systemctl start kube-proxy
systemctl enable kube-proxy
systemctl status kube-proxy
```

#### *部署 CNI 网络插件*

下载 CNI 网络插件：

```sh
# 原地址
wget https://github.com/containernetworking/plugins/releases/download/v0.8.6/cni-plugins-linux-amd64-v0.8.6.tgz
# 码云地址【个人上传】【推荐】【速度较快】
wget https://gitee.com/bbigsun/kubernetes-study/raw/master/TLS/k8s/cni-plugins-linux-amd64-v0.8.6.tgz
```

安装插件：

```sh
mkdir -p /opt/cni/bin
tar -zxvf cni-plugins-linux-amd64-v0.8.6.tgz -C /opt/cni/bin
```

【k8smaster1 节点操作】：

```sh
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f kube-flannel.yml
```

#### *测试 kubernetes 集群*

在 Kubernetes 集群中创建一个 pod，验证是否正常运行【master 节点操作】：

```sh
# 下载 nginx 【会联网拉取 nginx 镜像】
kubectl create deployment nginx --image=nginx
# 查看状态
kubectl get pod
```

如果我们出现 Running 状态的时候，表示已经成功运行了

下面我们就需要将端口暴露出去，让其它外界能够访问

```sh
# 暴露端口
kubectl expose deployment nginx --port=80 --type=NodePort
# 查看一下对外的端口
kubectl get pod,svc
```

能够看到，我们已经成功暴露了 80 端口 到 30529 上

我们到我们的宿主机浏览器上，访问如下地址

```sh
http://192.168.177.130:30529/
```

发现我们的 nginx 已经成功启动了

## 三、Kubernetes 核心概念

### kubernetes 集群命令行工具 kubectl

####  kubectl 概述

kubectl 是 Kubernetes 集群的命令行工具，通过 kubectl 能够对集群本身进行管理，并能够在集群上进行容器化应用的安装和部署。

####  kubectl 命令格式

```
kubectl [command] [type] [name] [flags]
```

参数：

- command：指定要对资源执行的操作，例如 create、get、describe、delete

- type：指定资源类型，资源类型是大小写敏感的，开发者能够以单数 、复数 和 缩略的形式

  ```sh
  kubectl get pod pod1
  kubectl get pods pod1
  kubectl get po pod1
  ```

- name：指定资源的名称，名称也是大小写敏感的，如果省略名称，则会显示所有的资源，例如

  ```sh
  kubectl get pods
  ```

- flags：指定可选的参数，例如，可用 -s 或者 -server 参数指定 Kubernetes API server 的地址和端口

####  kubectl 帮助命令

```sh
# 获取 kubectl 的命令
kubectl --help

# 获取某个命令的介绍和使用
kubectl get --help
kubectl create --help
```

####  kubectl 基础命令

| 命令    | 介绍                                           |
| ------- | ---------------------------------------------- |
| create  | 通过文件名或标准输入创建资源                   |
| expose  | 将一个资源公开为一个新的 Service                |
| run     | 在集群中运行一个特定的镜像                     |
| set     | 在对象上设置特定的功能                         |
| get     | 显示一个或多个资源                             |
| explain | 文档参考资料                                   |
| edit    | 使用默认的编辑器编辑一个资源                   |
| delete  | 通过文件名，标准输入，资源名称或标签来删除资源 |

####  kubectl 部署命令

| 命令           | 介绍                                               |
| -------------- | -------------------------------------------------- |
| rollout        | 管理资源的发布                                     |
| rolling-update | 对给定的复制控制器滚动更新                         |
| scale          | 扩容或缩容 Pod 数量，Deployment、ReplicaSet、RC 或 Job |
| autoscale      | 创建一个自动选择扩容或缩容并设置 Pod 数量            |

#### kubectl 集群管理命令

| 命令         | 介绍                           |
| ------------ | ------------------------------ |
| certificate  | 修改证书资源                   |
| cluster-info | 显示集群信息                   |
| top          | 显示资源 (CPU/M)                |
| cordon       | 标记节点不可调度               |
| uncordon     | 标记节点可被调度               |
| drain        | 驱逐节点上的应用，准备下线维护 |
| taint        | 修改节点 taint 标记              |

#### kubectl 故障和调试命令

| 命令         | 介绍                                                         |
| ------------ | ------------------------------------------------------------ |
| describe     | 显示特定资源或资源组的详细信息                               |
| logs         | 在一个 Pod 中打印一个容器日志，如果 Pod 只有一个容器，容器名称是可选的 |
| attach       | 附加到一个运行的容器                                         |
| exec         | 执行命令到容器                                               |
| port-forward | 转发一个或多个                                               |
| proxy        | 运行一个 proxy 到 Kubernetes API Server                         |
| cp           | 拷贝文件或目录到容器中                                       |
| auth         | 检查授权                                                     |

####  kubectl 其它命令

| 命令         | 介绍                                                |
| ------------ | --------------------------------------------------- |
| apply        | 通过文件名或标准输入对资源应用配置                  |
| patch        | 使用补丁修改、更新资源的字段                        |
| replace      | 通过文件名或标准输入替换一个资源                    |
| convert      | 不同的 API 版本之间转换配置文件                       |
| label        | 更新资源上的标签                                    |
| annotate     | 更新资源上的注释                                    |
| completion   | 用于实现 kubectl 工具自动补全                         |
| api-versions | 打印受支持的 API 版本                                 |
| config       | 修改 kubeconfig 文件（用于访问 API，比如配置认证信息） |
| help         | 所有命令帮助                                        |
| plugin       | 运行一个命令行插件                                  |
| version      | 打印客户端和服务版本信息                            |

### Kubernetes 集群 YAML 文件详解

参考资料：[YAML 入门教程 | 菜鸟教程](https://www.runoob.com/w3cnote/yaml-intro.html)

#### YAML 概述

- YAML 文件 : 就是资源清单文件，用于资源编排。
- YAML : 仍是一种标记语言。为了强调这种语言以数据做为中心，而不是以标记语言为重点。
- YAML : 是一个可读性高，用来表达数据序列的格式。

#### YAML 基本语法

- 使用空格做为缩进
- 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
- 低版本缩进时不允许使用 Tab 键，只允许使用空格
- 使用#标识注释，从这个字符一直到行尾，都会被解释器忽略
- 使用 --- 表示新的 yaml 文件开始

####  YAML 数据结构

对象：键值对的集合，又称为映射 (mapping) / 哈希（hashes） / 字典（dictionary）

```
# 对象类型：对象的一组键值对，使用冒号结构表示
name: Tom
age: 18

# yaml 也允许另一种写法，将所有键值对写成一个行内对象
hash: {name: Tom, age: 18}
```

数组：

```
# 数组类型：一组连词线开头的行，构成一个数组
People
- Tom
- Jack

# 数组也可以采用行内表示法
People: [Tom, Jack]
```

####  YAML 组成部分

主要分为了两部分，一个是控制器的定义 和 被控制的对象。

在一个 YAML 文件的控制器定义中，有很多属性名称

| 属性名称   | 介绍       |
| ---------- | ---------- |
| apiVersion | API 版本    |
| kind       | 资源类型   |
| metadata   | 资源元数据 |
| spec       | 资源规格   |
| replicas   | 副本数量   |
| selector   | 标签选择器 |
| template   | Pod 模板    |
| metadata   | Pod 元数据  |
| spec       | Pod 规格    |
| containers | 容器配置   |

####  YAML 快速编写

一般来说，我们很少自己手写 YAML 文件，因为这里面涉及到了很多内容，我们一般都会借助工具来创建

**1、使用 kubectl create 命令**

这种方式一般用于资源没有部署的时候，我们可以直接创建一个 YAML 配置文件

```sh
# 尝试运行，并不会真正的创建镜像
kubectl create deployment web --image=nginx -o yaml --dry-run
```

或者我们可以输出到一个文件中

```sh
kubectl create deployment web --image=nginx -o yaml --dry-run > hello.yaml
```

然后我们就在文件中直接修改即可

**2、使用 kubectl get 命令导出 yaml 文件**

可以首先查看一个目前已经部署的镜像

```sh
kubectl get deploy
```

然后我们导出 nginx 的配置

```sh
kubectl get deploy nginx -o=yaml --export > nginx.yaml
```

然后会生成一个 `nginx.yaml` 的配置文件

### Pod

####  Pod 概述

**1、Pod 基本概念**

- 最小部署的单元
- Pod 里面是由一个或多个容器组成【一组容器的集合】
- 一个 pod 中的容器是共享网络命名空间
- Pod 是短暂的
- 每个 Pod 包含一个或多个紧密相关的用户业务容器

**2、Pod 存在的意义**

- 创建容器使用 docker，一个 docker 对应一个容器，一个容器运行一个应用进程
- Pod 是多进程设计，运用多个应用程序，也就是一个 Pod 里面有多个容器，而一个容器里面运行一个应用程序
- Pod 的存在是为了亲密性应用
  - 两个应用之间进行交互
  - 网络之间的调用【通过 127.0.0.1 或 socket】
  - 两个应用之间需要频繁调用

**3、k8s 业务类型**

>  Pod 是 K8S 集群中所有业务类型的基础，可以把 Pod 看作运行在 K8S 集群上的小机器人，不同类型的业务就需要不同类型的小机器人去执行。目前 K8S 的业务主要可以分为以下几种

- 长期伺服型：long-running
- 批处理型：batch
- 节点后台支撑型：node-daemon
- 有状态应用型：stateful application

上述的几种类型，分别对应的小机器人控制器为：Deployment、Job、DaemonSet 和 StatefulSet （后面将介绍控制器）

#### Pod 实现机制

> Pod 主要有以下两大机制：共享网络 和 共享存储。

**1、共享网络**【容器通过 **namespace** 和 **group** 进行隔离】

Pod 中容器通信 过程：

- 同一个 namespace 下
- 在 Pod 中创建一个根容器： `pause 容器`
- 在 Pod 中创建业务容器 【nginx，redis 等】【创建时会添加到 `info 容器` 中】
- 在 `info 容器` 中会独立出 ip 地址，mac 地址，port 等信息，然后实现网络的共享

**2、共享存储**【Pod 持久化数据，专门存储到某个地方中，使用 Volumn 数据卷进行共享存储】

#### Pod 镜像拉取策略

> 我们以具体实例来说，拉取策略就是 `imagePullPolicy`

拉取策略主要分为了以下几种：

- `IfNotPresent`：默认值，镜像在宿主机上不存在才拉取
- `Always`：每次创建 Pod 都会重新拉取一次镜像
- `Never`：Pod 永远不会主动拉取这个镜像

#### Pod 资源限制

> 也就是我们 Pod 在进行调度的时候，可以对调度的资源进行限制，例如我们限制 Pod 调度是使用的资源是 2C4G，那么在调度对应的 node 节点时，只会占用对应的资源，对于不满足资源的节点，将不会进行调度。

这里分了两个部分：

- `request`：表示调度所需的资源
- `limits`：表示最大所占用的资源

#### Pod 重启机制

> 因为 Pod 中包含了很多个容器，假设某个容器出现问题了，那么就会触发 Pod 重启机制

重启策略主要分为以下三种：

- `Always`：当容器终止退出后，总是重启容器，默认策略 【nginx 等，需要不断提供服务】
- `OnFailure`：当容器异常退出（退出状态码非 0）时，才重启容器。
- `Never`：当容器终止退出，从不重启容器 【批量任务】

####  Pod 健康检查

**1、通过容器检查**

```sh
kubectl get pod
```

**2、通过应用检查**

> 但是有的时候，程序可能出现了 **Java** 堆内存溢出，程序还在运行，但是不能对外提供服务了，这个时候就不能通过容器检查来判断服务是否可用了。需要通过应用检查。

```sh
# 存活检查，如果检查失败，将杀死容器，根据 Pod 的 restartPolicy【重启策略】来操作
livenessProbe

# 就绪检查，如果检查失败，Kubernetes 会把 Pod 从 Service endpoints 中剔除
readinessProbe
```

Probe 支持以下三种检查方式

- `http Get`：发送 HTTP 请求，返回 200 - 400 范围状态码为成功
- `exec`：执行 Shell 命令返回状态码是 0 为成功
- `tcpSocket`：发起 TCP Socket 建立成功

####  Pod 调度策略

 创建 Pod 流程：

- 首先创建一个 pod，然后创建一个 API Server 和 Etcd【把创建出来的信息存储在 etcd 中】
- 然后创建 Scheduler，监控 API Server 是否有新的 Pod，如果有的话，会通过调度算法，把 pod 调度某个 node 上
- 在 node 节点，会通过 `kubelet -- apiserver `读取 etcd 拿到分配在当前 node 节点上的 pod，然后通过 docker 创建容器

### Controller

####  Controller 内容简介

- 什么是 Controler
- Pod 和 Controller 的关系
- Deployment 控制器应用场景
- yaml 文件字段说明
- Deployment 控制器部署应用
- 升级回滚
- 弹性收缩

#### Controller 概述

> Controller 是集群上管理和运行容器的对象

- Controller 是实际存在的
- Pod 是虚拟机的

####  Pod 和 Controller 的关系

Pod 是通过 Controller 实现应用的运维，比如弹性收缩，滚动升级。

Pod 和 Controller 之间是通过 label 标签建立关系，同时 Controller 又被称为控制器工作负载。

- Controller【控制器】【工作负载】`selector: app:nginx`
- Pod【容器】`labels: app:nginx`

####  Deployment 控制器应用

> Deployment 表示用户对 K8S 集群的一次更新操作。

- Deployment 控制器可以部署无状态应用
- 管理 Pod 和 ReplicaSet
- 部署，滚动升级等功能
- 应用场景：web 服务，微服务

#### Deployment 部署应用

之前，使用 Deploment 部署应用，代码如下：【缺点：代码不好复用】

```sh
kubectrl create deployment web --image=nginx
```

现在，使用 YAML 文件进行配置：【快速编写 YAML 文件】

```sh
kubectl create deployment web --image=nginx -o yaml --dry-run > nginx.yaml
```

`nginx.yaml`  文件内容如下：【`selector `和 `label` 就是我们 Pod 和 Controller 之间建立关系的桥梁】

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  # Pod
  labels:
    app: web
  name: web
spec:
  replicas: 1
  # Controller
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
```

现在，使用`nginx.yaml`文件创建镜像：

```sh
kubectl apply -f nginx.yaml
```

然后，对外暴露端口：

```sh
kubectl expose deployment web --port=80 --type=NodePort --target-port=80 --name=web1

# 参数说明
# --port：就是我们内部的端口号
# --target-port：就是暴露外面访问的端口号
# --name：名称
# --type：类型
```

同理，导出配置文件：

```sh
kubectl expose deployment web --port=80 --type=NodePort --target-port=80 --name=web1 -o yaml > web1.yaml
```

查看端口：

```sh
kubectl get pods,svc

# 输出结果
NAME                       READY   STATUS    RESTARTS   AGE
pod/web-5dcb957ccc-d89v9   1/1     Running   0          8m35s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        2d5h
service/web1         NodePort    10.111.61.143   <none>        80:30344/TCP   6s
```

然后我们访问对应的 url，即可看到 nginx 了 `http://192.168.60.151:30344/`

<img src="./images/nginx.png" style="zoom:150%;" >

####  升级回滚和弹性收缩

- 升级： 假设从版本为 1.14 升级到 1.15 ，这就叫应用的升级【升级可以保证服务不中断】
- 回滚：从版本 1.15 变成 1.14，这就叫应用的回滚
- 弹性伸缩：我们根据不同的业务场景，来改变 Pod 的数量对外提供服务，这就是弹性伸缩

**1、创建一个 1.14 版本的 pod**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      # 修改 nginx 版本 1.14
      - image: nginx:1.14
        name: nginx
        resources: {}
status: {}
```

```sh
kubectl apply -f nginx.yaml
```

**2、应用升级**

```sh
kubectl set image deployment web nginx=nginx:1.15
```

升级过程：

```sh
[root@k8smaster ~]# kubectl set image deployment web nginx=nginx:1.15
deployment.apps/web image updated

# 首先是开始的 nginx 1.14 版本的 Pod 在运行，然后 1.15 版本的在创建
[root@k8smaster ~]# kubectl get pod
NAME                   READY   STATUS              RESTARTS   AGE
web-66bf4959f5-qhzsd   1/1     Running             0          52s
web-bbcf684cb-bbmqv    0/1     ContainerCreating   0          3s

# 然后在 1.15 版本创建完成后，就会暂停 1.14 版本
[root@k8smaster ~]# kubectl get pod
NAME                   READY   STATUS        RESTARTS   AGE
web-66bf4959f5-qhzsd   1/1     Terminating   0          67s
web-bbcf684cb-bbmqv    1/1     Running       0          18s

# 最后把 1.14 版本的 Pod 移除，完成我们的升级
[root@k8smaster ~]# kubectl get pod
NAME                  READY   STATUS    RESTARTS   AGE
web-bbcf684cb-bbmqv   1/1     Running   0          33s
```

> 我们在下载 1.15 版本，容器就处于 ContainerCreating 状态，然后下载完成后，就用 1.15 版本去替换 1.14 版本了，这么做的好处就是：升级可以保证服务不中断

**3、查看升级状态**

```sh
kubectl rollout status deployment web
```

**4、查看历史版本**

```sh
kubectl rollout history deployment web
```

**5、应用回滚**

```sh
# 回滚到上一版本
kubectl rollout undo deployment web

# 回滚到指定版本
kubectl rollout undo deployment web --to-revision=2
```

**6、弹性伸缩**

```sh
# 通过命令创建多个副本
kubectl scale deployment web --replicas=10

# 输出结果，等一会就会全部 Running
[root@k8smaster ~]# kubectl scale deployment web --replicas=10
deployment.apps/web scaled
[root@k8smaster ~]# kubectl get pod
NAME                  READY   STATUS              RESTARTS   AGE
web-bbcf684cb-2f2zl   0/1     ContainerCreating   0          4s
web-bbcf684cb-72pzr   0/1     ContainerCreating   0          4s
web-bbcf684cb-bbmqv   1/1     Running             0          3m9s
web-bbcf684cb-fgpgh   0/1     ContainerCreating   0          4s
web-bbcf684cb-fpk8d   0/1     ContainerCreating   0          4s
web-bbcf684cb-hqp4z   0/1     ContainerCreating   0          4s
web-bbcf684cb-htq2d   0/1     ContainerCreating   0          4s
web-bbcf684cb-lnkwx   0/1     ContainerCreating   0          4s
web-bbcf684cb-vmwb9   0/1     ContainerCreating   0          4s
web-bbcf684cb-vnk5w   0/1     ContainerCreating   0          4s
```

### Kubernetes 配置管理

####  Secret

> Secret 的主要作用就是加密数据

1、Secret 应用场景

​	对 用户名 和 密码 进行加密

2、Secret 三种类型

- `Opaque`：使用 base64 编码存储信息，可以通过 base64 --decode 解码获得原始数据，因此安全性弱。
- `kubernetes.io/dockerconfigjson`：用于存储 docker registry 的认证信息。
- `kubernetes.io/service-account-token`：用于被 serviceaccount 引用。serviceaccout 创建时 Kubernetes 会默认创建对应的 secret。Pod 如果使用了 serviceaccount，对应的 secret 会自动挂载到 Pod 的 /run/secrets/kubernetes.io/serviceaccount 目录中。

3、Secret 创建

（1）命令行方式创建 Secret

```sh
echo -n "admin" > ./username.txt
echo -n "1f1f1f1f1f" > ./password.txt

# 使用 kubectl create secret 命令创建 secret
kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
#  secret/db-user-pass created

# 查看 secret
kubectl get secrets
#  NAME                  TYPE                                  DATA   AGE
#  db-user-pass          Opaque                                2      59s
```

（2）yaml 文件方式创建 Secret

```sh
echo -n 'admin' | base64
#  YWRtaW4=
echo -n '1f1f1f1f1f' | base64
#  MWYxZjFmMWYxZg==

# 创建 secret：创建 yaml 文件
cat > secret.yaml << EOF
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYxZjFmMWYxZg==
EOF

# 创建 secret：使用 yaml 文件创建 secret
kubectl create -f secret.yaml
#  secret/mysecret created

# 查看 secret
kubectl get secrets | grep mysecret
#  mysecret              Opaque                                2      32s

# 查看 secret 详细信息
kubectl describe secrets mysecret
# 查看 secret yaml 文件
kubectl get secrets mysecret -o yaml
```

4、Secret 使用【两种方式】

- 以 Volume 形式
- 以环境变量形式

（1）将 Secret 挂载到 Volume 中

```sh
cat > mypod1.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: mypod1
spec:
  containers:
  - name: mypod1
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
EOF

kubectl create -f mypod1.yaml
#  pod/mypod1 created
kubectl get pods | grep mypod
#  mypod1                1/1     Running   0          48s
kubectl exec -it mypod1 /bin/bash

## 查看密码和用户名
root@mypod1:/data# cd /etc/foo/
root@mypod1:/etc/foo# ls
password  username
root@mypod1:/etc/foo# cat password 
1f1f1f1f1f
root@mypod1:/etc/foo# cat username 
admin
root@mypod1:/etc/foo# 
```

（2）将 Secret 设置为环境变量

```sh
cat > mypod2.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: mypod2
spec:
  containers:
  - name: mypod2
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password
  restartPolicy: Never
EOF
  
kubectl create -f mypod2.yaml
#  pod/mypod2 created
kubectl get pods | grep mypod
#  mypod1                1/1     Running             0          4m39s
#  mypod2                0/1     ContainerCreating   0          6s
#  等   mypod2    running   之后在进入容器
kubectl exec -it mypod2 /bin/bash

## 查看环境变量
root@mypod2:/data# env | grep -E "USERNAME|PASSWORD"
SECRET_USERNAME=admin
SECRET_PASSWORD=1f1f1f1f1f
```

####  ConfigMap

> ConfigMap 作用是存储不加密的数据到 etcd 中

1、应用场景

​	配置文件

2、创建

（1）yaml 文件方式创建

```sh
cat > configmap-test01.yaml << EOF
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm-test01
data:
  appconf01: value01
  appconf02: value02
EOF

kubectl create -f configmap-test01.yaml
```

（2）命令行方式创建

> 读取文件方式（也可以是目录）通过`--from-file`参数从文件中读取。可以指定 key 的名称，若不指定，则默认使用文件名为 key。

```sh
cat > test.properties << EOF
key01:value01
key02:value02
conf01: value03
EOF

kubectl create cm cm-test-file --from-file=test.properties
```

3、查询

```sh
# 查看 configmap 列表
kubectl get configmap
# 查看 configmap 详情
kubectl describe configmap cm-test01
kubectl describe configmap cm-test-file
kubectl describe cm cm-test-literal
# 查看 yaml 输出
kubectl get cm cm-test01 -o yaml
kubectl get configmap cm-test-file -o yaml
kubectl get cm cm-test-literal -o yaml
```

4、更新

```sh
# 方式一：edit
kubectl edit cm cm-test01
# 查看更新是否生效
kubectl describe cm cm-test01
# 方式二：apply
kubectl apply -f configmap-test01.yaml
```

5、删除

```sh
# 方式一：通过 yaml 文件删除
kubectl delete -f configmap-test01.yaml
# 方式二：直接删除资源
kubectl delete cm cm-test-file
```

6、使用 【yaml 文件有误，以下四种方式无误】

容器应用对 ConfigMap 的使用主要是两种：

- 通过环境变量获取 ConfigMap 的内容：`spec.env`和`spec.envFrom`
- 通过卷 volume 挂载的方式将 ConfigMap 的内容挂载到容器内部的文件或目录：`spec.volumes`

（1）`spec.env` 【环境变量】

```sh
vim pod-test01.yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-test001
spec:
  containers:
  - name: cm-test
    image: tomcat:8
    command: [ "/bin/sh", "-c", "env | grep APP"]
    env:
    - name: APPCONF01 		# 定义环境变量的名称
      valueFrom:	  		# key “appconf01”的值获取
        configMapKeyRef:
          name: cm-test01	# 环境变量的值来自于 configmap cm-test01
          key: appconf01	# configmap 中的配置 key 为 appconf01
    - name: APPCONF02		# 定义环境变量的名称
      valueFrom:			# key “appconf02”的值获取
        configMapKeyRef: 
          name: cm-test01	# 环境变量的值来自于 configmap cm-test01
          key: appconf02	# configmap 中的配置 key 为 appconf02
  restartPolicy: Never		# 重启策略：从不。

kubectl create -f pod-test01.yaml
kubectl get pods | grep cm
kubectl logs cm-pod-test001
```

（2）`spec.envFrom` 【环境变量】

```sh
vim pod-test02.yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-test002
spec:
  containers:
  - name: cm-test2
    image: tomcat:8
    command: [ "/bin/sh", "-c", "env"]
    envFrom:
    - configMapRef:
      name: cm-test01	# 根据 ConfigMap cm-test01 资源自动生成环境变量
  restartPolicy: Never

kubectl create -f pod-test02.yaml
kubectl get po
```

（3）指定 items【卷挂载方式】

```sh
vim pod-test03.yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-test003
spec:
  containers:
  - name: cm-test3
    image: tomcat:8
    volumeMounts:
    - name: vm-01-1
      mountPath: /conf
  volumes:
  - name: vm-01-1
    configMap:
      name: cm-test-file
      items:
      - key: key-testproperties
        path: test.properties
  restartPolicy: Never

kubectl create -f pod-test03.yaml
kubectl get po
```

（4）不指定 items【卷挂载方式】

```sh
vim pod-test04.yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-test004
spec:
  containers:
  - name: cm-test4
    image: tomcat:8
    volumeMounts:
    - name: vm-02-2
      mountPath: /conf
  volumes:
  - name: vm-02-2
    configMap:
      name: cm-test-file
  restartPolicy: Never

kubectl create -f pod-test04.yaml
kubectl get po

# 进入容器查看
kubectl exec -it cm-pod-test004 -c cm-test4 -- bash
root@cm-pod-test004:/usr/local/tomcat# ls /conf
```

### Kubernetes 集群安全机制

####  API-SERVER

> Kubernetes api-server 安全访问机制

当我们访问 K8S 集群时，都需要经过 apiserver【 apiserver 做统一协调】，每个请求到达 apiserver 需要经过三个安全关卡：`① 认证` `② 鉴权 ` `③ 准入控制`

- 访问过程中，需要证书、token、或者用户名和密码
- 如果访问 pod 需要 serviceAccount

<img src="./images/api-server.png">

**1、认证**

对外不暴露 8080 端口，只能内部访问，对外使用的端口 6443

客户端身份认证常用方式

- https 证书认证，基于 ca 证书
- http token 认证，通过 token 来识别用户
- http 基本认证，用户名 + 密码认证

**2、鉴权**

基于 RBAC 进行鉴权操作

基于角色访问控制

**3、准入控制**

就是准入控制器的列表，如果列表有请求内容就通过，没有的话 就拒绝

#### TLS

> Kubernetes 认证方式之客户端证书（TLS）

客户端证书（TLS）认证方式，也叫 HTTPS 双向认证。一般我们访问一个 https 网站，认证是单向的，只有客户端会验证服务端的身份，服务端不会管客户端身份如何。

####  RBAC 介绍

> Kubernetes 授权方式之 RBAC 
>
> 基于角色的访问控制，为某个角色设置访问内容，然后用户分配该角色后，就拥有该角色的访问权限

k8s 中有默认的几个角色

- role：特定命名空间访问权限
- ClusterRole：所有命名空间的访问权限

角色绑定

- roleBinding：角色绑定到主体
- ClusterRoleBinding：集群角色绑定到主体

主体

- user：用户
- group：用户组
- serviceAccount：服务账号

####  RBAC 鉴权

1、创建命名空间

```sh
# 查看已经存在的命名空间
kubectl get namespace
# 创建自己的命名空间
kubectl create ns mytest
```

2、命名空间内创建 Pod

> 如果不创建命名空间，Pod 默认在 default

```sh
kubectl run nginx --image=nginx -n mytest
```

3、创建角色

> 通过 rbac-role.yaml 进行创建
>
> tips: 这个角色只对 pod 有 get 和 list 权限

```sh
cat > rbac-role.yaml << EOF
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: mytest
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
EOF
```

通过 yaml 创建 role

```sh
# 创建
kubectl apply -f rbac-role.yaml
# 查看
kubectl get role -n mytest
```

4、创建角色绑定

通过 rbac-rolebinding.yaml 的方式，来创建我们的角色绑定

```sh
cat > rbac-rolebinding.yaml << EOF
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: mytest
  name: read-pods
subjects:
- kind: User
  name: lucy
  apiGroup: rbac.authorization.k8s.io
roleRef: 
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
EOF

kubectl apply -f rbac-rolebinding.yaml
kubectl get role,rolebinding -n mytest

#  NAME                                        CREATED AT
#  role.rbac.authorization.k8s.io/pod-reader   2022-01-04T03:05:57Z
#  NAME                                              ROLE              AGE
#  rolebinding.rbac.authorization.k8s.io/read-pods   Role/pod-reader   35s
```

## 四、Kubernetes 版本发布

>  接下来演示一下如何在 Kubernetes 集群中部署项目

### 容器交付流程

- 开发代码阶段
  - 编写代码
  - 编写 Dockerfile【打镜像做准备】
- 持续交付/集成
  - 代码编译打包
  - 制作镜像
  - 上传镜像仓库
- 应用部署
  - Pod
  - Service
  - Ingress
- 运维
  - 监控
  - 故障排查
  - 应用升级

###  k8s 部署 Python 项目流程

- 制作镜像【Dockerfile】
- 上传到镜像仓库【Dockerhub、阿里云、网易】
- 控制器部署镜像【Deployment】
- 对外暴露应用【Service、Ingress】
- 运维【监控、升级】

### k8s 部署 Python 项目

#### 下载 Python 项目

> 这里已经提前准备了一个 demo 项目
>
> git clone https://gitee.com/bbigsun/demo-python.git

```text
.
├── djangopro
│   ├── db.sqlite3
│   ├── djangopro
│   │   ├── asgi.py
│   │   ├── __init__.py
│   │   ├── __pycache__
│   │   │   ├── __init__.cpython-310.pyc
│   │   │   ├── settings.cpython-310.pyc
│   │   │   ├── urls.cpython-310.pyc
│   │   │   └── wsgi.cpython-310.pyc
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   └── manage.py
├── Dockerfile
└── README.md
```

#### 制作镜像

> 这里已经写好了 Dockerfile

```dockerfile
FROM python:3.10
RUN pip install django -i https://pypi.tuna.tsinghua.edu.cn/simple
CMD mkdir -p /opt/demo-python
COPY  . /opt/demo-python
WORKDIR /opt/demo-python/djangopro
EXPOSE 8000
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

打包镜像：

```sh
cd demo-python
docker build . -t django:v1 -f Dockerfile
```

测试镜像：

```sh
docker run -d --name django -p 8000:8000 mydjango:v1
```

浏览器访问：http://<虚拟机IP>:8000

上传到镜像仓库：（本地仓库）

```sh
## 搭建私人仓库
mkdir -p /data/myregistry
docker pull registry:latest
docker run -d -p 5000:5000 --name my_registry --restart=always -v /data/myregistry:/var/lib/registry registry:latest

## 更改 docker 配置文件（在需要连接到私有仓库的机器上全部都执行一遍）
## 在 k8smaster k8snode1 k8snode2 上均执行一遍
cat > /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"],
  "insecure-registries": ["192.168.60.151:5000"]
}
EOF
## 重启 docker，重启 registry（如果停止了的话）
systemctl restart docker  # 3 台机器上执行
docker start my_registry  # 主节点上执行（因为私人仓库在主节点上）
```

访问：`ip:5000/v2/_catalog`查看本地仓库镜像

<img src="images/dockerregistry.png">

测试本地私有仓库：

```sh
docker tag mydjango:v1 192.168.60.151:5000/test/mydjango:v2
docker push 192.168.60.151:5000/test/mydjango:v2
```

访问：`ip:5000/v2/_catalog`查看本地仓库镜像

在 node 节点上测试：

```sh
docker pull 192.168.60.151:5000/test/mydjango:v2
```

#### 部署项目

```sh
kubectl create deployment test01 --image=192.168.60.151:5000/test/mydjango:v2 --dry-run -o yaml > test01.yaml
kubectl create -f test01.yaml
kubectl get pod -o wide
kubectl expose deployment test01 --port=8000 --target-port=8000 --type=NodePort

## 查看暴露的端口
[root@k8smaster test]# kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
test01       NodePort    10.109.195.123   <none>        8000:31954/TCP   15s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP          6d

```

浏览器访问：`ip:31954`
