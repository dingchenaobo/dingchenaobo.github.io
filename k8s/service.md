## k8s - Service

因为 pod 不是永久型资源，是可以进行扩容、缩容、部署等等行为的，而这些行为都会伴随 pod 的创建和销毁，导致容器所在的 ip 发生变化（并不是 pod 本身的 ip 会发生变化），所以和 pod 的通讯不能以 ip 来直接进行通讯，k8s 提供了 service 这种方式。

k8s 的 service 有两种：
- clister-ip：用于 pod 之间通讯；
- node-port：用于 pod 和集群外部通讯；

但是 node-port 的模式并不很推荐，这种模式是让 pod 直接对接外部流量，可以使用 k8s 的 ingress 来对接外部流量，让 ingress 统一做流量向 pod 的分发。

这个功能由 k8s 控制面的 api-server 实现。