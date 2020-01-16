# usage

## Before you start

将docker文件夹中的dockerfile文件中的git clone后面的地址中的USERNAME和PASSWORD改为你的远程仓库用户名和密码。

## Prerequisites

- [docker-18.03.0](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)
- [nvidia-docker](https://github.com/nvidia/nvidia-docker/wiki/Installation-(version-2.0))

> 注意：在使用GPU之前要根据docker镜像里面的cuda版本和宿主机的GPU架构，安装合适的nvidia驱动

### nvidia驱动版本

| CUDA toolkit version | Driver version  | GPU architecture |
| :----: | :------: | :------: |
| 6.5 | >= 340.29 | >= 2.0 (Fermi) |
| 7.0 | >= 346.46 | >= 2.0 (Fermi) |
| 7.5 | >= 352.29 | >= 2.0 (Fermi) |
| 8.0 | == 361.93 or >= 375.51 | == 6.0 (P100)) |
| 8.0 | >= 367.48 | >= 2.0 (Fermi) |
| 9.0 | >= 384.81 | >= 3.0 (Kepler) |
| 9.1 | >= 387.26 | >= 3.0 (Kepler) |


## Depends (nvidia-docker2)
- Depends: nvidia-container-runtime  
- Depends: docker-ce  

### 查询可用版本

```bash
# Use apt-cache madison nvidia-docker2 nvidia-container-runtime to list the available versions
# docker-ce
$ apt-cache madison docker-ce 
# results
 docker-ce | 18.03.1~ce-0~ubuntu | https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu xenial/stable amd64 Packages
 docker-ce | 18.03.0~ce-0~ubuntu | https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu xenial/stable amd64 Packages
 docker-ce | 17.12.1~ce-0~ubuntu | https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu xenial/stable amd64 Packages
# nvidia-container-runtime
$ apt-cache madison nvidia-container-runtime
# results
 nvidia-container-runtime | 2.0.0+docker18.03.1-1 | https://nvidia.github.io/nvidia-container-runtime/ubuntu16.04/amd64  Packages
 nvidia-container-runtime | 2.0.0+docker18.03.0-1 | https://nvidia.github.io/nvidia-container-runtime/ubuntu16.04/amd64  Packages
 nvidia-container-runtime | 2.0.0+docker17.12.1-1 | https://nvidia.github.io/nvidia-container-runtime/ubuntu16.04/amd64  Packages

# nvidia-docker2
$ apt-cache madison nvidia-docker2
# results
 nvidia-docker2 | 2.0.3+docker18.03.1-1 | https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64  Packages
 nvidia-docker2 | 2.0.3+docker18.03.0-1 | https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64  Packages
 nvidia-docker2 | 2.0.3+docker17.12.1-1 | https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64  Packages
```
### 安装nvidia-docker2
```bash
# 若docker-ce选择安装18.03.0~ce-0~ubuntu版本，则nvidia-container-runtime与nvidia-docker2都需要安装对应的版本，如下：
$ sudo apt-get install -y 18.03.0~ce-0~ubuntu
$ sudo apt-get install -y nvidia-docker2=2.0.3+docker18.03.0-1 nvidia-container-runtime=2.0.0+docker18.03.0-1
```
### 下载redis镜像
```bash
$ docker pull redis
```
## GPU

### RESTFUL版本
```bash
# current path is detector/docker
# build image
$ docker build -t verifier-rest:gpu -f Dockerfile_rest .
# docker-compose 一键启动
$ docker-compose -f docker_compose_rest.yml up -d
```
### ZMQ版本
```bash
# current path is detector/docker
# build verifier image
$ docker build -t verifier-zmq-dealer:gpu -f Dockerfile_zmq_dealer .
# build router image
$ docker build -t verifier-zmq-router:gpu -f Dockerfile_zmq_router .
# docker-compose 一键启动
$ docker-compose -f docker_compose_zmq.yml up -d
# 使用docker-compose前提是安装了正确版本的nvidia-container-runtime之后，配置/etc/docker/daemon.json默认的runtime为nvidia，示例如下：

{
  "runtimes": {
    "nvidia": {        
       "path": "/usr/bin/nvidia-container-runtime",
       "runtimeArgs": []
       }
    },
  "default-runtime": "nvidia"
}
```

## CPU

### RESTFUL版本

```bash
# current path is detector/docker
# 取消Dockfile_rest的rest和cpu下方的注释
# build image
$ docker build -t verifier-rest:cpu -f Dockerfile_rest .
# docker-compose 一键启动
$ docker-compose -f docker_compose_rest.yml up -d
# docker-compose 一键停止并删除docker容器
$ docker-compose -f docker_compose_zmq.yml down
```
### ZMQ版本

```bash
# current path is detector/docker
# 取消Dockfile_zmq_dealer的rest和cpu下方的注释
# build verifier image
$ docker build -t verifier-zmq-dealer:cpu -f Dockerfile_zmq_dealer .
# build router image
$ docker build -t verifier-zmq-router:cpu -f Dockerfile_zmq_router .
# docker-compose 一键启动(注意docker-compose.yml文件中的镜像名字一定要正确)
$ docker-compose -f docker_compose_zmq.yml up -d
# docker-compose 一键停止并删除docker容器(注意docker-compose.yml文件中的镜像名字一定要正确)
$ docker-compose -f docker_compose_zmq.yml down
```
