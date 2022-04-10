## 架构模式
纯 k8s 架构一般有两种：
- domain + slb + ingress + svc + pod 模式
- gateway + registry + pod 模式

但是如果加上服务网格就会更复杂一点