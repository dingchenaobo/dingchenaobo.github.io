## istio
下面的绝大多书功能，默认为安装 istio 后自动开启，不要手动修改 deployment 文件去做配置变更；

和 k8s 配合使用效果比较好，而是不是他必须绑定 k8s。

## 自动注入

在 deployment.yaml 文件中，通过 deployment.spec.template.metadata.annotations.sidecar.istio.io/inject 开启：

```yaml
kind: Deployment
apiVersion: apps/v1
spec:
  template:
    metadata:
      annotations:
        sidecar.istio.io/inject: true
```

## Sidecar

边车模式，其实我们的业务容器才是坐在斗篷里面的那个。
pod 启动的时候，pod 里面其实运行了两个容器，一个是代理容器，一个是应用容器。
所有的流量都会被代理容器拦截，也就是入和出应用的流量，会由代理容器过一道，可以理解为一个小型 nginx 服务的样子。
但是这个东西妙就妙在：
- 对应用无感，应用在运行、发布的时候对可以完全不知道主容器的存在；
- pod 级别的网关；
- 应用可以不需要操心应用间通讯的问题，什么 gRpc、dubbo 之类的东西可以不需要应用来配置了。也就是真正做到了应用只需要管业务，和业务无关的东西可以不写在应用里面；

## 服务发现和负载均衡
从 Pod 发起的请求，会由代理容器 envoy 首先向控制平面的 Pilot 组件发起请求，获取这次请求的对应 Pod 实例的 ip 列表；
获取 ip 列表的时候同时也会获取负载均衡策略，根据策略来选择对应的实例 ip；

## istio 组件 - Pilot
该组件存在控制平面内。pilot 内部包含了三个模块：
- Platform Adapter：注册中心适配器，对接不同的注册中心框架，对内做统一的封装和抽象，比如对接了 Eureka、Etcd 等；
- Abstract Model：使用 Platform Adapter 提供的封装接口；
- Envoy API：和 Pod 中的 Envoy 代理进行通讯，发送服务发现信息和流量控制规则给 Pod 的 Envoy 代理；

Pilot 更重要的是向数据平面下发规则，也就是进行流量控制，比如可以下发给某个 Pod_1 一个规则，让发起 header 中存在 version 为 1 的 http 请求，把这个请求给 Pod_v1，header 中存在 version 为 2 的 http 请求，把这个请求给 Pod_v2；

## istio 组件 - Envoy
该组件存在数据平面，也就是以容器运行时的形式存在于 Pod 内。
该容器包含两个进程：
- pilot agent：生成 envoy 配置文件和启动 envoy 进程，同时也作为 envoy 的守护进程，监控其运行状态，负责重启、异常上报等；
- envoy：
  - 拦截 Pod 的流量；
  - 从 Pilot 获取负载配置和服务发现配置 ；
  - 上报数据给控制平面的组件；

## istio 组件 - Ingress Gateway
k8s 已经有 gateway 了，istio 咋还有个 ingress gateway？ 不是很理解这两个网关有啥区别。