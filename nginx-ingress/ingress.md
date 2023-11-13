Kubernetes暴露服务的方式目前只有三种：LoadBlancer Service、NodePort Service、Ingress;这一片主要聊聊Ingress。

Ingress
其实就是一个代理，可以根据配置转发请求到指定的服务上，用来分割测试个生产环境的“网关”。

Ingress公开从集群外部到集群内服务的 HTTP 和 HTTPS 路由。 流量路由由 Ingress 资源上定义的规则控制。

Ingress介绍
通俗来讲，ingress和之前提到的Service、Deployment，也是一个k8s的资源类型，ingress用于实现用域名的方式访问k8s内部应用。

Ingress为Kubernetes集群中的服务提供了入口，可以提供负载均衡、SSL终止和基于名称的虚拟主机，在生产环境中常用的Ingress有Treafik、Nginx、HAProxy、Istio等。

在Kubernetesv 1.1版中添加的Ingress用于从集群外部到集群内部Service的HTTP和HTTPS路由，流量从Internet到Ingress再到Services最后到Pod上，通常情况下，Ingress部署在所有的Node节点上。

Ingress可以配置提供服务外部访问的URL、负载均衡、终止SSL，并提供基于域名的虚拟主机。但Ingress不会暴露任意端口或协议。


Ingress资源作用
前面我们学习了k8s的一些强大的功能，比如k8s集群的副本控制能力，Pod随时可能从一个节点上被驱逐到另一个节点上，或者直接销毁再来一个新的。那么然而伴随着Pod的销毁和重生，Pod的IP等信息不断地在改变，生产和测试之间环境不就错乱了吗，所以此时使用K8S提供的Service机制可以解决这一问题，Service通过标签选定指定的Pod作为后端服务，并监听这些Pod的变化。

在对外暴露服务时，使用Service的NodePort是一个方法，那么问题来了，怎么去管理这些service端口呢？

Ingress首当其中。

问题1-如何管理端口

当需要对外暴露的服务量比较多的时候，端口管理的问题变会暴露出来。

此时的一个处理方案是使用一个代理服务（例如nginx）根据请求信息将请求转发到不同的服务器上。

问题2-如何管理转发配置

每当有新服务加入，都需要对该服务的配置进行修改、升级，在服务数量逐渐变多后，该配置项目会变得越来越大，手工修改的风险也会逐渐增高。

那么需要一个工具来简化这一过程，希望可以通过简单的配置动态生成代理中复杂的配置，最好还可以顺手重新加载配置文件。

Ingress的工作方式
Ingress 工作原理
ingress-controller通过和 kubernetes APIServer 交互，动态的去感知集群中ingress规则变化，
然后读取它，按照自定义的规则，规则就是写明了哪个域名对应哪个service，生成一段nginx配置，
再写到nginx-ingress-controller的pod里，这个ingress-controller的pod里运行着一个Nginx服务，控制器会把生成的 nginx配置写入 /etc/nginx.conf文件中，
然后reload一下使配置生效。以此达到域名区分配置和动态更新的作用。
在使用普通的Service时，集群中每个节点的kube-proxy在监听到Service和Endpoints的变化时，会动态的修改相关的iptables的转发规则。 客户端在访问时通过iptables设置的规则进行路由转发达到访问服务的目的。

Ingress安装与使用
 
安装
kubectl apply -f deploy.yaml
查看
kubectl get pod,svc -n ingress-nginx

应用配置
kubectl apply -f text.yaml
查看应用
kubectl get svc

 curl http://cluster-ip:port
配置ingress-nginx
kubectl apply -f guize.yaml

curl http://hello.jiangcb.com








