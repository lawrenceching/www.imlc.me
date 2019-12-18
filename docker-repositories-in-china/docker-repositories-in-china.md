# Docker/Kubernetes 中国镜像

基于众所周知的原因，我们没法顺顺利利连上 Docker 的官方镜像。另外还有部分厂商选择把自家的镜像托管在谷歌镜像服务（gcr.io）和 quay.io 上。
本文搜集了这三家镜像源的国内镜像。自己使用下来，阿里云、中科大和 Azure 的速度都不错，下载可以跑上10mb/s。 

## Docker Hub
```
https://registry.cn-hangzhou.aliyuncs.com
https://dockerhub.azk8s.cn
https://docker.mirrors.ustc.edu.cn
https://registry.docker-cn.com
https://hub-mirror.c.163.com
```

#### https://docker.mirrors.ustc.edu.cn - 中科大
[Docker 镜像使用帮助 LUG@USTC](https://lug.ustc.edu.cn/wiki/mirrors/help/docker)
[Docker Hub 源使用帮助 — USTC Mirror Help  文档](http://mirrors.ustc.edu.cn/help/dockerhub.html)

#### https://dockerhub.azk8s.cn - Azure
[Docker Registry Proxy Cache](http://mirror.azure.cn/help/docker-registry-proxy-cache.html)

Example  
`docker pull dockerhub.azk8s.cn/library/node:10.16.3-alpine`

## gcr.io
```
registry.cn-hangzhou.aliyuncs.com
gcr.azk8s.cn
```

#### registry.cn-hangzhou.aliyuncs.com - Aliyun
Example  
`docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause-amd64:3.0`

#### gcr.azk8s.cn - Azure
[GCR Proxy Cache](http://mirror.azure.cn/help/gcr-proxy-cache.html)

Example  
`docker pull gcr.azk8s.cn/google_containers/pause-amd64:3.0`

#### gcr.mirrors.ustc.edu.cn - USTC
测试不通。不知道是临时挂了还是永久下线了。


## quay.io
```
quay.mirrors.ustc.edu.cn
quay.azk8s.cn
```

#### quay.mirrors.ustc.edu.cn - USTC
Example  
`docker pull quay.mirrors.ustc.edu.cn/coreos/kube-state-metrics:v1.5.0`

#### quay.azk8s.cn - Azure
Example  
`docker pull quay.azk8s.cn/coreos/kube-state-metrics:v1.5.0`

- - - -
Tags: 中国镜像, Docker镜像, 阿里云镜像, gcr.io镜像,quay.io镜像
