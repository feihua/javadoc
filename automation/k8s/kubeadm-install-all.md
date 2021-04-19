kubeadm 是官方社区推出的一个用于快速部署 kubernetes 集群的工具。

这个工具能通过两条指令完成一个 kubernetes 集群的部署：

```shell
# 创建一个 Master 节点
$ kubeadm init
# 将一个 Node 节点加入到当前集群中
$ kubeadm join <Master 节点的 IP 和端口 >
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
| master1       | 192.168.44.155 |
| master2       | 192.168.44.156 |
| node1         | 192.168.44.157 |
| VIP（虚拟 ip） | 192.168.44.158 |

```properties
# 关闭防火墙
systemctl stop firewalld
systemctl disable firewalld
# 关闭 selinux
sed -i 's/enforcing/disabled/' /etc/selinux/config # 永久
setenforce 0 # 临时
# 关闭 swap
swapoff -a # 临时
sed -ri 's/.*swap.*/#&/' /etc/fstab # 永久
# 根据规划设置主机名
hostnamectl set-hostname <hostname>
# 在 master 添加 hosts
cat >> /etc/hosts << EOF
192.168.44.158 master.k8s.io k8s-vip
192.168.44.155 master01.k8s.io master1
192.168.44.156 master02.k8s.io master2
192.168.44.157 node01.k8s.io node1
EOF
# 将桥接的 IPv4 流量传递到 iptables 的链
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system # 生效
# 时间同步
yum install ntpdate -y
ntpdate time.windows.com
```
## 3. 所有 master 节点部署 keepalived

### 3.1 安装相关包和 keepalived

```bash
yum install -y conntrack-tools libseccomp libtool-ltdl
yum install -y keepalived
```
### 3.2 配置 master 节点

master1 节点配置

```properties
cat > /etc/keepalived/keepalived.conf <<EOF
! Configuration File for keepalived
global_defs {
    router_id k8s
}
vrrp_script check_haproxy {
    script "killall -0 haproxy"
    interval 3
    weight -2
    fall 10
    rise 2
}
vrrp_instance VI_1 {
    state MASTER
    interface ens33
    virtual_router_id 51
    priority 250
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass ceb1b3ec013d66163d6ab
    }
    virtual_ipaddress {
        192.168.44.158
    }
    track_script {
        check_haproxy
    }
}
EOF
```
master2 节点配置
```properties
cat > /etc/keepalived/keepalived.conf <<EOF
! Configuration File for keepalived
global_defs {
    router_id k8s
}
vrrp_script check_haproxy {
    script "killall -0 haproxy"
    interval 3
    weight -2
    fall 10
    rise 2
}
vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 51
    priority 200
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass ceb1b3ec013d66163d6ab
    }
    virtual_ipaddress {
        192.168.44.158
    }
    track_script {
        check_haproxy
    }
}
EOF
```
### 3.3 启动和检查

在两台 master 节点都执行

```bash
# 启动 keepalived
$ systemctl start keepalived.service
设置开机启动
$ systemctl enable keepalived.service
# 查看启动状态
$ systemctl status keepalived.service
```
启动后查看 master1 的网卡信息
```bash
ip a s ens33
```
## 4. 部署 haproxy

### 4.1 安装

```bash
yum install -y haproxy
```
### 4.2 配置

两台 master 节点的配置均相同，配置中声明了后端代理的两个 master 节点服务器，指定了 haproxy 运行的端口为 16443 等，因此 16443 端口为集群的入口

```properties
cat > /etc/haproxy/haproxy.cfg << EOF
#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    # to have these messages end up in /var/log/haproxy.log you will
    # need to:
    # 1) configure syslog to accept network log events. This is done
    # by adding the '-r' option to the SYSLOGD_OPTIONS in
    # /etc/sysconfig/syslog
    # 2) configure local2 events to go to the /var/log/haproxy.log
    # file. A line like the following can be added to
    # /etc/sysconfig/syslog
    #
    # local2.* /var/log/haproxy.log
    #
    log 127.0.0.1 local2
    chroot /var/lib/haproxy
    pidfile /var/run/haproxy.pid
    maxconn 4000
    user haproxy
    group haproxy
    daemon
    # turn on stats unix socket
    stats socket /var/lib/haproxy/stats
#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
    mode http
    log global
    option httplog
    option dontlognull
    option http-server-close
    option forwardfor except 127.0.0.0/8
    option redispatch
    retries 3
    timeout http-request 10s
    timeout queue 1m
    timeout connect 10s
    timeout client 1m
    timeout server 1m
    timeout http-keep-alive 10s
    timeout check 10s
    maxconn 3000
#---------------------------------------------------------------------
# kubernetes apiserver frontend which proxys to the backends
#---------------------------------------------------------------------
frontend kubernetes-apiserver
    mode tcp
    bind *:16443
    option tcplog
    default_backend kubernetes-apiserver
#---------------------------------------------------------------------
# round robin balancing between the various backends
#---------------------------------------------------------------------
backend kubernetes-apiserver
    mode tcp
    balance roundrobin
    server master01.k8s.io 192.168.44.155:6443 check
    server master02.k8s.io 192.168.44.156:6443 check
#---------------------------------------------------------------------
# collection haproxy statistics message
#---------------------------------------------------------------------
listen stats
    bind *:1080
    stats auth admin:awesomePassword
    stats refresh 5s
    stats realm HAProxy\ Statistics
    stats uri /admin?stats
EOF
```
### 4.3 启动和检查

两台 master 都启动

```bash
# 设置开机启动
$ systemctl enable haproxy
# 开启 haproxy
$ systemctl start haproxy
# 查看启动状态
$ systemctl status haproxy
```
检查端口
```bash
netstat -lntup|grep haproxy
```
## 5. 所有节点安装 Docker/kubeadm/kubelet

Kubernetes 默认 CRI（容器运行时）为 Docker，因此先安装 Docker。

### 5.1 安装 Docker

```bash
$ wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
$ yum -y install docker-ce-18.06.1.ce-3.el7
$ systemctl enable docker && systemctl start docker
$ docker --version
Docker version 18.06.1-ce, build e68fc7a 
$ cat > /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
}
EOF
```
### 5.2 添加阿里云 YUM 软件源

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
### 5.3 安装 kubeadm，kubelet 和 kubectl

由于版本更新频繁，这里指定版本号部署：

```bash
$ yum install -y kubelet-1.16.3 kubeadm-1.16.3 kubectl-1.16.3
$ systemctl enable kubelet
```
## 6. 部署 Kubernetes Master

### 6.1 创建 kubeadm 配置文件

在具有 vip 的 master 上操作，这里为 master1

```properties
$ mkdir /usr/local/kubernetes/manifests -p
$ cd /usr/local/kubernetes/manifests/
$ vi kubeadm-config.yaml
apiServer:
  certSANs:
    - master1
    - master2
    - master.k8s.io
    - 192.168.44.158
    - 192.168.44.155
    - 192.168.44.156
    - 127.0.0.1
  extraArgs:
    authorization-mode: Node,RBAC
    timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta1
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controlPlaneEndpoint: "master.k8s.io:16443"
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
kubernetesVersion: v1.16.3
networking:
  dnsDomain: cluster.local
  podSubnet: 10.244.0.0/16
  serviceSubnet: 10.1.0.0/16
scheduler: {}
```
### 6.2 在 master1 节点执行

```bash
$ kubeadm init --config kubeadm-config.yaml
```
按照提示配置环境变量，使用 kubectl 工具：
```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
$ kubectl get nodes
$ kubectl get pods -n kube-system
```
**按照提示保存以下内容，一会要使用：**
```bash
kubeadm join master.k8s.io:16443 --token jv5z7n.3y1zi95p952y9p65 \
--discovery-token-ca-cert-hash sha256:403bca185c2f3a4791685013499e7ce58f9848e2213e27194b75a2e3293d8812 \
--control-plane
```
查看集群状态
```bash
kubectl get cs
kubectl get pods -n kube-system
```
## 7.安装集群网络

从官方地址获取到 flannel 的 yaml，在 master1 上执行

```bash
mkdir flannel
cd flannel
wget -c https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
安装 flannel 网络
```bash
kubectl apply -f kube-flannel.yml
```
检查
```bash
kubectl get pods -n kube-system
```
## 8、master2 节点加入集群

### 8.1 复制密钥及相关文件

从 master1 复制密钥及相关文件到 master2

```bash
# ssh root@192.168.44.156 mkdir -p /etc/kubernetes/pki/etcd
# scp /etc/kubernetes/admin.conf root@192.168.44.156:/etc/kubernetes

# scp /etc/kubernetes/pki/{ca.*,sa.*,front-proxy-ca.*} root@192.168.44.156:/etc/kubernetes/pki

# scp /etc/kubernetes/pki/etcd/ca.* root@192.168.44.156:/etc/kubernetes/pki/etcd
```
### 8.2 master2 加入集群

执行在 master1 上 init 后输出的 join 命令,需要带上参数`--control-plane`表示把 master 控制节点加入集群

```bash
kubeadm join master.k8s.io:16443 --token ckf7bs.30576l0okocepg8b --discovery-token-ca-cert-hash sha256:19afac8b11182f61073e254fb57b9f19ab4d798b70501036fc69ebef46094aba --control-plane
```
检查状态
```bash
kubectl get node
kubectl get pods --all-namespaces
```
## 5. 加入 Kubernetes Node

在 node1 上执行

向集群添加新节点，执行在 kubeadm init 输出的 kubeadm join 命令：

```bash
kubeadm join master.k8s.io:16443 --token ckf7bs.30576l0okocepg8b --discovery-token-ca-cert-hash sha256:19afac8b11182f61073e254fb57b9f19ab4d798b70501036fc69ebef46094aba
```
**集群网络重新安装，因为添加了新的 node 节点**
检查状态

```bash
kubectl get node
kubectl get pods --all-namespaces
```
## 7. 测试 kubernetes 集群

在 Kubernetes 集群中创建一个 pod，验证是否正常运行：

```bash
$ kubectl create deployment nginx --image=nginx
$ kubectl expose deployment nginx --port=80 --type=NodePort
$ kubectl get pod,svc
```
访问地址：http://NodeIP:Port




