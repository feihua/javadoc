## Kubernetes部署-容器化应用

**Docker应用-->在docker里面部署一个java程序（springboot）**

# 1.在Kubernetes集群中部署一个Nginx：

```bash
kubectl create deployment nginx --image=nginx

kubectl expose deployment nginx --port=80 --type=NodePort

kubectl get pod,svc
```



访问地址：http://NodeIP:Port 



# 2.在Kubernetes集群中部署一个Tomcat：

```bash
kubectl create deployment tomcat --image=tomcat

kubectl expose deployment tomcat --port=8080 --type=NodePort
```



访问地址：http://NodeIP:Port



# 3.K8s部署微服务（springboot程序）

1. 项目打包（jar、war）-->可以采用一些工具git、maven、jenkins

2. 制作Dockerfile文件，生成镜像；

   ```yaml
   FROM java:8
   VOLUME /tmp
   ADD appApi.jar app.jar
   RUN bash -c 'touch /app.jar'
   RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
   && echo 'Asia/Shanghai' >/etc/timezone
   EXPOSE 8080
   ENTRYPOINT ["java","-Xmx3000m","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
   ```

   

3. kubectl create deployment nginx --image= 你的镜像

4. 你的springboot就部署好了，是以docker容器的方式运行在pod里面的；

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: springboot-k8s
  name: springboot-k8s
spec:
  replicas: 1
  selector:
    matchLabels:
      app: springboot-k8s
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: springboot-k8s
    spec:
      containers:
      - image: 38-springboot-k8s-1.0.0-jar
        name: 38-springboot-k8s-1-0-0-jar-8ntrx
        imagePullPolicy: Never
        resources: {}
        
        args: 
           
status: {}
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: springboot-k8s
  name: springboot-k8s
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
    nodePort: 30008
  selector:
    app: springboot-k8s
  type: NodePort
status:
  loadBalancer: {}

```

