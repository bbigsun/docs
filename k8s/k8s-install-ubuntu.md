# k8s 集群部署 | Ubuntu



## 安装 Kubeadm



### 准备开始

- 一台兼容的 Linux 主机。Kubernetes 项目为基于 Debian 和 Red Hat 的 Linux 发行版以及一些不提供包管理器的发行版提供通用的指令
- 每台机器 2 GB 或更多的 RAM （如果少于这个数字将会影响你应用的运行内存)
- 2 CPU 核或更多
- 集群中的所有机器的网络彼此均能相互连接(公网和内网都可以)
- 节点之中不可以有重复的主机名、MAC 地址或 product_uuid。
- 开启机器上的某些端口。
- 禁用交换分区。为了保证 kubelet 正常工作，你 **必须** 禁用交换分区。

```sh
sudo swapoff -a
sudo sed -ri 's/.*swap.*/#&/' /etc/fstab
```



### 确保每个节点上 MAC 地址和 product_uuid 的唯一性

- 你可以使用命令 `ip link` 或 `ifconfig -a` 来获取网络接口的 MAC 地址
- 可以使用 `sudo cat /sys/class/dmi/id/product_uuid` 命令对 product_uuid 校验

一般来讲，硬件设备会拥有唯一的地址，但是有些虚拟机的地址可能会重复。 Kubernetes 使用这些值来唯一确定集群中的节点。 如果这些值在每个节点上不唯一，可能会导致安装失败。



### 检查网络适配器

如果你有一个以上的网络适配器，同时你的 Kubernetes 组件通过默认路由不可达，我们建议你预先添加 IP 路由规则，这样 Kubernetes 集群就可以通过对应的适配器完成连接。



### 允许 iptables 检查桥接流量

确保 `br_netfilter` 模块被加载。这一操作可以通过运行 `lsmod | grep br_netfilter` 来完成。若要显式加载该模块，可执行 `sudo modprobe br_netfilter`。（如果使用该模块，请配置cgroup）

为了让你的 Linux 节点上的 iptables 能够正确地查看桥接流量，你需要确保在你的 `sysctl` 配置中将 `net.bridge.bridge-nf-call-iptables` 设置为 1。例如：

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```



### 检查所需端口

启用这些必要的端口后才能使 Kubernetes 的各组件相互通信。可以使用 telnet 来检查端口是否启用，例如：

```shell
telnet 127.0.0.1 6443
```

你使用的 Pod 网络插件 (详见后续章节) 也可能需要开启某些特定端口。由于各个 Pod 网络插件的功能都有所不同， 请参阅他们各自文档中对端口的要求。



### 安装 runtime

为了在 Pod 中运行容器，Kubernetes 使用 **容器运行时（Container Runtime）**。

- [Linux 节点](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#container-runtimes-0)
- [其它操作系统](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#container-runtimes-1)



默认情况下，Kubernetes 使用 **容器运行时接口（Container Runtime Interface，CRI）**来与你所选择的容器运行时交互。

如果你不指定运行时，则 kubeadm 会自动尝试检测到系统上已经安装的运行时， 方法是扫描一组众所周知的 Unix 域套接字。 下面的表格列举了一些容器运行时及其对应的套接字路径：

| 运行时     | 域套接字                        |
| ---------- | ------------------------------- |
| Docker     | /var/run/dockershim.sock        |
| containerd | /run/containerd/containerd.sock |
| CRI-O      | /var/run/crio/crio.sock         |


如果同时检测到 Docker 和 containerd，则优先选择 Docker。 这是必然的，因为 Docker 18.09 附带了 containerd 并且两者都是可以检测到的， 即使你仅安装了 Docker。 如果检测到其他两个或多个运行时，kubeadm 输出错误信息并退出。

kubelet 通过内置的 `dockershim` CRI 实现与 Docker 集成。



***使用官方脚本安装 docker***【Linux 通用】

```sh
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

也可以使用国内 daocloud 一键安装命令

```sh
curl -sSL https://get.daocloud.io/docker | sh
```



***使用 apt 安装 docker***【Ubuntu 不推荐，太长了】

```sh
sudo apt-get update

sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
    
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88

sudo add-apt-repository \
   "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/ \
  $(lsb_release -cs) \
  stable"

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```



### 配置 Docker 守护程序

- 使用 systemd 来管理容器的 cgroup。

- 配置 docker 的镜像源（加速镜像拉取速度）。

```sh
cat <<EOF | sudo tee /etc/docker/daemon.json
{
	"registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"],
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
```



### 安装 kubeadm、kubelet 和 kubectl

你需要在每台机器上安装以下的软件包：

- `kubeadm`：用来初始化集群的指令。
- `kubelet`：在集群中的每个节点上用来启动 Pod 和容器等。
- `kubectl`：用来与集群通信的命令行工具。

> kubeadm **不能** 帮你安装或者管理 `kubelet` 或 `kubectl`，所以你需要 确保它们与通过 kubeadm 安装的控制平面的版本相匹配。 如果不这样做，则存在发生版本偏差的风险，可能会导致一些预料之外的错误和问题。 然而，控制平面与 kubelet 间的相差一个次要版本不一致是支持的，但 kubelet 的版本不可以超过 API 服务器的版本。 例如，1.7.0 版本的 kubelet 可以完全兼容 1.8.0 版本的 API 服务器，反之则不可以。



1、更新 `apt` 包索引并安装使用 Kubernetes `apt` 仓库所需要的包：

```shell
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

2、下载 Google Cloud 公开签名秘钥：（谷歌连不上，我们用阿里云的）

```shell
wget https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg && mv apt-key.gpg /usr/share/keyrings/kubernetes-archive-keyring.gpg
```

3、添加 Kubernetes `apt` 仓库：（同理，谷歌连不上，我们用阿里云的）

```shell
echo "deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

因为使用阿里云的仓库，需要解决 public key 问题，执行下面命令：

```sh
cat /usr/share/keyrings/kubernetes-archive-keyring.gpg | sudo apt-key add -
```

4、更新 `apt` 包索引，安装 kubelet、kubeadm 和 kubectl，并锁定其版本：

```shell
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```



### 配置 cgroup 驱动程序

容器运行时和 kubelet 都具有名字为 ["cgroup driver"](https://kubernetes.io/zh/docs/setup/production-environment/container-runtimes/) 的属性，该属性对于在 Linux 机器上管理 CGroups 而言非常重要。

默认 systemd

**警告：**

你需要确保容器运行时（docker）和 kubelet 所使用的是相同的 cgroup 驱动，否则 kubelet 进程会失败。

相关细节可参见[配置 cgroup 驱动](https://kubernetes.io/zh/docs/tasks/administer-cluster/kubeadm/configure-cgroup-driver/)。





## Kubeadm 启动 k8s 集群



### 初始化控制平面节点

请执行命令：

```sh
kubeadm init --apiserver-advertise-address=192.168.60.160 --image-repository registry.aliyuncs.com/google_containers  --service-cidr=10.96.0.0/12  --pod-network-cidr=10.244.0.0/16
```

参数说明：

- `--apiserver-advertise-address`        当前主机 IP
- `--image-repository`                               指定镜像仓库
- `--kubernetes-version`                           指定当前版本
- `--service-cidr`                                        指定 service 网段
- `--pod-network-cidr`                                指定 pod 网段 

如果想要非 root 用户使用 k8s 集群，创建以下文件：

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```



### 安装 CNI 网络插件

查看 节点 状态

```sh
kubectl get node
```

上面的状态还是NotReady，下面我们需要安装网络插件，来进行联网访问：

```sh
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# ........... 让子弹飞一会儿 ...........

kubectl get pods -n kube-system
```

运行后的结果为Ready状态

```sh
NAME                     STATUS   ROLES                  AGE   VERSION
ubuntu-virtual-machine   Ready    control-plane,master   15h   v1.23.5
```



