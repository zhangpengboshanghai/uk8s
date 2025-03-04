{{indexmenu_n>3}}
## 使用web kubectl

UK8S 在console中提供 web terminal，你可以通过web terminal 登录到集群内的Pod，并使用kubectl 操作和管理集群。

该Pod通过Deployment的方式启动，并通过特定的安全机制代理到UCloud控制台界面，如果你误删除了该Deployment，则无法使用console中的kubectl功能。

你可以使用下方的yaml文件重新启动一个Pod,yaml示例如下。

```
# ------------------- kubectl Deployment ------------------- #
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    k8s-app: uk8s-kubectl
  name: uk8s-kubectl
spec:
  replicas: 1
  selector:
    matchLabels:
      k8s-app: uk8s-kubectl
  template:
    metadata:
      labels:
        k8s-app: uk8s-kubectl
    spec:
      serviceAccountName: uk8s-kubectl
      containers:
        - image: uhub.service.ucloud.cn/ucloud/uk8s-kubectl:v1.13.5
          imagePullPolicy: IfNotPresent
          name: uk8s-kubectl

---
# ------------------- Service Account ------------------- #

apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: uk8s-kubectl
  name: uk8s-kubectl
  namespace: default

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: uk8s-kubectl-rolebind
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: uk8s-kubectl
  namespace: default
```

