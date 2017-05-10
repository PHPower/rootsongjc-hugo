+++
date = "2017-05-10T17:14:20+08:00"
draft = false
title = "Kubernetes中的Rolling Update服务滚动升级"
Tags = ["kubernetes"]

+++

![后海夜色](http://olz1di9xf.bkt.clouddn.com/20170430014.jpg)

*（题图：后海夜色 Apr 30,2017）*

## 前言

本文已同步到gitbook [kubernetes-handbook](https://github.com/rootsongjc/kubernetes-handbook)的第8章第1节。

本文说明在Kubernetes1.6中服务如何滚动升级，并对其进行测试。

当有镜像发布新版本，新版本服务上线时如何实现服务的滚动和平滑升级？

如果你使用**ReplicationController**创建的pod可以使用`kubectl rollingupdate`命令滚动升级，如果使用的是**Deployment**创建的Pod可以直接修改yaml文件后执行`kubectl apply`即可。

Deployment已经内置了RollingUpdate strategy，因此不用再调用`kubectl rollingupdate`命令，升级的过程是先创建新版的pod将流量导入到新pod上后销毁原来的旧的pod。

Rolling Update适用于`Deployment`、`Replication Controller`，官方推荐使用Deployment而不再使用Replication Controller。

使用ReplicationController时的滚动升级请参考官网说明：https://kubernetes.io/docs/tasks/run-application/rolling-update-replication-controller/

## 创建测试镜像

我们来创建一个特别简单的web服务，当你访问网页时，将输出一句版本信息。通过区分这句版本信息输出我们就可以断定升级是否完成。

所有配置和代码见Github上的[manifests/test/rolling-update-test](https://github.com/rootsongjc/kubernetes-handbook/tree/master/manifests/test/rolling-update-test)目录。

**Web服务的代码main.go**

```Go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func sayhello(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "This is version 1.") //这个写入到w的是输出到客户端的
}

func main() {
	http.HandleFunc("/", sayhello) //设置访问的路由
	log.Println("This is version 1.")
	err := http.ListenAndServe(":9090", nil) //设置监听的端口
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```

**创建Dockerfile**

```Dockerfile
FROM alpine:3.5
MAINTAINER Jimmy Song<rootsongjc@gmail.com>
ADD hellov2 /
ENTRYPOINT ["/hellov2"]
```

注意修改添加的文件的名称。

**创建Makefile**

修改镜像仓库的地址为你自己的私有镜像仓库地址。

修改`Makefile`中的`TAG`为新的版本号。

```cmake
all: build push clean
.PHONY: build push clean

TAG = v1

# Build for linux amd64
build:
	GOOS=linux GOARCH=amd64 go build -o hello${TAG} main.go
	docker build -t sz-pg-oam-docker-hub-001.tendcloud.com/library/hello:${TAG} .

# Push to tenxcloud
push:
	docker push sz-pg-oam-docker-hub-001.tendcloud.com/library/hello:${TAG}

# Clean 
clean:
	rm -f hello${TAG}
```

**编译**

```Shell
make all
```

分别修改main.go中的输出语句、Dockerfile中的文件名称和Makefile中的TAG，创建两个版本的镜像。

## 测试

我们使用Deployment部署服务来测试。

配置文件`rolling-update-test.yaml`：

```Yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
    name: rolling-update-test
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: rolling-update-test
    spec:
      containers:
      - name: rolling-update-test
        image: sz-pg-oam-docker-hub-001.tendcloud.com/library/hello:v1
        ports:
        - containerPort: 9090
---
apiVersion: v1
kind: Service
metadata:
  name: rolling-update-test
  labels:
    app: rolling-update-test
spec:
  ports:
  - port: 9090
    protocol: TCP
    name: http
  selector:
    app: rolling-update-test
```

**部署service**

```shell
kubectl create -f rolling-update-test.yaml
```

**修改traefik ingress配置**

在`ingress.yaml`文件中增加新service的配置。

```Yaml
  - host: rolling-update-test.traefik.io
    http:
      paths:
      - path: /
        backend:
          serviceName: rolling-update-test
          servicePort: 9090
```

修改本地的host配置，增加一条配置：

```
172.20.0.119 rolling-update-test.traefik.io
```

注意：172.20.0.119是我们之前使用keepalived创建的VIP。

打开浏览器访问http://rolling-update-test.traefik.io将会看到以下输出：

```
This is version 1.
```

**滚动升级**

只需要将`rolling-update-test.yaml`文件中的`image`改成新版本的镜像名，然后执行：

```shell
kubectl apply -f rolling-update-test.yaml
```

在浏览器中刷新http://rolling-update-test.traefik.io将会看到以下输出：

```
This is version 2.
```

说明滚动升级成功。

## 参考

[Rolling update机制解析](http://dockone.io/article/328)

[Running a Stateless Application Using a Deployment](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)

[Simple Rolling Update](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/simple-rolling-update.md)

