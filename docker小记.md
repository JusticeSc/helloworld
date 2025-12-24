# docker小记

### 1. docker是什么

Docker 是一个用于开发、交付和运行应用程序的开放平台。Docker 在Linux容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护。

> Linux 容器不是模拟一个完整的操作系统，而是对进程进行隔离。或者说，在正常进程的外面套了一个保护层。对于容器里面的进程来说，它接触到的各种资源都是虚拟的，从而实现与底层系统的隔离。
>
> Docker使用操作系统虚拟化技术将软件及其依赖环境打包成容器以便一次构建到处运行，其基于cgroup,namespacd,overlayFS,
> unionFS等技术实现。
>
> 1. namespace: 提供隔离，让每个容器拥有独立的系统视图
>
> 2. cgroups: 限制和监控资源使用，确保公平分配。
> 3. unionFS: 实现镜像分层和容器可写层。

### 2. 为什么用docker

docker的优势主要可分为三点：

1. **快速、一致地交付应用程序**：开发者在本地构建的镜像，可以在测试、预发布和生产环境中完全一致地运行，彻底消除了“在我机器上能跑”的环境差异问题。这实现了从开发到生产的持续集成和持续部署（CI/CD） 流水线自动化，交付速度极快
2. **灵活响应的部署与扩展**：Docker 容器是轻量级、可移植的进程单元，启动时间仅为秒级。开发者结合编排工具（如Kubernetes）可以轻松地横向扩展，快速启动多个容器副本以应对流量高峰，还有快速回滚镜像版本，以及在服务器集群中灵活迁移和重新部署容器
3. **相同硬件条件下可运行更多的工作负载**：与虚拟机（VM）每个实例都需要完整的客户机操作系统不同，Docker 容器共享主机系统的内核，在进程级别进行隔离，因此其资源开销更低，能够提升硬件资源的利用率

### 3. Docker架构

Docker 采用客户端-服务器架构。Docker 客户端与 Docker 守护进程通信，由守护进程负责执行构建、运行和分发 Docker 容器等繁重任务。

![image-20251224155812864](C:\Users\31368\Desktop\笔记\assets\image-20251224155812864.png)

核心组件：

1. **Docker守护进程**：负责监听 Docker API 请求，并管理 Docker 对象，如镜像、容器、网络和卷。守护进程还可以与其他守护进程通信，以管理 Docker 服务。
2. **Docker客户端**：用户和Docker交互的界面。客户端负责将命令发送给守护进程，一个客户端可以与多个守护进程通信。
3. **Docker Registry**: 集中存储和分发镜像的服务。公共 registry（如 Docker Hub）用于共享；私有 registry 用于企业内部。
4. **Docker 对象**：
   1. **镜像（Image）**：只读的模板，包含创建容器所需的文件系统和配置。是容器的“构建蓝图”。
   2. **容器（Container）**：镜像的可运行实例。是隔离的进程，拥有自己的文件系统、网络和配置。是应用的“运行实体”。
   3. **其他**：网络（Network）提供容器间通信，数据卷（Volume）实现持久化存储。

> 1. docker镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建后也不会被改变。
> 2. docker容器是一个特殊的进程，其提供轻量级、隔离的运行环境，通过命名空间和分层存储实现资源隔离和临时文件系统，持久化数据需要外部卷
> 3. docker registry 是集中存储分发镜像的服务，默认的公开docker registry服务就是docker hub,针对其的mirror服务叫加速器，除此之外还有其他公开docker registry服务如redhat 的quay.io,阿里云镜像库，也可以搭建私有docker registry服务
> 4. 一个docker registry包含多个repository，一个repository可以包含多个tag，一个tag对应一个image
> 5. 一个仓库存储一个软件不同版本的镜像，每个镜像使用 repository:tag 标识，如果不指明tag默认是latest，仓库名常以用户名/软件名形式出现，但非绝对

### 4. Docker核心命令

#### 4.1 container

container即容器，其包含以下特性：

1. 自包含：每个容器都具备自身运行所需的一切，无需依赖宿主机上预先安装的任何依赖项。
2. 隔离性。由于容器在隔离环境中运行，它们对宿主机和其他容器的影响极小，从而提升应用程序的安全性。
3. 独立性。每个容器独立管理。删除一个容器不会影响任何其他容器。
4. 可移植性。容器可在任何地方运行

管理容器主要包括以下操作：

1. 生命周期管理：
   ```bash
   # 1. 运行容器
   docker run -d --name 容器名 -p 主机端口:容器端口 镜像名/镜像ID
   
   # 2. 查看容器
   docker ps -a           # 查看所有
   docker ps              # 查看运行中
   
   # 3. 停止/启动
   docker stop 容器名/容器ID
   docker start 容器名/容器ID
   
   # 4. 删除容器
   docker rm 容器名/容器ID
   docker rm -f 容器名/容器ID    # 强制删除运行中
   
   # 5. 重启
   docker restart 容器名/容器ID
   ```

   部分操作展示：
   ![](C:\Users\31368\Desktop\笔记\assets\image-20251224164045228.png)

   > 在Docker的早期版本中，命令是直接以docker <command>的形式出现，后来为了更好的组织命令，Docker将命令进行了分组，形成了新的命令结构。但是，旧的命令仍然可以使用，只不过在新版本中，Docker推荐使用新的命令格式
   >
   > 因此docker ps 也等价于docker container ls
   >
   > 类似的还有 docker images == docker image ls , docker run == docker container run

2. 状态监控：

   ```bash
   # 6. 查看日志
   docker logs 容器名/容器ID
   docker logs -f 容器名/容器ID   # 实时跟踪
   
   # 7. 进入容器
   docker exec -it 容器名/容器ID /bin/bash
   
   # 8. 资源监控
   docker stats           # 实时资源使用
   ```

3. 批量操作：
   ```bash
   # 9. 批量停止
   docker stop $(docker ps -q)
   
   # 10. 批量删除
   docker rm $(docker ps -aq)
   ```

#### 4.2 image

image即镜像文件，包括应用程序及其依赖，可以看作是容器的模板，一个image可以生成多个同时运行的容器。image也是可以层层继承依赖的，而为了加速镜像构建、重复利用资源的镜像就是上层镜像，负责为顶层镜像提供依赖。

常用命令：
```bash
# 1. 查看镜像
docker images
# 2. 拉取镜像
docker pull [镜像名/镜像ID]
# 3. 构建镜像
docker build -t [镜像名/镜像ID] .
# 4. 删除镜像
docker rmi [镜像名/镜像ID]
# 5. 镜像标签
docker tag [旧名] [新名]
# 6. 推送镜像
docker push [镜像名/镜像ID]
# 7. 清理镜像
docker image prune
```

#### 4.3 registry

仓库（Repository）是集中存放镜像的地方，而registry（注册服务器）是管理仓库的具体服务器，每个服务器上可以有多个仓库，而每个仓库下面有多个镜像。

`docker login`可以登录服务器，登录后可以`docker search`查找仓库中的镜像，并利用`docker pull`下载到本地，也可以`docker push`推送自己制作的镜像。未登录情况下也可以拉取镜像，只不过有次数限制。

> docker hub服务器在国外，可能会存在网络问题
>
> 避坑：阿里云加速器不再同步更新镜像，无法获取较新版本的镜像

### 参考文献

[Docker-从入门到实践](https://yeasy.gitbook.io/docker_practice)

[Docker官方入门文档](https://docs.docker.com/get-started/)

[阮一峰-Docker入门教程](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)