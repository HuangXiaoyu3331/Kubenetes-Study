# 创建一个 service
```
$ kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
service/kubernetes-bootcamp exposed
```

# 查看 services
```
$ kubectl get services
NAME                  TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes            ClusterIP   10.96.0.1      <none>        443/TCP          57m
kubernetes-bootcamp   NodePort    10.98.201.76   <none>        8080:31716/TCP   35s
```
> 注意：minikube 不支持 Loadbalance
可以看到这里有两个service，kubernetes 跟 kubernetes-bootcamp，其中 kubernetes 是 minikebu 启动的时候默认会创建的，kubernetes-bootcamp 则是我们创建的 service

其中 --type="NodePort" --port 8080 表示此 service 开启了 NodePort 方式的外网访问模式，当外网访问 NodePort 的端口时，会将流量转发到 pod 上的 8080 端口上

可以通过以下命令查看 NodePort 端口
```
$ kubectl describe service kubernetes-bootcamp
Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   app=kubernetes-bootcamp
Annotations:              <none>
Selector:                 app=kubernetes-bootcamp
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.107.120.211
IPs:                      10.107.120.211
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31436/TCP
Endpoints:                172.17.0.3:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```
可以看到 kubernetes-bootcamp 的 NodePort 端口是 31436，使用 curl 访问该端口试试

```
$ curl $(minikube ip):31436
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-57978f5f5d-vbnsk | v=1
```
可以看到，我们已经成功访问到容器内部了
