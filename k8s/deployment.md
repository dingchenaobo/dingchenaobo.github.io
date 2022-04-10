## deployment

### 介绍
属于 k8s 控制器的一种
> 一个 Deployment 为 [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) 和 [ReplicaSets](https://kubernetes.io/zh/docs/concepts/workloads/controllers/replicaset/) 提供声明式的更新能力。

- Pod ：一组（或一个）正在正在运行的容器，变相理解为进程组，或者一个软件、一个集合、一个虚拟机。该字段以 sepc.template 字段控制；
- ReplicaSet：维持 Pod 的副本数量，变相立即为由他来维护有 deployment 需要创建几个 pod。该字段在 deployment 的 yml 文件中以 spec.replicas 字段控制；

### 一个简单的 deployment.yml 文件
```yaml
apiVersion: apps/v1           # 该字段和 k8s 的版本有关系，可以理解为 html 的 doctype
kind: Deployment							# 当前文件描述的是一个 Deployment
metadata:											# 元数据，也就是当前对象的一些基本信息或描述
  name: nginx-deployment			# 创建的 deployment 对象名
  labels:											# deployment 对象的标签
    app: nginx
spec:													# 规范，或者规则
  replicas: 3									# 创建 3 个 pod 副本
  selector:										# 定义如何查找 pods
    matchLabels:							#	按标签匹配
      app: nginx							#	标签为 app: nginx 的 pod
  template:										# 对于 pod 的定义
    metadata:
      labels:									# pod 的标签
        app: nginx
    spec:
      containers:							# 容器列表
      - name: nginx						# 容器名
        image: nginx:1.14.2		# 镜像名
        ports:								# 端口映射
        - containerPort: 80
```

可以看出：
- yml 文件其实和 json 表达的意思是一样的，只是序列化和反序列化的方式不同而已；
- deployment 其实可以看作一个对象，他的 yml 文件为该对象的初始化属性、参数；
- kind 一般标明这个对象是谁；
- metadata、spec 这种为通用信息，基本每个对象都会有；
- 关于容器的比较重要的信息一般在 .spec.template 对象中：
  - .spec.template.metadata.labels 为容器打标签；
  - .spec.template.spec.containers 对象声明容器；​若有收获，就点个赞吧