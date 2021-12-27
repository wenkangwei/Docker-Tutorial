# 1-Container

## 1. Introduction

**容器定义:** 容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。 Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

### docker 特点

* **简化开发周期:** Docker 允许开发人员使用您提供的应用程序或服务的本地容器在标准化环境中工作，从而简化了开发的生命周期
* **可延展性**. Docker 是基于容器的平台，允许高度可移植的工作负载。Docker 容器可以在开发人员的本机上，数据中心的物理或虚拟机上，云服务上或混合环境中运行。
* **轻巧快速**。它为基于虚拟机管理程序的虚拟机提供了可行、经济、高效的替代方案，因此您可以利用更多的计算能力来实现业务目标。Docker 非常适合于高密度环境以及中小型部署，而您可以用更少的资源做更多的事情.

## 2. 容器Container使用

### Usefull commands

docker的使用有点像git `docker search <img-name>` : 搜索docker 已有的镜像 `docker pull <img-name>` : 从线上下载一个镜像 `docker ps -a`: 查看docker中所有容器和对应的镜像image, 每个容器对应在一个image镜像里面. (其实这里的关系类似于一个程序对应一个进程, 一个container类似一个进程, 一个image类似一个程序) `docker rm <container-id>`: 删除container `docker rmi <image-name>`: 删除image 镜像

### 常用容器操作

#### 容器启动和后台运行

* `docker run <img-name> <command-of-img>` : 执行一个镜像, 并向这个镜像的程序输入 commands
* `docker start <container-id>`: 启动一个已经停止的container
* `docker stop <container-id>`: 停止一个已经停止的container
* `docker restart <container-id>`: 重启一个已经停止的container
* `docker run -it <img-name> <command-of-img>` : 这里增加 `-it` 选择, 其中`-i` 是interact `-t` 是terminal合起来启动terminal进行互动输入输出
* `docker run -itd --name <container-name> <img> <commands>`: 把容器在后台跑, 容器名字为`<container-name>`用于区分container, 例子terminal不会进入容器 `docker run -itd --name ubuntu-test ubuntu /bin/bash`.

这里如果想进入后台的container 可以用 `attach` 或 `exec` `docker attach <container-id>`: 进入一个后台的container, 这里attach里面的是container的id, 不能说container name

* `docker exec -it <container-id> <command>`: 进入一个后台的container, 这里exec 要加入 `-it <container-id> <command>` 从能重新进入terminal里面, 但是 `attach` 只要直接后面加container-id即可 **attach 和 exec区别: 是attach进入container的terminal再exit后container会停止, 而 exec不会,并且会保存上一条记录**

#### 容器导入导出

* `docker export <container-id> > output_file` : 把docker 的container导出到指定的文件
* `cat <container-file-name> | docker import - test/<container-name>:<tag>` : 把docker镜像文件读取并导入到 test目录到`container-name` 并把导入的image 标注tag为 `tag`
* `docker import <url>`: 从url导入数据

#### Webapp 容器使用

* `-d`:让容器在后台运行。
* `-P`: 将容器内部使用的网络端口随机映射到我们使用的主机上。

例子: `docker run -d -P training/webapp python app.py` : 让webapp 的python应用绑定到port `docker ps -a`: 之后查看容器的绑定端口, 并在浏览器访问 `docker run -d -p 5000:5000 training/webapp python app.py` : 指定绑定的端口来运行webapp

`docker logs -f <image-name>` : 查看容器的日志

## 3. 镜像Image使用

#### 构建 docker镜像

为了从零构建一个docker的镜像, 需要有一个Dockerfile 描述 怎么构建image

* 规则
  * 每一个指令都会在镜像上创建一个新的层，每一个**指令的前缀都必须是大写的**。
  * 第一条`FROM`，指定使用**哪个镜像源**
  * `RUN` 指令告诉docker 在镜像内执行命令，安装了什么。。。
  * 然后，我们使用 Dockerfile 文件，通过 docker build 命令来构建一个镜像
*   Dockerfile 例子:

    ```
    FROM    centos:6.7
    MAINTAINER      Fisher "fisher@sudops.com"

    RUN     /bin/echo 'root:123456' |chpasswd
    RUN     useradd runoob
    RUN     /bin/echo 'runoob:123456' |chpasswd
    RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
    EXPOSE  22
    EXPOSE  80
    CMD     /usr/sbin/sshd -D
    ```
* 构建镜像
  * `docker build -t <image-name> <Dockerfile-path>`: `-t` 制定构建的镜像名称, `<Dockerfile-path>` 指定要执行的DockerFile路径. 例子`docker build -t runoob/centos:6.7 .` 把名为 `runoob/centos:6.7` 的镜像按照当前目录的Dockerfile进行构建.

`docker tag <image-id> <tag-name>`: 为镜像添加tag

## 4. Reference

Runoob: [https://www.runoob.com/docker/docker-tutorial.html](https://www.runoob.com/docker/docker-tutorial.html)
