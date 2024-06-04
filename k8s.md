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


