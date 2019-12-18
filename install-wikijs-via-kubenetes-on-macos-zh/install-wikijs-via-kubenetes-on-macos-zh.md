# 在 macOS 上通过 Kubenetes 安装 wiki.js

> [wiki.js](https://wiki.js.org/) 是一款完全由 JavaScript 技术栈创建的百科应用。前端由 Vue.js 开发，而后台为 Node.js。只要安装好 Node.js ，wiki.js 能很简单地在任何环境下跑起来。wiki.js 内置了登录和搜索功能，拥有良好的自定义和扩展能力。并且支持了大部分常用的数据库软件。

> Wordpress 虽然也很不错，但是 PHP 的技术栈真的不太熟悉。

> 本文提供了 wiki.js 2 的本地安装方式。该方式仅适合本地开发和试用，数据库采用 SQLite，旨在不用安装任何数据库即可上手试玩。注意 wiki.js 2 还处在 Beta 阶段，需要稳定版本的可以选择 wiki.js 1。

创建`wikijs.yml`

```
apiVersion:v1
kind:Pod
metadata:
name:wikijs
labels:
env:dev
spec:
containers:
-name:wikijs
image:requarks/wiki:beta
ports:
-containerPort:3000
env:
-name:DB_TYPE
value:"sqlite"
-name:DB_FILEPATH
value:"/tmp/wiki.db"
```

然后运行

```
kubectl apply -f ./wikijs.yml
```

然后通过 port fowarding 把 Pod 暴露到外界

```
kubectl port-forward wikijs 3000:3000
```
