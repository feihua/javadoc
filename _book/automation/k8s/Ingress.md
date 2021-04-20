# Ingress暴露应用

# 1.**NodePort**

NodePort服务是让外部请求直接访问服务的最原始方式，NodePort是在所有的节点（虚拟机）上开放指定的端口，所有发送到这个端口的请求都会直接转发到服务中的pod里；

NodePort服务的YAML文件如下：

```yaml
apiVersion: v1
kind: Service
metadata:  
 name: my-nodeport-service
selector:   
 app: my-appspec:
 type: NodePort
 ports:  
 - name: http
   port: 80
   targetPort: 80
   nodePort: 30008
   protocol: TCP
```

这种方式有一个“nodePort"的端口，能在节点上指定开放哪个端口，如果没有指定端口，它会选择一个随机端口，大多数时候应该让Kubernetes随机选择端口；

**这种方式的不足：**

1. 一个端口只能供一个服务使用；

2. 只能使用30000–32767之间的端口；
3. 如果节点/虚拟机的IP地址发生变化，需要人工进行处理；


因此，在生产环境不推荐使用这种方式来直接发布服务，如果不要求运行的服务实时可用，或者用于演示或者临时运行一个应用可以用这种方式；

**三种端口说明**

```yaml
ports:  
 - name: http
   port: 80
   targetPort: 80
   nodePort: 30008
   protocol: TCP
```

**nodePort**

外部机器（在windows浏览器）可以访问的端口；

比如一个Web应用需要被其他用户访问，那么需要配置type=NodePort，而且配置nodePort=30001，那么其他机器就可以通过浏览器访问scheme://node:30001访问到该服务；

**targetPort**

容器的端口，与制作容器时暴露的端口一致（Dockerfile中EXPOSE），例如docker.io官方的nginx暴露的是80端口；

**port**

Kubernetes集群中的各个服务之间访问的端口，虽然mysql容器暴露了3306端口，但外部机器不能访问到mysql服务，因为他没有配置NodePort类型，该3306端口是集群内其他容器需要通过3306端口访问该服务；

```bash
kubectl expose deployment springboot-k8s --port=8080 --target-port=8080 --type=NodePort
```



# 2.LoadBalancer

LoadBlancer可以暴露服务，这种方式需要向云平台申请负载均衡器，目前很多云平台都支持，但是这种方式深度耦合了云平台；（相当于是购买服务）

从外部的访问通过负载均衡器LoadBlancer转发到后端的Pod，具体如何实现要看云提供商；



# 3.**Ingress**

Ingress 英文翻译为：入口、进入、进入权、进食，也就是入口，即外部请求进入k8s集群必经之口，我们看一张图：

![图片](https://uploader.shimo.im/f/KeoUDyUOI0VMyLqp.png!thumbnail?fileGuid=i2JiSs1tqMMuCw2F)

虽然k8s集群内部署的pod、service都有自己的IP，但是却无法提供外网访问，以前我们可以通过监听NodePort的方式暴露服务，但是这种方式并不灵活，生产环境也不建议使用；

Ingresss是k8s集群中的一个API资源对象，相当于一个集群网关，我们可以自定义路由规则来转发、管理、暴露服务(一组pod)，比较灵活，生产环境建议使用这种方式；

Ingress不是kubernetes内置的（安装好k8s之后，并没有安装ingress），ingress需要单独安装，而且有多种类型Google Cloud Load Balancer，[Nginx](https://www.baidu.com/s?wd=Nginx&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd&fileGuid=i2JiSs1tqMMuCw2F)，Contour，Istio等等，我们这里选择官方维护的Ingress Nginx；

## 3.1**使用Ingress Nginx的步骤：**

1. 部署Ingress Nginx；
2. 配置Ingress Nginx规则；
## 3.2采用Ingress暴露容器化应用（Nginx）

1. 部署一个容器化应用（pod），比如Nginx、SpringBoot程序；

```bash
kubectl create deployment nginx --image=nginx
```



2. 暴露该服务；

```bash
kubectl expose deployment nginx --port=80--target-port=80--type=NodePort
```



3. 部署Ingress Nginx

[https://github.com/kubernetes/ingress-nginx](https://github.com/kubernetes/ingress-nginx?fileGuid=i2JiSs1tqMMuCw2F)

ingress-nginx是使用NGINX作为反向代理和负载均衡器的Kubernetes的Ingress控制器；

```
kubectl apply -f[https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/baremetal/deploy.yaml](https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/baremetal/deploy.yaml?fileGuid=i2JiSs1tqMMuCw2F)
```

332行修改成阿里云镜像：

阿里云镜像首页：[http://dev.aliyun.com/](http://dev.aliyun.com/?fileGuid=i2JiSs1tqMMuCw2F)

修改镜像地址为：

```
registry.cn-hangzhou.aliyuncs.com/google_containers/nginx-ingress-controller:0.33.0
```

添加一个配置项：

![图片](https://uploader.shimo.im/f/GaORykUnwSnrZVBJ.png!thumbnail?fileGuid=i2JiSs1tqMMuCw2F)

应用：

```bash
kubectl apply -f deploy.yaml
```

![图片](https://uploader.shimo.im/f/Pw0QB4mpJACzHcl9.png!thumbnail?fileGuid=i2JiSs1tqMMuCw2F)

4. 查看Ingress的状态

```bash
kubectl get service -n ingress-nginx

kubectl get deploy -n ingress-nginx

kubectl get pods -n ingress-nginx
```

![图片](https://uploader.shimo.im/f/txtAJGoXz4lMm1Fa.png!thumbnail?fileGuid=i2JiSs1tqMMuCw2F)

5. 创建Ingress规则

![图片](https://uploader.shimo.im/f/KeoUDyUOI0VMyLqp.png!thumbnail?fileGuid=i2JiSs1tqMMuCw2F)

**kubectl apply -f ingress-nginx-rule.yaml**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: k8s-ingress
spec:
  rules:
  - host: www.abc.com
    http:
      paths:
      - pathType: Prefix
        path: /
        backend:
          service:
            name: nginx
            port: 
              number: 80
```

报如下错误：

![图片](https://uploader.shimo.im/f/QFU4QYz27d9aK0QX.png!thumbnail?fileGuid=i2JiSs1tqMMuCw2F)

解决：

```
kubectl delete -A ValidatingWebhookConfiguration ingress-nginx-admission
```

然后再次执行：

```bash
kubectl apply -f ingress-nginx-rule.yaml
```

检查一下：

kubectl get ing(ress)  --查规则

## kubernetes部署Spring Cloud微服务

1. 项目本身打成jar包或者war包；
2. 制作项目镜像（写Dockerfile文件）；
3. 用k8s部署镜像（命令方式、yaml方式）；
4. 对外暴露服务；


生成镜像：

```bash
docker build -t spring-cloud-alibaba-consumer -f Dockerfile-consumer .

docker build -t spring-cloud-alibaba-provider -f Dockerfile-provider .

docker build -t spring-cloud-alibaba-gateway -f Dockerfile-gateway .
```

部署提供者：

```
kubectl create deployment spring-cloud-alibaba-provider --image=spring-cloud-alibaba-provider --dry-run -o yaml > provider.yaml
```

```bash
kubectl apply -f provider.yaml
```

部署消费者：

```bash
kubectl create deployment spring-cloud-alibaba-consumer --image=spring-cloud-alibaba-consumer --dry-run -o yaml > consumer.yaml
```

```bash
kubectl apply -f consumer.yaml
```

```bash
kubectl expose deployment spring-cloud-alibaba-consumer --port=9090 --target-port=9090 --type=NodePort
```

部署网关：

```bash
kubectl create deployment spring-cloud-alibaba-gateway --image=spring-cloud-alibaba-gateway --dry-run -o yaml > gateway.yaml
```

```bash
kubectl apply -f gateway.yaml
```

```bash
kubectl expose deployment spring-cloud-alibaba-gateway --port=80 --target-port=80 --type=NodePort
```

在网关上面部署ingress，统一入口；

按照上面讲解ingress暴露nginx的步骤进行就可以；

```bash
kubectl get service -n ingress-nginx

kubectl get deploy -n ingress-nginx

kubectl get pods -n ingress-nginx
```

注意：deploy.yaml文件里面镜像从本地拉取；

containers:

- image: 38-springboot-k8s-1.0.0-jar

name: 38-springboot-k8s-1-0-0-jar-8ntrx

imagePullPolicy: Never

把镜像拉取策略改为Never；

看pod详情：

kubectl describe pods spring-cloud-alibaba-consumer-5d557f4765-d27d9

看pod运行日志：

kubectl logs -f spring-cloud-alibaba-consumer-8697896544-g6rsf



