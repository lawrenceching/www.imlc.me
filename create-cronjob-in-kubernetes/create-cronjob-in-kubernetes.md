# 创建 Kubernetes Cron 定时任务
cron 是开发中常用的定时任务工具。cron 表示式由于其强大的表达能力，能满足各种条件的定时计划。但是单机的 cron 工具（例如 crontab)，无法满足大规模的 cron job 任务部署和运行。而 Kubernetes 的容器调度和编排恰好满足了这方面的需求。

## 创建一个简单的 Cron 定时任务
下面会以官方的 hello 镜像为例，创建一个简单的定时任务。并且演示一下相关联的基础操作（例如日志查询、Job 查询、删除任务等）。


### 创建 .yaml 文件
源代码储存在 [CODING | 一站式软件研发管理平台](https://lawrenceching.coding.net/p/play-with-kubernetes/d/play-with-kubernetes/git/blob/master/cronjob/create-cronjob.yaml)
创建 `create-cronjob.yaml`

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

### 部署
登录 k8s master 节点。运行如下 `kubectl` 命令
```bash
kubectl create -f ./create-cronjob.yaml
```
此命令告诉 kubectl 按照当前目录下的 `create-cronjob.yaml` 文件创建 cron job。

> 你也可以直接调用 `kubectl create -f https://lawrenceching.coding.net/p/play-with-kubernetes/d/play-with-kubernetes/git/raw/master/cronjob/create-cronjob.yaml` ，直接使用我已创建好的 yaml 文件。  

当你看到命令行输出 `cronjob.batch/hello created` ，创建 cronjob 就成功了。

### 查看和删除 cronjob 和 job

cronjob 是定时任务的要求。 job 是 cronjob 触发后真实在跑的任务。别混淆。

#### 查看和删除 cron job
```bash
# 查询 cronjob
Kubectl get cronjob
NAME    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
hello   */1 * * * *   False     1        8s              3m12s
# 删除 cronjob
Kubectl delete cronjob hello
```


#### 查看和删除 job
```bash
# 查询 jobs
Kubectl get jobs
NAME               COMPLETIONS   DURATION   AGE
hello-1574569560   1/1           12s        2m55s
hello-1574569620   1/1           18s        115s
hello-1574569680   1/1           10s        55s
# 删除 jobs
Kubectl delete jobs hello-1574569680
```

如果嫌麻烦，直接运行 `kubectl get all` 就可以看到当前 k8s 集群所有的状态。
```bash
kubectl get all
NAME                         READY   STATUS      RESTARTS   AGE
pod/hello-1574569500-fhmwm   0/1     Completed   0          93s
pod/hello-1574569560-kh46t   0/1     Completed   0          33s


NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   62d

NAME                         COMPLETIONS   DURATION   AGE
job.batch/hello-1574569500   1/1           16s        93s
job.batch/hello-1574569560   1/1           12s        33s

NAME                  SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/hello   */1 * * * *   False     0        33s             97s
```

你可以运行 `kubectl delete <name>` 删除你想删除的任何资源。`<name>` 为 `kubectl get all` 输出中 NAME 列显示的名称。

### 查看运行日志

Kubernetes 会为 job 创建容器并执行。要查看 job 的运行日志，相当于要查看 job 所在的容器的日志。以查看 `hello-1574569560` job 为例。

```bash
# 查找 job 所在的 pod
Kubectl get pods | grep hello-1574569560

# 查看日志
Kubectl logs <pod-id>
```
