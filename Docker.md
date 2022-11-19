# 传统虚拟机和容器的对比

## Docker和传统虚拟化方式的不同之处

+ 传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程
+ 容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核且也没有进行硬件虚拟。因此容器需要比传统虚拟机更为轻便。
+ 每个容器之间互相隔离，每个容器有自己的文件系统，容器之间进程不会相互影响，能区分计算资源。





# Docker前提条件

Docker并非是一个通用的容器工具，它依赖于已存在并运行的Linux内核环境。

Docker实质上是在已经运行的Linux下制造了一个隔离的文件环境，因此它执行的效率几乎等同于所部署的Linux主机。

因此，Docker必须部署在Linux内核的系统上，如果其它系统想部署Docker就必须安装一个虚拟Linux环境。





# Docker三要素

+ **镜像**： 一个只读的模板。镜像可以额用来创建Docker容器，一个镜像可以创建很多容器。它也相当于是一个root文件系统。比如官方进行centos:7就包含了完整的一套centos:7最小系统的root文件系统。相当于容器的“源代码”，docker镜像文件类似于Java的类模板，而docker容器实例类似于java中new出来的实例对象。
+ **容器**：
  + 从面向对象角度：Docker利用容器（Container）独立运行的一个或一组应用，应用程序或服务运行在容器里面，容器就类似于一个虚拟化的运行环境，容器时用镜像创建的运行实例。就像是Java中的类和实例对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器为镜像提供了一个标准的和隔离的运行环境，它可以被启动、开始、停止、删除。每个容器都是相互隔离、保证安全的平台
  + 从镜像容器角度：可以把容器看作是一个简易版的Linux环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。
+ **仓库**：
  + 仓库（Repository）是集中存放镜像文件的场所
  + 仓库分为公开仓库和私有仓库两种形式
  + 最大的公开仓库是Docker Hub
  + 存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云、网易云等





# Docker平台架构

Docker是一个 C/S模式的架构，后端是一个松耦合架构，众多模块各司其职。

Docker运行的基本流程为:

1. 用户是使用Docker Client与Docker Daemon建立通信，并发送请求给后者。
2. Docker Daemon作为Docker架构中的主体部分，首先提供Docker Server的功能使其可以接受Docker client的请求。
3. Docker Engine执行Docker内部的一系列工作，每一项工作都是以一个Job的形式的存在。
4. Job的运行过程中，当需要容器镜像时，则从Docker Registry中下载镜像，并通过镜像管理驱动Graph driver将下载镜像以Graph的形式存储。
5. 当需要为docker创建网络环境时，通过网络管理驱动Network driver创建并配置Docker容器网络环境。
6. 当需要限制Docker容器运行环境或执行用户指令等操作时，则通过Exec driver来完成。
7. LIbcontainer是一项独立的容器管理包，Network Driver都是通过Libcontainer来实现具体对容器进行的操作





# Docker安装

+ 启动

  ```
  systemctl docker start
  ```

+ 测试

  ```
  查看版本信息
  docker version
  ```





# 镜像加速器配置

阿里云地址：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors





# Docker的run干了什么

![image-20221117201028612](D:\note\Docker.assets\image-20221117201028612.png)





# 为什么Docker比虚拟机快

1. docker有着比虚拟机更少的抽象层

   由于docker不需要Hypervisio(虚拟机)实现硬件资源虚拟化，运行在docker容器上的程序直接使用的都是实际物理机得硬件资源。因此在CPU、内存利用率上docker将会在效率上有明显优势。

2. docker利用的是宿主机的内核，而不需要加载操作系统OS内核

   当新建一个容器时，docker不需要和虚拟机一样重新加载一个操作系统内核，进而避免引导、加载操作系统内核返回等比较费时费资源的过程，当新建一个虚拟机时，虚拟机软件需要加载OS，返回新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统，则省略了返回过程，因此新建一个docker容器只需要几秒钟。

   ![image-20221117202627054](D:\note\Docker.assets\image-20221117202627054.png)

![image-20221117202604603](D:\note\Docker.assets\image-20221117202604603.png)







# 帮助启动类命令

+ 启动docker：systemctl start docker
+ 停止docker：systemctl stop docker
+ 重启docker：systemctl restsart docker
+ 查看docker状态：systemctl status docker
+ 开机启动：systemctl enable docker
+ 查看docker概要信息：docker info
+ 查看docker总体帮助文档：docker --help
+ 查看docker命令帮助文档：docker 具体命令 --help







# 镜像命令

+ docker images：列出本地主机上的镜像	
  + 各个选项说明：
    + REPOSITORY：表示镜像的仓库源
    + TAG：镜像的标签版本号
    + IMAGE ID：镜像ID
    + CREATED：镜像创建时间
    + SIZE：镜像大小
  + 同一个仓库可以有多个TAG版本，代表这个仓库源的不同版本，我们使用REPOSITORY：TAG来定义不同的镜像。如果你不指定一个镜像的版本标签，例如你只使用ubuntu，docker将默认使用ubuntu:latest镜像
  + OPTIONS说明：
    + -a：列出本地所有的镜像（含历史映像层）
    + -q：只显示镜像ID
+ docker search（某个镜像的名称）
  + -limit：只列出N个镜像，默认25个
+ docker pull（某个镜像的名称）：获取远程仓库的某个镜像
+ docker system df 查看镜像/容器/数据卷所占的空间
+ docker rmi（某个镜像名字ID）：删除某个镜像
  + 强制删除 -f
  + 删除单个 docker rmi -f 镜像ID
  + 删除多个 docker rmi -f 镜像名1：TAG 镜像名2：TAG
+ 面试题：谈谈docker虚悬镜像是什么？
  + 仓库名、标签都是<none>的镜像，俗称虚悬镜像dangling image





# 容器命令

前提：docker pull ubuntu

+ 新建+启动容器
+ docker run [options] image [command][ARG...]
  
  + -name="容器新名字"  为容器指定一个名称
    + -d：后台运行容器并返回容器ID，也即启动守护式容器（后台运行）
    + -i：以交互模式运行容器，通常与-t同时使用；
    + -t：为容器重新分配一个伪输入终端，通常与-i同时使用；
    + 也即启动交互式容器（前台有伪终端，等待交互）
    + -P：随机端口映射，大写P
    + -p：指定端口映射，小写p
  
+ 创建接口命令窗口
  + docker run -it ubuntu /bin/bash
    + -i:交互式操作
    + -t:终端
    + centos：centos镜像
    + /bin/bash：放在镜像名后的是命令，这里我们希望有个交互式Shell，因此用的是/bin/bash
    + 要退出终端，直接输入exit
+ 列出当前所有正在运行的容器
  + docker ps [options]
+ 退出容器
  + exit：run进去容器，exit退出，容器停止
  + ctrl+p+q：run进去容器，ctrl+p+q推出，容器不停止

+ 启动已停止运行的容器
  + docker start 容器ID或者容器名
+ 重启容器
  + docker restart 容器ID或者容器名
+ 停止容器
  + docker stop 容器ID或者容器名
+ 强制停止容器
  + docker kill 容器ID或者容器名
+ 删除已停止的容器
  + docker rm 容器ID
+ **启动守护式容器（后台服务器）**
  + 在大部分场景下，我们希望docker的服务是在后台运行的，我们可以通过-d指定容器的后台运行模式
  + docker run -d 容器名
  + redis前后台启动演示case
    + 前台交互式启动：docker run -it redis:6.0.8
    + 后台守护式启动：docker run -d redis:6.0.8

+ **查看容器日志**
  + docker logs 容器ID
+ **查看容器内运行的进程**
  + docker top 容器ID
+ **查看容器内部细节**
  + docker inspect 容器ID
+ **进入正在运行的容器并以命令进行交互**
  + docker exec -it 容器ID bash
  + 重新进入docker attach 容器ID
  + 案例演示，用centos或者ubuntu都可以
  + 上述两个区别
    + attach直接进入容器启动命令的终端，不会启动新的进程用exit退出，会导致容器的停止。
    + exec是在容器中打开新的终端，并且可以启动新的进程用exit退出，不会导致容器的停止。
  + 推荐大家使用docker exec命令，因为退出同期终端，不会导致容器的停止。
+ **从容器内拷贝文件到主机上**
  + 容器->主机
  + docker cp 容器ID:容器内路径 目的主机路径
+ **导入导出容器**
  + export 导出容器的内容流作为一个tar归档文件【对应import命令】
  + import从tar包中的内容创建一个新的文件系统再导入为镜像【对应export】
  + 案例
    + docker export 容器ID > 文件名.tar
    + cat 文件名.tar | docker import - 镜像用户/镜像名：镜像版本号





# Docker镜像的分层概念





## 镜像

是一种轻量级、可执行的独立软件包，它包含运行某个软件所需的所有内容，我们把应用程序和配置依赖打包好形成一个可交付的运行环境（包括代码、运行时需要的库、环境变量和配置文件等），这个打包好的运行环境就是image镜像文件。



只有通过这个镜像文件才能生成Docker容器实例（类似Java中new出来一个对象）



以我们的pull为例，在下载的过程中我们可以看到docker的镜像好像是在一层一层的在下载



**UnionFS（联合文件系统）**：Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite serveral directories into a single virtual filesystem)。Union文件系统是Dokcer镜像的基础。镜像可以额通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性： 一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录



## Docker镜像加载原理

docker的镜像实际上是由一层一层的文件系统组成，这种层级的文件系统UnionFS。



