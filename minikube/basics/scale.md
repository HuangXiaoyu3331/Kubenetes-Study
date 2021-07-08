# 对 deployment 进行扩容
可以使用`kubectl scale` 很方便的对 deployment 进行扩容

比如我们现在有一个 deployment，详情如下
```
$ kubectl get deployment/kubernetes-bootcamp
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           67m
```
可以看到，现在的 deployment 只有一个副本，使用`kubectl scale` 将其扩容至 4 个副本

```
$ kubectl scale deployments/kubernetes-bootcamp --replicas=4
deployment.apps/kubernetes-bootcamp scaled

$ kubectl get deployments/kubernetes-bootcamp
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           69m
```

此时，deployment 已经成功扩容至 4 个副本了