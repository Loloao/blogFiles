# Docker

> Docker 能干什么？

1. 简化配置，
2. 代码流水线管理
3. 提高开发效率
4. 隔离应用
5. 整合服务器
6. 调试能力
7. 多租户
8. 快速部署

> k8s 是干什么的？

它是一个容器编排的工具，对容器的创建、管理、调度、运维。

> 容器是什么？

在很久之前是先有一台服务器，再安装操作系统，再安装 App，缺点有很多

1. 部署非常慢
2. 成本非常高
3. 资源浪费
4. 难以迁移和扩展
5. 可能会被限定硬件厂商

虚拟化技术出现以后，通过一层 Hypervisor 来进行物理资源的虚拟化，在虚拟化之上安装操作系统，一个物理机可以部署多个 app，每个 app 独立运行在一个 VM 里，它有很多优点

1. 资源池——一个物理机的资源分配到不同的虚拟机里
2. 很容易扩展——加物理机器 or 加虚拟机
3. 很容易云化——亚马逊 AWS，阿里云等

它也有局限性，每一个虚拟机都是一个完整的操作系统，要给其分配资源，当虚拟机数量增多时，操作系统本身消耗的资源势必增多

现在开发和运维面临的挑战，在开发一个项目的时候，我们需要部署数据库，后台，缓存，可能会使用不同的后台语言和数据库，对于开发人员的测试很复杂。对于运维人员的部署也是非常复杂，还需要开发人员和运维人员的沟通，不然环境可能不一样

容器就是对 app 的打包，通过打包可以运行在任何环境中，容器是 APP 层面的隔离，虚拟化是物理资源层面的隔离，当然虚拟化和容器技术可以一起使用

1. 对软件和其依赖的标准化打包
2. 应用之间相互隔离
3. 共享同一个 OS Kernel
4. 可以运行在很多主流操作系统上

它解决了很多问题

1. 解决了开发和运维之间的矛盾
2. 在开发和运维之间搭建了一个桥梁，是实现 devops 的最佳解决方案


### 一些技巧

- `docker-machine`可以创建一个拥有 docker 的 linux 虚拟机
- `docker playground`可以不用安装 docker 来运行 docker
- 如果我们想不用`sudo`就使用`docker`命令，可以将用户加入`docker`用户组
  - `sudo groupadd docker`
  - `sudo gpasswd -a <user> docker`
  - 重启`docker`进程`sudo service docker restart`并重启`shell`

## Dock Platform
Docker 提供了一个开发，打包，运行app的平台，并把 app 和底层`infrastructure`隔离，按顺序分为以下几层
1. `Application`
2. `Docker Engine`它有以下几个东西
  - 后台进程`dockerd`：用于容器和存储的管理，`ps -ef | grep docker`可以看到进程在`/usr/bin/dockerd`
  - `REST API Server`：用于`dockerd`和`docker`通信
  - CLI接口`docker`：
3. `Infrastructure(physical/virtual)`
![Docker Architecture](./docker-architecture.png)
底层技术支持，都是 linux 早已存在的技术
- `Namespaces`：隔离`pid`，`net`，`ipc`，`mnt`，`uts`
- `Control group`：做资源限制
- `Union file systems`：`Container`和`image`的分层

### Docker Image
> image 是什么？
- 它是文件和`meta data`的集合`root filesystem`
- 分层的，并且每一层可以添加改变删除文件，成为一个新的`image`
- 不同的`image`可以共享相同的`layer`
- `Image`本身是`read-only`的
命令
- `docker history <image id>`：可查看 image 历史
- `docker images`或`docker image ls`：可以列出所有 docker
- `docker rmi`或`docker image rm <image id>`：移除`image`
- `docker build`或`docker image build`：根据一个`Dockerfile`创建`image`
获取`Image`
- `Build from Dockerfile`：根据`Dockerfile`进行构建
  ```yaml
  # base image
  FROM ubuntu:14.04
  # 基本标识
  LABEL maintainer="Peng Xiao <123@qq.com>
  # 在 base image 基础上跑命令
  RUN apt-get update && apt-get install -y redis-server
  # 暴露端口
  EXPOSE 6379
  # 通讯入口
  ENTRYPOINT ["/usr/bin/redis-server"]
  ```
  之后通过`docker build -t <image name> <dockerfile 路径>`来进行构建
- `Pull from Registry`：从`Registry`上获取`docker image`，具体`image`可从`https://hub.docker.com`上查阅

### Container
> Container 是什么？
- 通过`docker`创建
- 在`Image layer`之上建立一个`container layer`可读写
- 类比面向对象：类和实例
- `Image`负责 app 的存储和分发，`Container`负责运行`app`
命令
- `docker run <image name>:<image version>`：基于`image`创建一个`container`，`version`不传表示最新
- `docker ps -a`或`docker container ls`：列出当前本地正在运行的容器
  - `-a`：列出所有容器，包括正在运行和退出的
  - `-aq`：列出所有`container id`
  - `-it`：可以进入这个`image`里输入命令
  - 可通过`docker rm $(docker ps -aq)`将所有正在运行的`container`移除
  - `docker rm $(docker container ls -f "status-exited" -q)`将已经退出的容器移除
- `docker rm`或`docker container rm <container id>`：移除正在运行的`docker container`
- `docker commit <container id> <container name>`或`docker container commit`：基于某个`container`，在某个`container`中做了一些变化，比如安装了某些软件，就能将它再构建为一个`image`，但这种方式并不提倡

### Dockerfile
github 上的`docker-library`有很多官方`image`的`dockerfile`，可以作为参考
- `FROM`：指定了我们定义的`base image`是什么，尽量使用官方的`image`
  - `FROM scratch`：制作`base image`
  - `FROM centos`：使用`base image`
- `LABEL`：指定`image`信息，类似于注释
  - `maintainer`：作者
  - `version`：版本
  - `description`：描述
- `RUN`：运行一些命令，但是要注意每运行一次`RUN`，`image`就会生成新的一层，复杂的`RUN`请用反斜线换行，避免无用分层，合并多条命令成一行。
- `CMD`：设置容器启动后默认执行的命令和参数
  - 如果`docker run`指定了其他命令，`CMD`命令会被忽略
  - 如果定义了多个`CMD`，只有最后一个会执行
- `ENTRYPOINT`：设置容器启动时运行的命令
  - 让容器以应用程序或服务的形式运行，比如说启动一个数据库
  - 不会被忽略，一定会执行
  - 最好写一个 shell 脚本作为`entrypoint`，比如
  ```dockerfile
  COPY docker-entrypoint.sh /usr/local/bin/
  ENTRYPOINT ["docker-entrypoint.sh"]
  ```
使用上面三个操作可以有两种格式
1. Shell 格式：`RUN apt-get install -y vim`，能够识别变量`ENV`
2. Exec 格式：`RUN ["apt-get", "install", "-y", "vim"]`，它不能识别变量，除非指定`shell`，比如`["/bin/bash", "-c", "echo hello $name"]`才会识别这里的`$name`变量
- `WORKDIR`：当前工作目录，如果后面的值没有则会自动创建目录，用`WORKDIR`，不要使用`RUN cd`，同时尽量使用绝对目录
- `ADD`：把本地文件添加到`image`里，它还可以将压缩文件解压缩
- `COPY`：类似`ADD`，但不能解压缩，大部分情况`COPY`优于`ADD`，添加远程文件/目录请使用`curl`或`wget`
- `ENV`：用于设定环境变量，在下面的命令里可以使用，可以增加可维护性


