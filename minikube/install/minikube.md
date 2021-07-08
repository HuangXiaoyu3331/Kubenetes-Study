# 环境最低要求：
* 2 CPU
* 2GB 内存
* 20G 磁盘
* 连接 internet
* 安装有容器管理程序/虚拟机 如：Docker, Hyperkit, Hyper-V, KVM, Parallels, Podman, VirtualBox, or VMWare（这里使用docker）

# 我的环境：
* centos7 虚拟机
* 6.5G 内存
* 2CPU
* 20G
* docker

# docker 安装
1. 卸载旧版本 docker
```
$ sudo yum remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-engine
```

2. 配置docker仓库
首次安装docker，需要先配置docker仓库，安装步骤如下：
* 先安装 yum-utils（yum-utils 提供了yum-config-manager用于设置仓库）
```
$ sudo yum install -y yum-utils 
```
* 设置docker仓库
docker 仓库可以使用官方源地址
```
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

3. 安装 docker-engine
```
$ sudo yum install docker-ce docker-ce-cli containerd.io
```
> 如果提示是否检索 gpg 密钥，请选择是

4. 启动 docker
```
$ sudo systemctl start docker
```

> 更多信息，可以阅读 docker 官网：https://docs.docker.com/engine/install/centos/

# minikube 安装
1. 安装命令：
```
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
$ sudo rpm -Uvh minikube-latest.x86_64.rpm
```
> 如果安装包下载慢，建议开启科学上网

2. 启动minikube
```
$ minikube start
```
注意1：minikube 不能使用 root 运行，我这里使用 root 运行报错
```
[root@stone huangxy]# minikube start
ð  Centos 7.9.2009 上的 minikube v1.22.0
✨  自动选择 docker 驱动。其他选项：none, ssh
ð  The "docker" driver should not be used with root privileges.
ð¡  If you are running minikube within a VM, consider using --driver=none:
ð    https://minikube.sigs.k8s.io/docs/reference/drivers/none/

❌  Exiting due to DRV_AS_ROOT: The "docker" driver should not be used with root privileges.
```

使用非 root 用户运行的话，由于 minikube 需要使用 docker 下载镜像，所以该用户还需要有 docker 权限(前面安装docker的时候，没有将当前用户加入docker用户组中，还需要将docker加入到用户组)，详情参考下面链接
```
https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue
https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user

```

注意2：如果 minikube start 报下面的错误
```
ð  Centos 7.9.2009 上的 minikube v1.22.0
✨  根据现有的配置文件使用 docker 驱动程序
ð  Starting control plane node minikube in cluster minikube
ð  Pulling base image ...
ð  Updating the running docker "minikube" container ...
❗  This container is having trouble accessing https://k8s.gcr.io
ð¡  To pull new external images, you may need to configure a proxy: https://minikube.sigs.k8s.io/docs/reference/networking/proxy/
ð³  正在 Docker 20.10.7 中准备 Kubernetes v1.21.2…
ð  Verifying Kubernetes components...
    ▪ Using image kubernetesui/metrics-scraper:v1.0.4
    ▪ Using image kubernetesui/dashboard:v2.1.0
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
ð  Enabled addons: storage-provisioner, default-storageclass, dashboard
ð¡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
ð  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```
是因为被墙导致的，可以参考https://minikube.sigs.k8s.io/docs/handbook/vpn_and_proxy/设置代理，我设置完代理后的输出信息如下
```
[huangxy@stone ~]$ minikube start
ð  Centos 7.9.2009 上的 minikube v1.22.0
✨  自动选择 docker 驱动
ð  Starting control plane node minikube in cluster minikube
ð  Pulling base image ...
    > gcr.io/k8s-minikube/kicbase...: 361.09 MiB / 361.09 MiB  100.00% 4.95 MiB
❗  minikube was unable to download gcr.io/k8s-minikube/kicbase:v0.0.25, but successfully downloaded kicbase/stable:v0.0.25 as a fallback image
ð¥  Creating docker container (CPUs=2, Memory=2200MB) ...
ð  找到的网络选项：
    ▪ HTTP_PROXY=http://192.168.0.105:8118
    ▪ HTTPS_PROXY=http://192.168.0.105:8118
    ▪ NO_PROXY=localhost,127.0.0.1,10.96.0.0/12,192.168.99.0/24,192.168.39.0/24,192.168.49.2
    ▪ http_proxy=http://192.168.0.105:8118
    ▪ https_proxy=http://192.168.0.105:8118
ð³  正在 Docker 20.10.7 中准备 Kubernetes v1.21.2…
    ▪ env HTTP_PROXY=http://192.168.0.105:8118
    ▪ env HTTPS_PROXY=http://192.168.0.105:8118
    ▪ env NO_PROXY=localhost,127.0.0.1,10.96.0.0/12,192.168.99.0/24,192.168.39.0/24,192.168.49.2
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
ð  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
ð  Enabled addons: default-storageclass, storage-provisioner
ð¡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
ð  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

使用阿里云镜像启动
```
$ minikube start --image-mirror-country=cn --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers
```

# kubectl 设置
如果本机没安装 kubectl，执行 kubectl 命令会报错，minikube 内置了 kubectl 命令，可以使用`minikube kubectl -- <kubectl command>` 执行 kubectl 命令，如获取pods
```
$ minikube kubectl -- get pods
```
为方便使用，可以通过增加别名/软链接的方式使用 minikube kubectl
```
$ alias kubectl="minikube kubectl --"
或
$ ln -s $(which minikube) /usr/local/bin/kubectl
```
这样，就能像使用普通的 kubectl 一样使用 minikube kubectl 了，如：
```
$ kubectl get pods
```

> 参考 minikube 官网：https://minikube.sigs.k8s.io/docs/start/