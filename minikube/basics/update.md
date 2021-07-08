# 升级应用版本
kubernetes 能很方便的对应用进行升级，比如现在有这样一个 service
```
$ kubectl get services/kubernetes-bootcamp
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           69m
```

其 Image 版本是 v1
```
$ kubectl describe deployment/kubernetes-bootcamp|grep Image
Image:        gcr.io/google-samples/kubernetes-bootcamp:v1
```

如果想将版本升级到 v2，可以使用以下命令
```
$ kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
deployment.apps/kubernetes-bootcamp image updated
```

修改完 deployment 的版本后，使用 get pods 查看当前 pod 状态
```
$ kubectl get pods
NAME                                   READY   STATUS              RESTARTS   AGE
kubernetes-bootcamp-57978f5f5d-59zs4   1/1     Terminating         0          79s
kubernetes-bootcamp-57978f5f5d-8mtd9   1/1     Terminating         0          27s
kubernetes-bootcamp-57978f5f5d-vkdff   1/1     Terminating         0          27s
kubernetes-bootcamp-57978f5f5d-zl66z   1/1     Terminating         0          27s
kubernetes-bootcamp-769746fd4-698mk    0/1     ContainerCreating   0          1s
kubernetes-bootcamp-769746fd4-c7ccq    1/1     Running             0          2s
kubernetes-bootcamp-769746fd4-df45l    1/1     Running             0          3s
kubernetes-bootcamp-769746fd4-dgqzw    1/1     Running             0          3s
```

可以看到，kubernetes 已经在对 kubernetes-bootcamp 进行滚动升级了

过一会再次查看 pod 的状态，发现此时只有 4 个 pod，且状态均为 running
```
$ kubectl get pods
NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-769746fd4-698mk   1/1     Running   0          107s
kubernetes-bootcamp-769746fd4-c7ccq   1/1     Running   0          108s
kubernetes-bootcamp-769746fd4-df45l   1/1     Running   0          109s
kubernetes-bootcamp-769746fd4-dgqzw   1/1     Running   0          109s
```
再次查看 deployment 镜像版本
```
$ kubectl describe deployment | grep Image
Image:        jocatalin/kubernetes-bootcamp:v2
```
此时版本已经更新为 v2 最新版本了


# 回滚
当我们尝试将 pod 版本升级到一个不存在的版本时，比如升级到 v10
```
$ kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10
deployment.apps/kubernetes-bootcamp image updated
```

查看 pods 状态，会发现 v10 版本的 pod 的状态最终会变成 ImagePullBackOff
```
$ kubectl get pods
NAME                                  READY   STATUS             RESTARTS   AGE
kubernetes-bootcamp-597654dbd-dmmc8   0/1     ImagePullBackOff   0          45s
kubernetes-bootcamp-597654dbd-nfjbk   0/1     ImagePullBackOff   0          45s
kubernetes-bootcamp-769746fd4-c7ccq   1/1     Running            0          5m1s
kubernetes-bootcamp-769746fd4-df45l   1/1     Running            0          5m2s
kubernetes-bootcamp-769746fd4-dgqzw   1/1     Running            0          5m2s
```

此时可以使用 rollout 命令将 deployment 的版本回滚到上个可用版本
```
$ kubectl rollout undo deployments/kubernetes-bootcamp
deployment.apps/kubernetes-bootcamp rolled back
```

```
$ kubectl get pods
NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-769746fd4-bt2nz   1/1     Running   0          33s
kubernetes-bootcamp-769746fd4-c7ccq   1/1     Running   0          10m
kubernetes-bootcamp-769746fd4-df45l   1/1     Running   0          10m
kubernetes-bootcamp-769746fd4-dgqzw   1/1     Running   0          10m

$ kubectl describe deployment/kubernetes-bootcamp | grep Image
Image:        jocatalin/kubernetes-bootcamp:v2
```

