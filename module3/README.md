# 模块三作业



##  作业要求

构建本地镜像

-  编写 Dockerfile 将练习 2.2 编写的 httpserver 容器化

- 将镜像推送至 docker 官方镜像仓库

- 通过 docker 命令本地启动 httpserver

- 通过 nsenter 进入容器查看 IP 



## 使用说明

### 1，下载源代码

```bash
git clone https://github.com/Wuzhibin05/cncamp.git
```



### 2，构建docker镜像

```bash
cd cncamp/module3/httpserver
```

- 源码编译成二进制

```bash
make build
```

```bash
root@master:/opt/project/src/cncamp/module3/httpserver# make build
echo "building httpserver binary"
building httpserver binary
mkdir -p bin/amd64
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o bin/amd64 .
```

- 构建镜像

```
make release
```

```bash
root@master:/opt/project/src/cncamp/module3/httpserver# make release
echo "building httpserver binary"
building httpserver binary
mkdir -p bin/amd64
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o bin/amd64 .
echo "building httpserver image"
building httpserver image
docker build -t wuzb/httpserver:v1.0 .
Sending build context to Docker daemon  6.067MB
Step 1/6 : FROM ubuntu:20.10
 ---> e508bd6d694e
Step 2/6 : ENV MY_SERVICE_PORT=8080
 ---> Using cache
 ---> 20eb2394f98e
Step 3/6 : LABEL Author="wuzhibin05@163.com"
 ---> Using cache
 ---> d64d6bb5aeab
Step 4/6 : ADD bin/amd64/httpserver /httpserver
 ---> Using cache
 ---> d084f5b1b5f8
Step 5/6 : EXPOSE 8080
 ---> Using cache
 ---> 0696bfd0767a
Step 6/6 : ENTRYPOINT /httpserver
 ---> Using cache
 ---> 4c50d9cecc63
Successfully built 4c50d9cecc63
Successfully tagged wuzb/httpserver:v1.0
```



### 3，推送镜像到docker 官方镜像仓库

- 登录镜像仓库

```bash
make login
```

```bash
root@master:/opt/project/src/cncamp/module3/httpserver# make login
make: Circular login <- login dependency dropped.
echo "login docker.io"
login docker.io
docker login docker.io -u wuzb
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

```



- 推送到官方仓库

```bash
make push
```

```bash
root@master:/opt/project/src/cncamp/module3/httpserver# make push
echo "building httpserver binary"
building httpserver binary
mkdir -p bin/amd64
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o bin/amd64 .
echo "building httpserver image"
building httpserver image
docker build -t wuzb/httpserver:v1.0 .
Sending build context to Docker daemon  6.067MB
Step 1/6 : FROM ubuntu:20.10
 ---> e508bd6d694e
Step 2/6 : ENV MY_SERVICE_PORT=8080
 ---> Using cache
 ---> 20eb2394f98e
Step 3/6 : LABEL Author="wuzhibin05@163.com"
 ---> Using cache
 ---> d64d6bb5aeab
Step 4/6 : ADD bin/amd64/httpserver /httpserver
 ---> Using cache
 ---> d084f5b1b5f8
Step 5/6 : EXPOSE 8080
 ---> Using cache
 ---> 0696bfd0767a
Step 6/6 : ENTRYPOINT /httpserver
 ---> Using cache
 ---> 4c50d9cecc63
Successfully built 4c50d9cecc63
Successfully tagged wuzb/httpserver:v1.0
echo "pushing image to repository"
pushing image to repository
docker push wuzb/httpserver:v1.0
The push refers to repository [docker.io/wuzb/httpserver]
27c51d33356d: Pushed 
c7b30e8af03a: Mounted from library/ubuntu 
v1.0: digest: sha256:9a6c7662edd9d7b19ce34988ac13b38846608edf373db4ac74c1d5fef3ec3cc4 size: 740
```


### 4，本地运行镜像

- 本地运行

```bash
docker run \
	-d \
    -p 8080:8080 \
    --name httpserver \
    --restart=always \
    wuzb/httpserver:v1.0
```

- 访问：http://ip:8080/



### 5，通过 nsenter 进入容器查看 IP 

- 查看pid

```bash
docker inspect httpserver --format "{{ .State.Pid }}"
```

```bash
root@master:/opt/project/src/cncamp/module3/httpserver# docker inspect httpserver --format "{{ .State.Pid }}"
8142
```

- 查看ip

方法一：直接查看

```bash
docker inspect httpserver --format "{{ .NetworkSettings.IPAddress }}"
```

 

```bash
root@master:/opt/project/src/cncamp/module3/httpserver# docker inspect httpserver --format "{{ .NetworkSettings.IPAddress }}"
172.17.0.2
```

方法二：通过nsenter

```bash
nsenter -t 8142 -n ip addr
```

```bash
root@master:/opt/project/src/cncamp/module3/httpserver# nsenter -t 8142 -n ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
13: eth0@if14: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

```
