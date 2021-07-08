# 使用命令行创建一个 deployment
```
$ kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
deployment.apps/kubernetes-bootcamp created
```

# 创建成功后可以使用 kubectl get 命令查看 deployments 列表
```
$ kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           20s
```

# 使用 kubectl get pods 查看 pods 状态
```
$ kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-57978f5f5d-vbnsk   1/1     Running   0          3m53s
```

# 与 pod 进行通讯
pods 运行在 Kubernetes 的内部网络中，默认情况下，kubernetes 集群中的其他 pod/service 可以互相通讯。但是当集群外部访问不了 pod，如果想访问，就需要把 pod 中要访问的端口开放出去，可以使用 service 将 pod 作为服务暴露出去，或使用 kubectl 创建一个 proxy，使用该代理，能让我们方便的访问集群中的 pod

这里先讲 proxy 的方式访问 pod，首先打开另外的窗口创建 proxy
```
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```
可以看到，代理默认监听的是 8001 端口，接着使用`curl`访问 8001 端口
```
$ curl http://127.0.0.1:8001/version
{
  "major": "1",
  "minor": "21",
  "gitVersion": "v1.21.2",
  "gitCommit": "092fbfbf53427de67cac1e9fa54aaa09a28371d7",
  "gitTreeState": "clean",
  "buildDate": "2021-06-16T12:53:14Z",
  "goVersion": "go1.16.5",
  "compiler": "gc",
  "platform": "linux/amd64"
}
```

访问刚刚创建的 pod
```
$ curl http://localhost:8001/api/v1/namespaces/default/pods/kubernetes-bootcamp-57978f5f5d-vbnsk
{
  "kind": "Pod",
  "apiVersion": "v1",
  "metadata": {
    "name": "kubernetes-bootcamp-57978f5f5d-vbnsk",
    "generateName": "kubernetes-bootcamp-57978f5f5d-",
    "namespace": "default",
    "uid": "3863c78f-5421-4cfd-81fb-8fc02fb7dc5f",
    "resourceVersion": "1436",
    "creationTimestamp": "2021-07-08T14:28:54Z",
    "labels": {
      "app": "kubernetes-bootcamp",
      "pod-template-hash": "57978f5f5d"
    },
    "ownerReferences": [
      {
        "apiVersion": "apps/v1",
        "kind": "ReplicaSet",
        "name": "kubernetes-bootcamp-57978f5f5d",
        "uid": "ee784d89-b926-4366-9d86-d13d6edb67b4",
        "controller": true,
        "blockOwnerDeletion": true
      }
    ],
    "managedFields": [
      {
        "manager": "kube-controller-manager",
        "operation": "Update",
        "apiVersion": "v1",
        "time": "2021-07-08T14:28:54Z",
        "fieldsType": "FieldsV1",
        "fieldsV1": {"f:metadata":{"f:generateName":{},"f:labels":{".":{},"f:app":{},"f:pod-template-hash":{}},"f:ownerReferences":{".":{},"k:{\"uid\":\"ee784d89-b926-4366-9d86-d13d6edb67b4\"}":{".":{},"f:apiVersion":{},"f:blockOwnerDeletion":{},"f:controller":{},"f:kind":{},"f:name":{},"f:uid":{}}}},"f:spec":{"f:containers":{"k:{\"name\":\"kubernetes-bootcamp\"}":{".":{},"f:image":{},"f:imagePullPolicy":{},"f:name":{},"f:resources":{},"f:terminationMessagePath":{},"f:terminationMessagePolicy":{}}},"f:dnsPolicy":{},"f:enableServiceLinks":{},"f:restartPolicy":{},"f:schedulerName":{},"f:securityContext":{},"f:terminationGracePeriodSeconds":{}}}
      },
      {
        "manager": "kubelet",
        "operation": "Update",
        "apiVersion": "v1",
        "time": "2021-07-08T14:29:14Z",
        "fieldsType": "FieldsV1",
        "fieldsV1": {"f:status":{"f:conditions":{"k:{\"type\":\"ContainersReady\"}":{".":{},"f:lastProbeTime":{},"f:lastTransitionTime":{},"f:status":{},"f:type":{}},"k:{\"type\":\"Initialized\"}":{".":{},"f:lastProbeTime":{},"f:lastTransitionTime":{},"f:status":{},"f:type":{}},"k:{\"type\":\"Ready\"}":{".":{},"f:lastProbeTime":{},"f:lastTransitionTime":{},"f:status":{},"f:type":{}}},"f:containerStatuses":{},"f:hostIP":{},"f:phase":{},"f:podIP":{},"f:podIPs":{".":{},"k:{\"ip\":\"172.17.0.3\"}":{".":{},"f:ip":{}}},"f:startTime":{}}}
      }
    ]
  },
  "spec": {
    "volumes": [
      {
        "name": "kube-api-access-zfsnv",
        "projected": {
          "sources": [
            {
              "serviceAccountToken": {
                "expirationSeconds": 3607,
                "path": "token"
              }
            },
            {
              "configMap": {
                "name": "kube-root-ca.crt",
                "items": [
                  {
                    "key": "ca.crt",
                    "path": "ca.crt"
                  }
                ]
              }
            },
            {
              "downwardAPI": {
                "items": [
                  {
                    "path": "namespace",
                    "fieldRef": {
                      "apiVersion": "v1",
                      "fieldPath": "metadata.namespace"
                    }
                  }
                ]
              }
            }
          ],
          "defaultMode": 420
        }
      }
    ],
    "containers": [
      {
        "name": "kubernetes-bootcamp",
        "image": "gcr.io/google-samples/kubernetes-bootcamp:v1",
        "resources": {

        },
        "volumeMounts": [
          {
            "name": "kube-api-access-zfsnv",
            "readOnly": true,
            "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount"
          }
        ],
        "terminationMessagePath": "/dev/termination-log",
        "terminationMessagePolicy": "File",
        "imagePullPolicy": "IfNotPresent"
      }
    ],
    "restartPolicy": "Always",
    "terminationGracePeriodSeconds": 30,
    "dnsPolicy": "ClusterFirst",
    "serviceAccountName": "default",
    "serviceAccount": "default",
    "nodeName": "minikube",
    "securityContext": {

    },
    "schedulerName": "default-scheduler",
    "tolerations": [
      {
        "key": "node.kubernetes.io/not-ready",
        "operator": "Exists",
        "effect": "NoExecute",
        "tolerationSeconds": 300
      },
      {
        "key": "node.kubernetes.io/unreachable",
        "operator": "Exists",
        "effect": "NoExecute",
        "tolerationSeconds": 300
      }
    ],
    "priority": 0,
    "enableServiceLinks": true,
    "preemptionPolicy": "PreemptLowerPriority"
  },
  "status": {
    "phase": "Running",
    "conditions": [
      {
        "type": "Initialized",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2021-07-08T14:28:54Z"
      },
      {
        "type": "Ready",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2021-07-08T14:29:14Z"
      },
      {
        "type": "ContainersReady",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2021-07-08T14:29:14Z"
      },
      {
        "type": "PodScheduled",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2021-07-08T14:28:54Z"
      }
    ],
    "hostIP": "192.168.49.2",
    "podIP": "172.17.0.3",
    "podIPs": [
      {
        "ip": "172.17.0.3"
      }
    ],
    "startTime": "2021-07-08T14:28:54Z",
    "containerStatuses": [
      {
        "name": "kubernetes-bootcamp",
        "state": {
          "running": {
            "startedAt": "2021-07-08T14:29:14Z"
          }
        },
        "lastState": {

        },
        "ready": true,
        "restartCount": 0,
        "image": "gcr.io/google-samples/kubernetes-bootcamp:v1",
        "imageID": "docker-pullable://gcr.io/google-samples/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af",
        "containerID": "docker://bceff6f777a1751074df83b14dbaec8faa32ac4eab7dc27eee30a5d260809d03",
        "started": true
      }
    ],
    "qosClass": "BestEffort"
  }
}[huangxy@stone ~]$ curl http://localhost:8001/api/v1/namespaces/default/pods/kubernetes-bootcamp-57978f5f5d-vbnk
{
  "kind": "Pod",
  "apiVersion": "v1",
  "metadata": {
    "name": "kubernetes-bootcamp-57978f5f5d-vbnsk",
    "generateName": "kubernetes-bootcamp-57978f5f5d-",
    "namespace": "default",
    "uid": "3863c78f-5421-4cfd-81fb-8fc02fb7dc5f",
    "resourceVersion": "1436",
    "creationTimestamp": "2021-07-08T14:28:54Z",
    "labels": {
      "app": "kubernetes-bootcamp",
      "pod-template-hash": "57978f5f5d"
    },
    "ownerReferences": [
      {
        "apiVersion": "apps/v1",
        "kind": "ReplicaSet",
        "name": "kubernetes-bootcamp-57978f5f5d",
        "uid": "ee784d89-b926-4366-9d86-d13d6edb67b4",
        "controller": true,
        "blockOwnerDeletion": true
      }
    ],
    "managedFields": [
      {
        "manager": "kube-controller-manager",
        "operation": "Update",
        "apiVersion": "v1",
        "time": "2021-07-08T14:28:54Z",
        "fieldsType": "FieldsV1",
        "fieldsV1": {"f:metadata":{"f:generateName":{},"f:labels":{".":{},"f:app":{},"f:pod-template-hash":{}},"f:ownerReferences":{".":{},"k:{\"uid\":\"ee784d89-b926-4366-9d86-d13d6edb67b4\"}":{".":{},"f:apiVersion":{},"f:blockOwnerDeletion":{},"f:controller":{},"f:kind":{},"f:name":{},"f:uid":{}}}},"f:spec":{"f:containers":{"k:{\"name\":\"kubernetes-bootcamp\"}":{".":{},"f:image":{},"f:imagePullPolicy":{},"f:name":{},"f:resources":{},"f:terminationMessagePath":{},"f:terminationMessagePolicy":{}}},"f:dnsPolicy":{},"f:enableServiceLinks":{},"f:restartPolicy":{},"f:schedulerName":{},"f:securityContext":{},"f:terminationGracePeriodSeconds":{}}}
      },
      {
        "manager": "kubelet",
        "operation": "Update",
        "apiVersion": "v1",
        "time": "2021-07-08T14:29:14Z",
        "fieldsType": "FieldsV1",
        "fieldsV1": {"f:status":{"f:conditions":{"k:{\"type\":\"ContainersReady\"}":{".":{},"f:lastProbeTime":{},"f:lastTransitionTime":{},"f:status":{},"f:type":{}},"k:{\"type\":\"Initialized\"}":{".":{},"f:lastProbeTime":{},"f:lastTransitionTime":{},"f:status":{},"f:type":{}},"k:{\"type\":\"Ready\"}":{".":{},"f:lastProbeTime":{},"f:lastTransitionTime":{},"f:status":{},"f:type":{}}},"f:containerStatuses":{},"f:hostIP":{},"f:phase":{},"f:podIP":{},"f:podIPs":{".":{},"k:{\"ip\":\"172.17.0.3\"}":{".":{},"f:ip":{}}},"f:startTime":{}}}
      }
    ]
  },
  "spec": {
    "volumes": [
      {
        "name": "kube-api-access-zfsnv",
        "projected": {
          "sources": [
            {
              "serviceAccountToken": {
                "expirationSeconds": 3607,
                "path": "token"
              }
            },
            {
              "configMap": {
                "name": "kube-root-ca.crt",
                "items": [
                  {
                    "key": "ca.crt",
                    "path": "ca.crt"
                  }
                ]
              }
            },
            {
              "downwardAPI": {
                "items": [
                  {
                    "path": "namespace",
                    "fieldRef": {
                      "apiVersion": "v1",
                      "fieldPath": "metadata.namespace"
                    }
                  }
                ]
              }
            }
          ],
          "defaultMode": 420
        }
      }
    ],
    "containers": [
      {
        "name": "kubernetes-bootcamp",
        "image": "gcr.io/google-samples/kubernetes-bootcamp:v1",
        "resources": {

        },
        "volumeMounts": [
          {
            "name": "kube-api-access-zfsnv",
            "readOnly": true,
            "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount"
          }
        ],
        "terminationMessagePath": "/dev/termination-log",
        "terminationMessagePolicy": "File",
        "imagePullPolicy": "IfNotPresent"
      }
    ],
    "restartPolicy": "Always",
    "terminationGracePeriodSeconds": 30,
    "dnsPolicy": "ClusterFirst",
    "serviceAccountName": "default",
    "serviceAccount": "default",
    "nodeName": "minikube",
    "securityContext": {

    },
    "schedulerName": "default-scheduler",
    "tolerations": [
      {
        "key": "node.kubernetes.io/not-ready",
        "operator": "Exists",
        "effect": "NoExecute",
        "tolerationSeconds": 300
      },
      {
        "key": "node.kubernetes.io/unreachable",
        "operator": "Exists",
        "effect": "NoExecute",
        "tolerationSeconds": 300
      }
    ],
    "priority": 0,
    "enableServiceLinks": true,
    "preemptionPolicy": "PreemptLowerPriority"
  },
  "status": {
    "phase": "Running",
    "conditions": [
      {
        "type": "Initialized",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2021-07-08T14:28:54Z"
      },
      {
        "type": "Ready",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2021-07-08T14:29:14Z"
      },
      {
        "type": "ContainersReady",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2021-07-08T14:29:14Z"
      },
      {
        "type": "PodScheduled",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2021-07-08T14:28:54Z"
      }
    ],
    "hostIP": "192.168.49.2",
    "podIP": "172.17.0.3",
    "podIPs": [
      {
        "ip": "172.17.0.3"
      }
    ],
    "startTime": "2021-07-08T14:28:54Z",
    "containerStatuses": [
      {
        "name": "kubernetes-bootcamp",
        "state": {
          "running": {
            "startedAt": "2021-07-08T14:29:14Z"
          }
        },
        "lastState": {

        },
        "ready": true,
        "restartCount": 0,
        "image": "gcr.io/google-samples/kubernetes-bootcamp:v1",
        "imageID": "docker-pullable://gcr.io/google-samples/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af",
        "containerID": "docker://bceff6f777a1751074df83b14dbaec8faa32ac4eab7dc27eee30a5d260809d03",
        "started": true
      }
    ],
    "qosClass": "BestEffort"
  }
}
```
