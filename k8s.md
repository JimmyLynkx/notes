## 架构组成

## POD

- 最小部署单元

- 包含一个或多个容器

- 一个pod中的容器共享命名空间

- pod是短暂的，（重启后pod中的容器ip会发生变化）

```shell
kubectl get pods

kubectl describe pod <pod-name>

kubectl logs <pod-name>

kubectl delete pod <pod-name>

kubectl exec -it <pod-name> -- /bin/sh
```

### POD基本分类

**pod 分为静态 pod 和普通的 pod**

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7820a8ca24a4381b295a575d641c535~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp" title="" alt="" width="392">

**静态 pod**

- 由 kubelet 进行管理， 不能通过 Api Server 管理
- 存在于特定 Node 上的 pod
- 无法 ReplicationController，Deployment，Daemonset 进行关联
- kubelet 无法对该 pod 进行健康检查

**普通 pod**

- 一旦创建，就会被放到 etcd 存储中
- 会被 k8s 中的 master 调度到某个 Node 上面并绑定，该 Node 上的 kubelet 会实例化成 docker 容器 运行起来
- k8s 会对 pod 做健康检查，若 pod 中的容器暂停或者异常，k8s 会将他们重启
- **若 pod 所在的 Node 宕机了，那么 k8s 会将 该 Node 的所有 pod 重新调度到别的节点上面**

### POD生命周期

| `Pending`（悬决）   | Pod 已被 Kubernetes 系统接受，但有一个或者多个容器尚未创建亦未运行。此阶段包括等待 Pod 被调度的时间和通过网络下载镜像的时间。 |
| --------------- | ------------------------------------------------------------------------- |
| `Running`（运行中）  | Pod 已经绑定到了某个节点，Pod 中所有的容器都已被创建。至少有一个容器仍在运行，或者正处于启动或重启状态。                  |
| `Succeeded`（成功） | Pod 中的所有容器都已成功终止，并且不会再重启。                                                 |
| `Failed`（失败）    | Pod 中的所有容器都已终止，并且至少有一个容器是因为失败终止。也就是说，容器以非 0 状态退出或者被系统终止。                  |
| `Unknown`（未知）   | 因为某些原因无法取得 Pod 的状态。这种情况通常是因为与 Pod 所在主机通信失败。                               |

说明：

当一个 Pod 被删除时，执行一些 kubectl 命令会展示这个 Pod 的状态为 `Terminating`（终止）。 这个 `Terminating` 状态并不是 Pod 阶段之一。 Pod 被赋予一个可以体面终止的期限，默认为 30 秒。 你可以使用 `--force` 参数来[强制终止 Pod](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/#pod-termination-forced)。

### POD重启策略

Pod 的 `spec` 中包含一个 `restartPolicy` 字段，其可能取值包括 Always、OnFailure 和 Never。默认值是 Always。

`restartPolicy` 应用于 Pod 中的[应用容器](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-app-container)和常规的 [Init 容器](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/init-containers/)。 [Sidecar 容器](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/sidecar-containers/)忽略 Pod 级别的 `restartPolicy` 字段：在 Kubernetes 中，Sidecar 被定义为 `initContainers` 内的一个条目，其容器级别的 `restartPolicy` 被设置为 `Always`。 对于因错误而退出的 Init 容器，如果 Pod 级别 `restartPolicy` 为 `OnFailure` 或 `Always`， 则 kubelet 会重新启动 Init 容器。

- `Always`：只要容器终止就自动重启容器。
- `OnFailure`：只有在容器错误退出（退出状态非零）时才重新启动容器。
- `Never`：不会自动重启已终止的容器。

当 kubelet 根据配置的重启策略处理容器重启时，仅适用于同一 Pod 内替换容器并在同一节点上运行的重启。当 Pod 中的容器退出时，`kubelet` 会以指数级回退延迟机制（10 秒、20 秒、40 秒......）重启容器， 上限为 300 秒（5 分钟）。一旦容器顺利执行了 10 分钟， kubelet 就会重置该容器的重启延迟计时器。 [Sidecar 容器和 Pod 生命周期](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/sidecar-containers/#sidecar-containers-and-pod-lifecycle)中解释了 `init containers` 在指定 `restartpolicy` 字段时的行为。

**当 Pod 中有一个容器时：**

1. **容器成功退出**：
   
   - **Always 重启策略**：
     - 状态仍然是 `Running`，因为容器会被自动重启。
   - **Never 重启策略**：
     - 状态会变成 `Succeeded`，因为容器不会被重启，且容器正常退出。
   - **OnFailure 重启策略**：
     - 状态会变成 `Succeeded`，因为容器正常退出（状态码为0），不会被重启。

2. **容器退出异常**：
   
   - **Always 重启策略**：
     - 状态仍然是 `Running`，因为容器会被自动重启。
   - **Never 重启策略**：
     - 状态会变成 `Failed`，因为容器不会被重启，且容器异常退出。
   - **OnFailure 重启策略**：
     - 状态仍然是 `Running`，因为容器会被重启，直到成功。

**当 Pod 中有两个或以上的容器时：**

1. **有一个容器退出异常**：
   
   - **Always 重启策略**：
     - 状态仍然是 `Running`，因为异常退出的容器会被自动重启。
   - **Never 重启策略**：
     - 状态仍然是 `Running`，因为其他容器仍在运行，Pod 的整体状态是以所有容器为基准的。
   - **OnFailure 重启策略**：
     - 状态仍然是 `Running`，因为异常退出的容器会被重启。

2. **容器资源被限制，容器被杀掉**：
   
   - **Always 重启策略**：
     - 状态仍然是 `Running`，因为被杀掉的容器会被自动重启。
   - **Never 重启策略**：
     - 状态会变成 `Failed`，因为容器不会被重启，且容器被杀掉。
   - **OnFailure 重启策略**：
     - 状态仍然是 `Running`，因为被杀掉的容器会被重启。

### 拉取策略&资源限制

![](C:\Users\Administrator\AppData\Roaming\marktext\images\2024-06-04-16-36-33-image.png)

### POD健康检查

<img title="" src="file:///C:/Users/Administrator/AppData/Roaming/marktext/images/2024-06-04-16-48-03-image.png" alt="" width="780" data-align="inline">

### POD调度

资源限制

节点选择器

节点亲和性

## Controller

```shell
kubectl create deployment web --image=nginx --dry-run -o yaml > web.yaml

kubectl apply -f web.yaml

kubectl expose deployment web --port=80 --type=NodePort --target-port=80 --name=web1 -o yaml > web1.yaml
```

Deployment描述了定义的pod的相关信息

Deployment 资源会根据其配置创建一个或多个 Pod，并确保这些 Pod 运行在集群中。Deployment 控制器会监控这些 Pod，并确保它们保持在期望的状态。例如，如果 Pod 崩溃或被删除，Deployment 控制器会自动创建新的 Pod 来替代它们。

## 技术文章链接

[最新、最全、最详细的 K8S 学习笔记总结（2021最新版）！建议收藏](https://segmentfault.com/a/1190000039844000)

[k8s-学习笔记总结（从入门到放弃的学习路线） 原创](https://blog.csdn.net/qq_21187515/article/details/112359593)

[霞的云原生之旅: Deploy Kubernetes](https://juejin.cn/post/7292041370893778983)

为什么要关闭swap分区？

1、基于其出发点，k8s希望将资源限制在一个严格，精确可控范围内:  

Kubernetes 云原生的实现目的是将运行实例紧密包装到尽可能接近 100％:  
所有的部署、运行环境应该与 CPU 以及内存限定在一个可控的空间内。所以如果调度程序发送一个 Pod 到某一台节点机器，它不应该使用 Swap。如果使用swap，则其实node的pod使用内存总和可能超过了node的内存，这样其实就达不到资源的严格限制和管理的目的  

2、为了性能和服务的稳定性  

若开启 Swap ，将会减慢速度。因此，关闭 Swap 也有一部分是为了性能考虑。  
原因是kubelet不是为了处理交换情况而设计的，Kubernetes团队不打算实现这一点，因为目标是pod应该适合主机的内存。打开这个之后，不稳定，pod可能使用内存也可以使用交换，这样对服务不稳定，而且无法保证pod申请的内存应该是真正使用的内存，  
还可能运行运行着，服务用的内存越来越多，导致已有服务可能部分内存不用的时候释放掉，再申请的时候是被分配到swap了，效率就变了，  
这样引发的问题很难定位，也很难确定，出现未定义行为

为什么要关闭selinux?

selinux，这个是用来加强安全性的一个组件，但非常容易出错且难以定位，一般上来装完系统就先给禁用了

 关闭 SELinux

所有节点关闭 SELinux

> 负载均衡机器必须要关闭,因为6443不是nginx的标准端口,会被selinux拦截, 防火墙也需要放行6443端口, 可以考虑直接关闭

SELINUX有三种状态:

1. permissive: 不阻止任何操作, 但会记录潜在的危险操作. 会打印警告，但不会强制执行安全策略
2. disabled: 完全关闭
3. enforcing: 启用SELinux安全保护状态

cat >> /etc/hosts << EOF
192.168.176.128 master1
192.168.176.131 worker1
192.168.176.130 worker2
EOF

为什么要配置这一项？

```shell
# 将桥接的IPv4流量传递到iptables的链
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
# 生效
sysctl --system 
```

```shell
#安装kubelet、kubeadm、kubectl，同时指定版本

yum install -y kubelet kubeadm kubectl

#设置开机启动

systemctl enable kubelet

kubectl version
Client Version: v1.28.2
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3

kubeadm init --apiserver-advertise-address=192.168.176.128 --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.25.5 --service-cidr=10.96.0.0/12 --pod-network-cidr=10.244.0.0/16

WARNING Firewalld]: firewalld is active, please ensure ports [6443 10250] are open or your cluster may not function correctly
[WARNING Swap]: swap is enabled; production deployments should disable swap unless testing the NodeSwap feature gate of the kubelet
error execution phase preflight: [preflight] Some fatal errors occurred:
[ERROR CRI]: container runtime is not running: output: time="2024-06-05T23:37:27-07:00" level=fatal msg="validate service connection: CRI v1 runtime API is not implemented for endpoint "unix:///var/run/containerd/containerd.sock": rpc error: code = Unimplemented desc = unknown service runtime.v1.RuntimeService"
, error: exit status 1
### there is no dockershim in kubernetes v1.24 you'll need to install and configure containerd or cri-o
```

```markdown
2. 配置containerd

2.1 生成默认配置文件

创建配置目录并生成默认配置文件：


 Copy code
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml

2.2 修改配置文件

编辑 /etc/containerd/config.toml 文件，确保 SystemdCgroup 设置为 true：


 Copy code
sudo nano /etc/containerd/config.toml

找到 [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options] 部分，将 SystemdCgroup 设置为 true：


 Copy code
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
  SystemdCgroup = true

保存并退出编辑器。


2.3 启动并启用 containerd

启动 containerd 服务并设置开机自启动：


 Copy code
sudo systemctl restart containerd
sudo systemctl enable containerd

3. 配置Kubelet使用containerd

3.1 创建或编辑Kubelet配置文件

编辑 /etc/sysconfig/kubelet 文件，添加或修改以下配置以指向 containerd 的 socket：


 Copy code
sudo nano /etc/sysconfig/kubelet

添加以下行：


 Copy code
KUBELET_EXTRA_ARGS="--container-runtime=remote --container-runtime-endpoint=unix:///run/containerd/containerd.sock"

保存并退出编辑器。


3.2 重新启动Kubelet服务

重新加载 systemd 配置并重启 Kubelet 服务：


 Copy code
sudo systemctl daemon-reload
sudo systemctl restart kubelet

4. 验证

确保 containerd 和 kubelet 服务运行正常：


 Copy code
sudo systemctl status containerd
sudo systemctl status kubelet

如果服务状态显示为 active (running)，则表示配置成功。
```

```shell
sudo firewall-cmd --permanent --add-port=6443/tcp
sudo firewall-cmd --permanent --add-port=10250/tcp
sudo firewall-cmd --permanent --add-port=2379-2380/tcp # etcd服务器客户端通信
sudo firewall-cmd --permanent --add-port=10251/tcp # kube-scheduler
sudo firewall-cmd --permanent --add-port=10252/tcp # kube-controller-manager
sudo firewall-cmd --permanent --add-port=30000-32767/tcp # NodePort服务
```

## 手把手部署k8s集群

### 1.准备服务器（虚拟机）

准备3台服务器（虚拟机），分别作为1个master node 和 2个worker node

分配资源：2核2G 20G磁盘空间

因为我是在个人电脑上创建的虚拟机，这里”网络适配器“选择：NAT



### 2.构建master node

#### 2.1构建前准备shell

```shell
# (1) 关闭swap分区（永久）
# ① 编辑/etc/fstab文件，把加载swap分区的那行记录注释掉即可，如""
vim /etc/fstab
# ② 移除当前的swap分区
swapoff -a
# ③ 验证
# 执行以下命令，如果没有输出，则说明已关闭swap分区
swapon --show

# (2) 关闭selinux（永久）
# ① 编辑/etc/selinux/config文件，设置"SELINUX=disabled"
vim /etc/selinux/config
# ② 完成修改config文件后，需要重启ECS实例使配置生效，但直接重启实例将会出现系统无法启动的错误。
#     因此，在重启之前需要在根目录下新建autorelabel文件，以避免出现该问题。（可选）
touch /.autorelabel
# ③ 重启
shutdown -r now
# ④验证
# 执行以下命令，如果Selinux status 为 disabled，说明已关闭selinux服务
sestatus

# 关闭防火墙？？？？

# 将桥接的IPv4流量传递到iptables的链
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
还未实现
# (3) 
```






