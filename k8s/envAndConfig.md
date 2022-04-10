## env、envFrom 和 configMap、secret
k8s 的 configMap 和 secret 可以用来保存一些动态、共享以及加密的数据，比如 redis 的密码、一些 token 等等。
这些变量一般以 deploment.spec.template.spec.containers 下的某一个容的 envFrom 和 env，通过环境变量的形式进行注入