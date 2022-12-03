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

bootfs（boot file system）主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是引导文件系统bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。



rootfs（root file system），在bootfs之上。包含的就是典型Liunx系统中的/dev，/proc，/bin，/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu,Centos等



**平时我们安装进虚拟机的Centos都是好几个G，为什么docker这里才200M？**

对于一个精简的OS，rootfs可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就行了。由此可见对于不同的Linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同发行版本可以公用bootfs。



**为什么Docker镜像要采用这种分层结构呢？**

镜像分层最大的好处就是共享资源，方便复制迁移，就是为了复用。

比如说有多个镜像都从相同的base镜像构建而来，那么Docker Host只需在磁盘上保存一份base镜像；同时内存中也只需加载一份Base镜像，就可以为所有容器服务了，而且镜像的每一层都可以被共享



**重点理解**

Docker镜像层都是只读得，容器层是可写的。

当容器启动时，一个新的可写层被加载到镜像的顶部。

这一层通常被称作“容器层“，”容器层“之下得都叫”镜像层“。



当容器启动时，一个新的可写层被加载到镜像的顶部。这一层通常被称作”容器层“，”容器层“之下的都叫”镜像层“。

所有容器的改动-无论添加、删除，还是修改文件都只会发生在容器层中。只有容器层是可写的，容器层下面所有的镜像都是可读的。



**Docker镜像commit操作实例**

+ docker commit提交容器副本使之成为一个新的镜像

+ docker commit -m=“提交的描述信息” -a="作者" 容器ID要创建的目标镜像名：【标签名】

  ```
  docker commit -m="vim配置和网络配置" -a="mie" 1ec59033bb56 mie/mie-ubuntu:0.0.1
  ```

  

Docker中的镜像分层，支持通过扩展现有镜像，创建新的镜像。类似Java继承于一个Base基础类，自己再按需扩展。

新镜像是从Base镜像一层一层叠加生成的。每安装一个软件，就在现有镜像的基础上增加一层。







# 本地镜像发布到阿里云

![image-20221121120936293](D:\note\Docker.assets\image-20221121120936293.png)





# Docker私有库简介

Docker Registry 是官方提供的工具，可以用于构建私有镜像仓库。



**将本地镜像推送到私有库**

1. 下载镜像Docker Registry
   
   1. docker pull registry
   
2. 运行私有库Registry，相当于本地有个私有Docker Hub
   
   1. docker run -d -p 5000:5000 -v /zzyyuse/myregistry --privileged=true registry
   
3. 案例演示创建一个新镜像，ubuntu安装ifconfig命令

   1. 从Hub上下载ubuntu镜像到本地并成功运行
   2. 原始的Ubuntu镜像是不带着ifconfig命令的
   3. 外网联通的情况下，安装Ifconfig命令并测试通过：apt-get install net-tools
   4. 安装完成后，commit我们自己的新镜像
      1. 公式
         1. docker commit -m = "提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]
      2. 命令
         1. 在容器外执行，记得
   5. 启动我们的新镜像并和原来的对比

4. curl验证私服库上有什么镜像

   1. curl -XGET http://localhost:5000/v2/_catalog

5. 将新镜像修改符合私服规范的Tag

   1. 按照公式:
      1. docker tag 镜像:Tag Host:Port/Repository:Tag
         1. 自己host主机IP地址
      2. 使用命令docker tag讲zzyyubuntu:1.2这个镜像修改为192.168.111.162：5000/zzyyubuntu:1.2
         1. docker tag zzyyubuntu:1.2 192.168.111.162：5000/zzyyubuntu:1.2

6. 修改配置文件使之支持Http

   1. vim /etc/docker/daemon.json

      1. 添加以下内容

         ```
         {
           "registry-mirrors": ["https://png8qy0v.mirror.aliyuncs.com"],
           "insecure-registries":["192.168.17.200:5000"]
         }
         ```

   2. 上述理由

      1. docker 默认不允许http方式推送镜像，通过配置选项来取消这个限制。===》修改后如果不生效，建议重启docker

7. push推送到私服库

   1. docker push 192.168.17.200:5000/zzyyubuntu:1.2

8. curl验证私服库上有什么镜像

   1. curl -XGET http://localhost:5000/v2/_catalog

9. pull到本地并运行

   1. docker pull 192.168.17.200:5000/zzyyubuntu:1.2







# 容器数据卷是什么



Docke挂载主机目录访问如果出现cannot open directory:Permission denied

解决方法：在挂载目录后多加一个--privileged=true参数即可

如果是centos安全模块会比之前系统版本加强，不安全的会先禁止，所以目录挂载的情况被默认为不安全的行为。

在SELinux里面挂载目录被禁止掉了，如果要开启，我们一般使用--privileged=true命令，扩大容器的权限解决挂载目录没有权限的问题，也即使用该参数，container内的root拥有真正的root权限，否则，container的root权限只是外部的一个普通用户权限。



**是什么**

一句话：有点类似于我们Redis里面的rdb和aof文件

将docker容器内的数据保存进宿主机的磁盘中

卷就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能够绕过Union File System提供一些用于持续存储或共享数据的特性。

卷的设计目的就是数据的持久化。完全独立于容器的生存周期，因此Docker不会在容器删除时删除其挂载的数据卷。



**运行一个带有容器卷存储功能的容器实例**

docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录 镜像名



**能干嘛**

将运用和运行的环境打包镜像，run后形成容器实例运行，但是我们对数据的要求希望是持久化的

Docker容器产生的数据，如果不备份，那么当容器实例删除后，容器内的数据自然也就没有了。

为了能保存数据在Docker中我们使用卷。

特点：

1. 数据卷可以在容器之间共享或重用数据
2. 卷中的更改可以直接实时生效
3. 数据卷中的更改不会包含在镜像的更新中
4. 数据卷的生命周期一直持续到没有容器使用它为止





# 容器卷和主机互通互联



## 宿主VS容器之间映射添加容器卷

**直接命令添加**

+ 命令
  + docker run -it --privileged=true -v /宿主机绝对路径目录：/容器内目录 镜像名
+ 查看数据卷是否挂载成功
+ 容器和宿主机之间数据共享



## 读写规则映射添加说明

**读写（默认）**

+ docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:rw 镜像名
+ 默认同上案例，默认就是rw

**只读**

+ 容器实例内部被限制，只能读取不能写
+ docker run -it --privileged=true -v /宿主机绝对路径：/容器内目录：ro 镜像名
  + 宿主机可以写内容，同步到容器



## 卷的继承和共享

**容器1完成和宿主机的映射**

**容器2继承容器1的卷规则**

+ docker  run it --privileged=true --volumes-from 父类 --name u2 ubuntu





# Docker上安装常用软件说明

+ 搜索镜像
+ 拉取镜像
+ 查看镜像
+ 启动镜像
  + 服务端口映射
+ 停止容器
+ 删除容器





## 安装Tomcat

+ docker hub上面查找tomcat镜像
  + docker search tomcat
+ 从docker hub 上拉取tomcat镜像到本地
  + docker pull tomcat
+ 查看是否有拉取到tomcat
  + docekr images tomcat
+ 使用tomcat镜像创建容器实例（也叫运行镜像）

  + docker run -it -p 8080:8080 tomcat

    + -p 小写，主机端口：docker 容器端口
    + -P 大写，随机分配端口
    + i:交互
    + t:终端
    + d:后台
+ 访问猫首页
  + 问题
    + 404问题
  + 解决
    + 可能没有映射端口或者没有关闭防火墙
    + 把webapps.dist目录换成webapps

​    

+ 免修改版说明
  + docker pull billygoo/tomcat8-jdk8
  + docker run -d -p 8080:8080 --name mytomcat8 billygoo/tomcat8-jdk8

​    



## 安装mysql

+ docker hub上面查找mysql镜像

+ 从docker hub上（阿里云加速器）拉取mysql镜像到本地标签为5.7

+ 使用mysql5.7镜像创建容器（也叫运行镜像）

  + 简单版

    + 使用mysql镜像
    + 建库建表插入数据
    + 外部win10也来连接运行在docker上的mysql容器实例服务
    + 问题
      + 插入中文数据试试
        + 为什么报错？
          + docker上默认字符集编码隐患
      + 删除容器后，里面的mysql数据如何办?
        + 容器实例一删除，什么都没有

  + 小坑点

    + docekr安装的mysql容器内执行命令 

    ```
    SHOW variables like 'character%'
    ```

    全是拉丁文，会导致无法插入中文字符

  + 实战版

    +  新建mysql容器实例

      + docker run -d -p 3306:3306 --privileged=true -v /zzyyuse/mysql/log:/var/log/mysql -v /zzyyuse/mysql/data:/var/lib/mysql -v /zzyyuse/mysgl/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:5.7

    + 新建my.cnf

      + 并编写以下内容

        + 路径为: /zzyyuse/mysgl/conf
    
        + ```
          [client]
          default_character_set=utf8
          [mysqld]
          collation_server=utf8_general_ci
          character_set_server=utf8
          ```
    
      + 通过容器卷同步mysql容器实例
    
    + 重新启动mysql容器给实例再重新进入并查集字符编码
    
    + 再新建库新建表再插入中文测试
    
    + 结论
    
    + 假如将当前容器实例删除，再重新来一次，之前建的db01实例还有吗？





## 安装Redis

+ 从docker hub上（阿里云加速器）拉取redis镜像到本地，标签为6.0.8
+ 命令提醒：容器记得加入--privileged=true
+ 在Centos宿主机下新建目录/app/redis
  + mkdir -p /app/redis
+ 将一个redis.conf文件模板拷贝进/app/redis目录下
+ /app/redis目录下修改reids.conf文件
  + 默认出厂的原始redis.conf
  + ![image-20221203160859380](D:\note\Docker.assets\image-20221203160859380.png)
+ 使用redis 6.0.8镜像创建容器（也叫运行镜像）
  + docker run -p 6379:6379 --name myr4 --privileged=true -v /app/redis/redis.conf:/etc/redis/redis.conf -v /app/redis/data:/data -d redis redis-server /etc/redis/redis.conf
+ 测试redis-cli连接上来
+ 请证明docker启动使用了我们自己指定的配置文件
+ 测试redis-cli连接上来第二次

