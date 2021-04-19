kubeadm 是官方社区推出的一个用于快速部署 kubernetes 集群的工具。

这个工具能通过两条指令完成一个 kubernetes 集群的部署：

```bash
# 创建一个 Master 节点
$ kubeadm init
 
# 将一个 Node 节点加入到当前集群中
$ kubeadm join <Master节点的IP和端口 >
```
## 1. 安装要求

在开始之前，部署 Kubernetes 集群机器需要满足以下几个条件：

* 一台或多台机器，操作系统 CentOS7.x-86_x64
* 硬件配置：2GB 或更多 RAM，2 个 CPU 或更多 CPU，硬盘 30GB 或更多
* 可以访问外网，需要拉取镜像，如果服务器不能上网，需要提前下载镜像并导入节点
* 禁止 swap 分区
## 2. 准备环境

|**角色**|**IP**|
|:----|:----|:----|:----|
| master | 192.168.62.132|
| node1  | 192.168.62.133|
| node2  | 192.168.62.134|

```bash
# 关闭防火墙
systemctl stop firewalld
systemctl disable firewalld
# 关闭selinux
sed -i 's/enforcing/disabled/' /etc/selinux/config # 永久
setenforce 0 # 临时
# 关闭swap
swapoff -a # 临时
sed -ri 's/.*swap.*/#&/' /etc/fstab # 永久
# 根据规划设置主机名
hostnamectl set-hostname <hostname>
# 在master添加hosts
cat >> /etc/hosts << EOF
192.168.62.132 k801
192.168.62.133 k8s02
192.168.62.134 k8s03
EOF
# 将桥接的IPv4流量传递到iptables的链
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system # 生效
# 时间同步
yum install ntpdate -y
ntpdate time.windows.com
```
## 3. 所有节点安装 Docker/kubeadm/kubelet

Kubernetes 默认 CRI（容器运行时）为 Docker，因此先安装 Docker。

### 3.1 安装 Docker

```bash
$ wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
$ yum -y install docker-ce-18.06.1.ce-3.el7
$ systemctl enable docker && systemctl start docker
$ docker --version
Docker version 18.06.1-ce, build e68fc7a
```
```properties
$ cat > /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
}
EOF
```
### 3.2 添加阿里云 YUM 软件源

```properties
$ cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```
### 3.3 安装 kubeadm，kubelet 和 kubectl

由于版本更新频繁，这里指定版本号部署：

```bash
$ yum install -y kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
$ systemctl enable kubelet
```
## 4. 部署 Kubernetes Master

在 192.168.62.132（Master）执行。

```bash
$ kubeadm init \
--apiserver-advertise-address=192.168.62.132 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.18.0 \
--service-cidr=10.96.0.0/12 \
--pod-network-cidr=10.244.0.0/16
```
由于默认拉取镜像地址 k8s.gcr.io 国内无法访问，这里指定阿里云镜像仓库地址。
使用 kubectl 工具：

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
kubectl get nodes
```
## 5. 加入 Kubernetes Node

在 192.168.62.133/134（Node）执行。

向集群添加新节点，执行在 kubeadm init 输出的 kubeadm join 命令：

```bash
$ kubeadm join 192.168.62.132:6443 --token dcxghk.5zgiiw6yk7qf5wol \
    --discovery-token-ca-cert-hash sha256:aad826e486e6728e176b14e803199a42805572ed8b266269d7581f1e244df33c
```
默认 token 有效期为 24 小时，当过期之后，该 token 就不可用了。这时就需要重新创建 token，操作如下：
```bash
kubeadm token create --print-join-command
```
## 6. 部署 CNI 网络插件

```bash
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
默认镜像地址无法访问，sed 命令修改为 docker hub 镜像仓库。

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
或者用啊里的源
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel-aliyun.yml

kubectl get pods -n kube-system
NAME READY STATUS RESTARTS AGE
kube-flannel-ds-amd64-2pc95 1/1 Running 0 72s
```
## 7. 测试 kubernetes 集群

在 Kubernetes 集群中创建一个 pod，验证是否正常运行：

```shell
$ kubectl create deployment nginx --image=nginx
$ kubectl expose deployment nginx --port=80 --type=NodePort
$ kubectl get pod,svc
```
访问地址：http://NodeIP:Port





