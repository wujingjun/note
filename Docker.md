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





# mysql主从复制docker版



## 主从搭建步骤

+ 新建主服务器容器实例3307
  
  + docker run -p 3307:3306 --name mysql-master  -v /mydata/mysql-master/log:/var/log/mysql -v /mydata/mysql-master/data:/var/lib/mysql -v /mydata/mysql-master/conf:/etc/mysql -e MYSQL_ROOT_PASSWORD=root -d mysql:5.7
  
+ 进入/mydata/mysql-master/conf目录下新建my.cnf

  + ```cnf
    [mysqld]
    ## 设置server_id,同一局域网中需要唯一
    server_id=101
    ## 指定不需要同步的数据库名称
    binlog-ignore-db=mysql
    ## 开启二进制日志功能
    log-bin=mall-mysql-bin
    ## 设置二进制使用内存大小（事务）
    binlog_cache_size=1M
    ## 设置使用的二进制日志格式（mixed,statement,row）
    binlog_format=mixed
    ##  二进制日志过期清理时间，默认值为0，表示不自动清理
    expire_logs_days=7
    ##  跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
    ##  如：1062错误是指一些主键错误，1032错误是因为主从数据库数据不一致
    slave_skip_errors=1062
    ```

+ 修改完配置后重启master实例

  + docker restart 容器名

+ 进入mysql-master容器

+ master容器实例内创建数据同步用户

  + create user 'slave'@'%' identified by '123456';
  + grant replication slave,replication client on * . * TO ' slave ' @ ' % '；

+ 新建从服务器容器实例3308
  
  + docker run -p 3308:3306 --name mysql-slave  -v /mydata/mysql-slave/log:/var/log/mysql -v /mydata/mysql-slave/data:/var/lib/mysql -v /mydata/mysql-slave/conf:/etc/mysql -e MYSQL_ROOT_PASSWORD=root -d mysql:5.7
  
+ 进入/mydata/mysql-slave/conf目录下新建my.cnf

  + ```
    [mysqld]
    ## 设置server_id,同一局域网中需要唯一
    server_id=102
    ## 指定不需要同步的数据库名称
    binlog-ignore-db=mysql
    ## 开启二进制日志功能,以备Slave作为其它数据库实例的Master时使用
    log-bin=mall-mysql-slave1-bin
    ## 设置二进制使用内存大小（事务）
    binlog_cache_size=1M
    ## 设置使用的二进制日志格式（mixed,statement,row）
    binlog_format=mixed
    ##  二进制日志过期清理时间，默认值为0，表示不自动清理
    expire_logs_days=7
    ##  跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
    ##  如：1062错误是指一些主键错误，1032错误是因为主从数据库数据不一致
    slave_skip_errors=1062
    ## relay_log配置中继日志
    relay_log=mall-mysql-relay-bin
    ## log_slave_updates表示slave将复制事件写进自己的二进制日志
    log_slave_updates=1
    ## slave设置为只读（具有super权限的用户除外）
    read_only=1
    ```

+ 修改完配置后重启slave实例

+ 在主数据库中查看主从同步状态

  + show master status;
    + ![image-20221206100408130](D:\note\Docker.assets\image-20221206100408130.png)

+ 进入mysql-slave容器

+ 在从数据库中配置主从复制

  + change master to master_host='宿主机ip',master_user='slave',master_password='123456',master_port=3307,master_log_file='mall-mysql-bin.000001',master_log_pos=‘上一步的Position’,master_connect_retry=30;
    + 注意master_log_pos要获取上一步的Position
    + master_host:主数据库的IP地址；
    + master_port:主数据库的运行端口
    + master_user:在主数据库创建的用于同步数据的用户账号
    + master_password:在主数据库创建的用于同步数据的用户密码
    + master_log_file:指定从数据库要复制数据的日志我呢见，通过查看主数据的状态，获取File参数
    + master_log_pos:指定从数据库从哪位置开始复制数据，通过查看主数据的状态，获取File参数；
    + master_connect_retry:连接失败重试的时间间隔，单位为秒。
  + ![image-20221206001333905](D:\note\Docker.assets\image-20221206001333905.png)

+ 在从数据库中查看主从同步状态

  + show slave status \G;

+ 在从数据库中开启主从同步

  + start slave;

+ 查看从数据库状态发现已经同步

+ 主从复制测试





# 分布式存储



1~2亿条数据需要缓存，请问如何设计这个存储案例

回答：

单机单台100%不可能，肯定是分布式存储，用redis如何落地？



## 哈希取余算法

![image-20221206165340607](D:\note\Docker.assets\image-20221206165340607.png)

2亿条记录就是2亿个kv，我们单机不行必须要分布式多机，假设有3台机器构成一个集群，用户每次读写操作都是根据公式，hashkey%N个机器台数，计算出哈希值，用来决定数据映射到哪一个节点上。

优点：

简单粗暴，直接有效，只需要预估好数据规划好节点，例如3台，8台，10台，就能保证一段时间的数据支撑，使用Hash算法让固定的一部分请求落到同一台服务器上，这样每台服务器固定处理一部分请求（并维护这些请求的信息）

缺点：

原来规划好的节点，进行扩容或者缩容就比较麻烦了，不管扩缩，每次数据变动都会导致节点变动，映射关系需要重新进行计算，在服务器个数固定不变时没有问题，如果需要弹性扩容或者故障停机的情况下，原来的取模公式就会发生变化，Hash(Key)/3会变成Hash(key)/?。此时地址经过取余运算的结果将发生很大变化，根据公式获取的服务器也会变得不可控。

某个redis机器宕机了，由于台数数量变化，会导致Hash取余全部数据重新洗牌。





## 一致性哈希算法



### 三大步骤

**一致性Hash算法背景**

​	一致性哈希算法在1997年由麻省理工学院提出的，设计的目的是为了解决分布式缓存数据变动和映射问题，某个机器单机了，分母数量改变了，自然取余数不OK了。



​	一致性哈希必然有个hash函数并按照算法产生hash值，这个算法的所有可能哈希值会构成一个全量集，这个集合可以成为一个hash空间【0，2^32-1^】，这个是一个线性空间，但是在算法中，我们通过适当的逻辑控制将它首尾相连（0=2^32^）,这样让它逻辑上形成了一个环形空间。



​	它也是按照使用取模的方法，前面笔记介绍的节点取模法是对节点（服务器）的数量进行取模，而一致性Hash算法是对2^32^取模，简单来说，一致性Hash算法将整个哈希值空间组织成一个虚拟的圆环，如假设某哈希函数H的值空间为0-2^32-1^（即哈希值是一个32位无符号整型），整个哈希环如下图，整个空间按顺时针方向组织，与那换的正上方的点代表0，0点右侧的第一个点代表1，以此类推，2、3、4、……直到2^32-1^,也就是说0点左侧的第一个点代表2^32-1^,0和2^32-1^在零点中方向重合，我们把这个由2^32个点组成的圆环称为Hash环。





**节点映射**

​	将集群中各个IP节点映射到换上的某一个位置。

​	将各个服务器使用Hash进行一个哈希，具体可以选择服务器的IP或主机名作为关键字进行哈希，这样每台机器就能确定其在哈希上的位置。例如4个节点NodeA、B、C、D，经过IP地址的哈希函数计算（hash(ip)）,使用IP地址哈希后在环空间的位置如下：

![image-20221207144001011](D:\note\Docker.assets\image-20221207144001011.png)





**key落到服务器的落键规则**

​	当我们需要存储一个kv键值对时，首先计算key的hash值，hash(key)，将这个key使用相同的函数Hash计算出哈希值并确定此数据在环上的位置，从此位置沿环顺时针“行走”，第一台遇到的服务器就是其应该定位到的服务器，并将该键值对存储在该节点上。

如我们有Object A、Object B、Object C、Object D两个数据对象，经过哈希计算后，在环空间上的位置如下：根据一致性Hash算法，数据A会被定位到NodeA上，B被定位到NodeB上，C被定位到Node C上，D被定位到Node D上。





### 优点

#### 容错性

假设Node C宕机，可以看到此时对象A、B、D不会受到影响，只有C对象被重定位到Node D。一般的，在一致性Hash算法中，如果一台服务器不可用，则受影响的数据仅仅是此服务器到其环空间中前一台服务器（即沿着逆时针方向行走遇到的第一台服务器）之间数据，其它不会受到影响。简单说，就是C挂了，收到影响的只是B、C之间的数据，并且这些数据会转移到D进行存储。



#### 扩展性

数据量增加了，需要增加一台节点NodeX,X的位置在A和B之间，那收到影响的也就是A到X之间的数据，重新把A到X的数据录入到X上即可，不会导致Hash取余全部数据重新洗牌。

![image-20221207150230720](D:\note\Docker.assets\image-20221207150230720.png)



### 缺点

**数据倾斜问题**

​	一致性Hash算法在服务节点太少时，容易因为节点分布不均匀而造成数据倾斜（被缓存的对象大部分集中缓存在某一台服务器上）问题，例如系统中只有两台服务器。





## 哈希槽

### 能干什么？

解决均匀分配的问题，在数据和节点之间又加入了一层，把这层称为哈希槽（slot），用于管理数据和节点之间的关系，现在就相当于节点上放的是槽，槽里放的是数据。

![image-20221207151647145](D:\note\Docker.assets\image-20221207151647145.png)

槽解决的是粒度问题，相当于把粒度变大了，这样便于数据移动。

哈希解决的是映射问题，使用key的哈希值来计算所在的槽，便于数据分配。



### 多少个hash槽

​	一个集群只能有16384个槽，编号0-16383（0-2^14-1^）。这些槽会分配给集群中的所有主节点，分配策略没有要求。可以指定哪些编号的槽分配给哪个主节点。集群记录节点和槽的对应关系。解决了节点和槽的关系后，接下来就需要对key求哈希值，然后对16384取余，余数是几key就落入对应的槽里。slot=CRC16(key)%16384。以槽为单位移动数据，因为槽的数目是固定的，处理起来比较容器，这样数据移动问题就解决了。

 



### 哈希槽计算

​	Redis集群中内置了16384个哈希槽，redis会根据节点数量大致均等的将哈希槽映射到不同的节点。当需要在Redis集群中放置一个key-value时，redis先对key使用crc16算法算出一个结果，然后把结果对16384求余数，这样灭个key都会对应一个编号在0-16383之间的哈希槽，也就是映射到某个节点上。如下代码，key之A、B在Node2，key之C落在Node3上。

![image-20221207155117082](D:\note\Docker.assets\image-20221207155117082.png)





# 3主3从redis集群配置



**新建6个docker容器实例**

```
docker run -d --name redis-node-1 --net  host --privileged=true -v /data/redis/share/redis-node-1:/data redis --cluster-enabled yes --appendonly yes --port 6381
```

+ docker run：创建并运行docker容器实例
+ --name redis-node-6：容器名字
+ --net host：使用宿主机的IP和端口，默认
+ --privileged=true：获取宿主机root用户权限
+ -v /data/....：容器卷，宿主机地址：docker内部地址
+ --cluster-enabled yes：开启redis集群
+ --appendonly yes：开启持久化
+ --port 6386



**进入容器redis-node-1并为6台机器构建集群关系**

+ 进入容器

  + docker exec -it redis-node-1 /bin/bash

+ 构建主从关系

  + 进入docker容器后才能执行以下命令，且注意自己的真实IP地址

  + ```
    redis-cli --cluster create
    ```

    ![image-20221207162431518](D:\note\Docker.assets\image-20221207162431518.png)

  + --cluster-replicas 1表示为每个master创建一个slave节点





**链接进入6381作为切入点，查看集群状态**

+ cluster info
+ cluster nodes







## 主从容错切换迁移案例



### 数据读写存储

+ 启动6机构成的集群并通过exec进入
+ 对6381新增两个key
  + 会报错
+ 防止路由失效加参数-c并新增两个key
  + redis-cli -p 6381 -c
+ 查看集群信息
  + redis-cli --cluster check 192.168.17.200:6381



### 主从容错切换迁移

+ 主6381和从机切换，先停止主机6381

  + docker stop redis-node-1

+ 再次查看集群信息

  + cluster nodes

  + ```
    127.0.0.1:6382> cluster nodes
    df0c9bb39dc8928433ca7ade93f6fdb2866b625f 192.168.17.200:6384@16384 slave 95d8baf1bc8044016b0f992b8e034c7c7fd45609 0 1670513856078 2 connected
    a24ffa537c8ff6a9175536b6d760ac0d93515b34 192.168.17.200:6385@16385 slave 5330623e1a80336ac08529bf3680ad5ccc677262 0 1670513855050 3 connected
    b3d3ef4873b803013c94d54a8e00db2ee32c16f6 192.168.17.200:6381@16381 master,fail - 1670513777344 1670513774275 1 disconnected
    95d8baf1bc8044016b0f992b8e034c7c7fd45609 192.168.17.200:6382@16382 myself,master - 0 1670513855000 2 connected 5461-10922
    5330623e1a80336ac08529bf3680ad5ccc677262 192.168.17.200:6383@16383 master - 0 1670513857104 3 connected 10923-16383
    7944f4ec033cb1ae803a189a32284c1cc13a2ca1 192.168.17.200:6386@16386 master - 0 1670513855000 7 connected 0-5460
    127.0.0.1:6382> 
    ```

  + 6381宕机了，6386上位成为了新的master

+ 先还原之前的3主3从

+ 查看集群状态





## 主从扩容

+ 新建6387、6388两个节点+新建后启动+查看是否8节点】
+ 进入6387容器实例内部
  + docker exec -it redis-node-7 /bin/bash
+ 将新增的6387节点（空槽号）作为master节点加入原集群
  + 将新增的6387作为master节点加入集群
  + redis-cli --cluster add-node 自己实际IP地址：6387 自己实际IP地址：6381
  + 6387就是将要作为master新增节点
  + 6381就是原来集群节点里面的领路人，相当于6387拜6381的码头从而找到组织加入集群
+ 检查集群情况第一次
+ 重新分派槽号
  + 重新分派槽号
  + redis-cli --cluster reshard IP地址：端口号
  + ![image-20221209002638549](D:\note\Docker.assets\image-20221209002638549.png)
+ 检查集群情况第二次
  + ![image-20221209002902872](D:\note\Docker.assets\image-20221209002902872.png)
  + ![image-20221209003101770](D:\note\Docker.assets\image-20221209003101770.png)
+ 为主节点6387分配从节点6388
  + redis-cli --cluster add-node ip:新slave端口 --cluster-slave --cluster-master-id 新主机节点ID
  + redis-cli --cluster add-node 192.168.17.200:6388 192.168.17.200:6387 --cluster-slave --cluster-master-id c4e1b0bcb05bf0528b1b9bc1064ab0dc1d931a1d
  + 注意最后这个是编号，需要执行以下命令获得
    + redis-cli --cluster check 192.168.17.200:6381
+ 检查集群情况第三次







## 主从缩容

+ 目的：6387和6388下线
+ 检查集群情况1获得6388的节点ID
+ redis-cli --cluster check 192.168.17.200:6381
+ 将6388删除
+ 从集群中将4号从节点6388删除
  + redis-cli --cluster del-node ip:从机端口 从机6388节点ID
  + redis-cli --cluster del-node 192.168.17.200:6388 a23b8357280757df523bc6ea9951aee6529ec25c
+ 将6387的槽号清空，重新分配，本例将清出来的槽号都给6381
  + redis-cli --cluster reshared 192.168.17.200:6381
  + ![image-20221210141227536](Docker.assets\image-20221210141227536.png)
+ 检查集群情况第二次
  + ![image-20221210141608691](Docker.assets\image-20221210141608691.png)
+ 将6387删除
  +  redis-cli --cluster del-node 192.168.17.200:6387 c4e1b0bcb05bf0528b1b9bc1064ab0dc1d931a1d
+ 检查集群情况第三次







# Docker File



## 简介



### 是什么

Dockerfile是用来构建Docker镜像的文本文件，是由一条条构建镜像所需的指令和参数构成的脚本。

![image-20221211130234765](Docker.assets\image-20221211130234765.png)



## Dockerfile构建过程解析



### Dockerfile内容基础知识

1. 每条保留字指令都必须为大写字母且后面要跟随至少一个参数
2. 指令按照从上到下，顺序执行
3. #表示注释
4. 每条指令都会创建一个新的镜像层并对镜像进行提交





### Docker执行Dockerfile的大致流程

1. docker从基础镜像运行一个容器
2. 执行一条指令并对容器作出修改
3. 执行类似docker commit的操作提交一个新的镜像层
4. docker再基于刚提交的镜像运行一个新容器
5. 执行dockerfile中的下一条指令直到所有指令都执行完成



从应用软件的角度来看，Dockerfile、Docker镜像与Docker容器分别代表软件的三个不同的阶段

+ Dockerfile是软件的原材料
+ Docker镜像是软件的交付品
+ Docker容器则可以认为是软件镜像的运行态，也即依照镜像运行的容器实例

Dockerfile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可，合力充当Docker体系的基石。

![image-20221211132312451](Docker.assets\image-20221211132312451.png)

1. Dockerfile，需要顶一个Dockerfile，Dockerfile定义了进程需要的一切东西。Dockerfile涉及的内容暴扣哦执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、防护进程和内核进程（当应用进程需要和系统服务和内核进程打交道，这时需要考虑如何涉及namespace的权限控制的权限控制）等等；
2. Docker镜像，在用Dockerfile定义一个文件之后，docker build时会产生一个Docker镜像，当运行Docker镜像时会真正开始提供服务；
3. Docker容器，容器是直接提供服务的







## DockerFile常用保留字指令



### FROM

基础镜像，当前新镜像是基于哪个镜像的，指定一个已经存在的镜像作为模板。第一条必须是from



### MAINTAINER

镜像维护者的姓名和邮箱地址



### RUN

+ 容器构建时需要运行的命令
+ 两种格式
  + shell格式
  + exec格式
+ RUN是在docker build时运行



### EXPOSE

当前容器对外暴露出的端口



### WORKDIR

指定在创建容器后，终端默认登陆的进来工作目录，一个落脚点



### USER

指定该镜像以什么样的的用户去执行，如果不指定，默认是root



### ENV

用来在构建镜像过程中设置环境变量（申请变量）

+ ENV MY_PATH/usr/mytest
  + 这个环境变量可以在后续的任何RUN指令中使用，这就如同在命令前面指定了环境变量前缀一样；也可以在其它指令中直接使用这些环境变量
  + 比如 WORDDIR $MY_PATH



### VOLUME

容器数据卷，用于数据保存和持久化工作



### ADD

将宿主机目录下的文件拷贝进镜像且会自动处理URL和解压tar压缩包



### COPY

+ 类似ADD，拷贝文件和目录到镜像中。

+ 将从构建上下文目录中<源路径>的文件/目录复制到新的一层的镜像内的<目标路径>位置
  + COPY src test
  + COPY ["src","dest"]
  + <src源路径>：源文件或者源目录
  + <dest目标路径>：容器内的指定路径，该路径不用事先建好，路径不存在的话，会自动创建。





### CMD

+ 指定容器要干的事情
  + CMD容器启动命令
    + CMD指令的格式和RUN相似，也是两种格式：
      + shell格式：CMD命令
      + exec格式：CMD ["可执行文件"，"参数1"，"参数2"...]
      + 参数列表格式：CMD["参数1"，"参数2"...]，在指定了ENTRYPOINT指令后，用CMD指定具体的参数。
+ 注意
  + Dockerfile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run之后的参数替换
    + 例子 docker run -it-p 8080:8080 tomcat镜像ID /bin/bash
    + 执行上述命令无法启动tomcat
+ 与RUN命令的区别
  + CND是在docker run时执行
  + RUN是在docker build时运行



### ENTRYPOINT

+ 也是用来指定一个容器启动时要运行的命令
+ 类似于CMD指令，但是ENTRYPOINT不会被docker run后面的命令覆盖，而且这些命令行参数会被当做参数送给ENTRYPOINT指定指定的程序
+ 命令格式和案例说明
  + 命令格式：ENTRYPOINT ["<executeable>","<param1>","<param2>",...]
  + ENTRYPOINT可以和CMD一起用，一般是变参才会使用CMD，这里的CMD等于是在给ENTRYPOINT传参
  + 当指定了ENTRYPOINT后，CMD的含义就发生了变化，不再是直接运行其命令而是将CMD的内容工作为参数传递给ENTRYPOINT指令，他两个组合会变成<ENTRYPOINT> "<CMD>"
  + ![image-20221211142442276](Docker.assets\image-20221211142442276.png)
+ 优点
+ 注意







## Dockerfile需求说明

+ 下载JDK
  + 进入oracle网址
  + https://www.oracle.com/java/technologies/javase/javase8u211-later-archive-downloads.html

+ 要求
  
  + centos7镜像具备vim+ifconfig+jdk8
  
+ 编写
  + 准备编写Dockerfile文件
    
    + 大写字母D
    
    + 编写内容
    
    + ```
      FROM centos
      MAINTAINER miejiang<824573518@qq.com>
      
      ENV MYPATH /usr/local
      WORKDIR $MYPATH
      
      #安装VIM编辑器
      RUN yum -y install vim
      #安装ifconfig命令查看网络IP
      RUN yun -y install net-tools
      #安装 java8及lib库
      RUN yum -y install glibc.i686
      RUN mkdir /usr/local/java
      #ADD 是相对路径jar，把jdk-8u171-linux-x64.tar.gz添加到容器中，安装包必须要和Dockerfile文件在同一位置
      ADD jdk-8u171-linux-x64.tar.gz /usr/local/java/
      #配置java环境变量
      ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
      ENV JRE_HOME $JAVA_HOME/jre
      ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
      ENV PATH $JAVA_HOME/bin:$PATH
      
      EXPOSE 80
       
      CMD echo $MYPATH
      CMD echo "success--------------ok"
      CMD /bin/bash
      ```
  
+ 构建
  + docker build -t新镜像名字:TAG .
  + 注意,上面TAG后面有个空格，有个点 
  
+ 运行

+ 再体会下UnionFS(联合文件系统)

  + Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下（Unite several directories into a single virtual filesystem）。Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。
  + 特性：一次同时加载多个文件系统，但从外面看起来，智能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件







## 虚选镜像

构建的时候或者删除镜像的时候出现错误

没有名称、标签的镜像，没有任何价值

使用以下命令进行删除

```
docker image prune
```







## Dockerfile发布微服务部署到docker容器

+ IDEA工具里面搞定微服务jar包

+ 编写Dockerfile

  + Dockerfile内容

    + ```
      # 基础镜像使用Java
      FROM java:8
      # 作者
      MAINTAINER MIE_JIANG
      # VOLUME 指定临时文件目录为/tmp。在主机/var/lib/docker目录下创建了一个临时文件并连接到容器的/tmp
      VOLUME /tmp
      # 将jar包添加到容器中并更名为miejiang_docker.jar
      ADD dockerDemo-0.0.1-SNAPSHOT.jar miejiang_docker.jar
      # 运行jar包
      RUN bash -c 'touch /miejiang_docker.jar'
      ENTRYPOINT ["java","-jar","/miejiang_docker.jar"]
      # 暴露6001端口作为服务
      EXPOSE 6001
      ```

  + 将微服务jar包和Dockerfile文件上传到同一个目录下 /mydocker

+ 构建镜像

  + docker build -t miejiang_docker:0.0.2 .
  + 打包成镜像文件

+ 运行容器

+ 访问





# Docker Network





## 简介

+ 是什么
  + docker不启动，默认网络情况
    + ens33
    + lo
    + virbr0
      + 在Centos7的安装过程中如果有选择相关虚拟化的服务安装系统后，启动网卡时会发现有一个以网桥联机的私网地址的virbr0网卡（virbr0网卡，它还有一个固定的默认IO地址192.168.122.1），是做虚拟机网桥的使用的，其作用是为连接其上的虚拟机网卡提供NAT访问外网的功能。
      + 我们之前学习Linux安装，勾选安装系统的时候附带了libvirt服务才会生成的一个东西，如果不需要可以直接将libvirtd服务卸载。
        + yum remove libvirt-libs.x86_64
  + docker启动后，网络情况
    + 查看docker网络模式命令



+ 常用基本命令
  + ALL命令[首页](https://www.bilibili.com/)-
  + 查看网络
    + docker network ls
  + 查看网络源数据
    + docker network inspect xxx网络名字
  + 删除网络
    + docker network rm XXX网络名字
  + 案例
+ 能干嘛
  + 容器间的互联和通信以及端口映射l
  + 容器IP变动时候可以通过服务名直接网络通信而不受到影响
+ 网络模式
  + 总体介绍
    + bridge：为每一个容器分配、设置IP等，并将容器连接到一个docker0。虚拟网桥，默认为该模式
      + 使用--network bridge指定，默认使用docker0
    + host：容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。
      + 使用--network host指定
    + none：容器有独立的Network namespace，但并没有对其进行任何网络设置，如分配veth pair和网桥连接，IP等。
      + 使用--network none指定
    + container：新创建的容器不会创建自己的网卡和配置自己的IP，而是和一个指定的容器共享IP，端口范围等
      + 使用--network container:NAME或者容器ID指定
  + 容器实例内默认网络IP生产规则
    
    + docker容器内部的IP是有可能会发生改变的
  + 案例说明
    + bridge
      + 是什么？
        + Docker服务默认会创建一个docker0网桥（其上有一个docker0内部接口），该桥接网络的名称为docker0，它在内核层联通了其它的物理或虚拟网卡，这就将所有容器和本地主机都放到同一个物理网络。Docker默认指定了docker0接口的IP地址和子网掩码，让主机和容器之间可以通过网桥相互通信。
        + 查看bridge网络的详细信息，并通过grep获取名称项
          + docker network inspect bridge | grep name
          + ifconfig
      + 案例
        + 说明
          + ![image-20221213232824465](D:\note\Docker.assets\image-20221213232824465.png)
          + Docker使用Linux桥接，在宿主机虚拟一个Docker容器网桥（docker0）,Docker启动一个容器时会根据Docker网桥的网段分配给容器一个IP地址，称为Container-IP，同时Docker网桥是每个容器的默认网关。因为在同一宿主机内的容器都会接入同一个网桥，这样容器之间就能够通过容器的Container-IP直接通信。
          + docker run的时候，没有指定network的话默认使用的网桥模式就是bridge，使用的就是docker0.在宿主机ifconfig，就可以看到docker0和自己create的network（后面讲）eth0，eth1，eth2...代表网卡一，网卡二，网卡三...，lo代表127.0.0.1，即localhost，inet addr用来表示网卡的IP地址
          + 网桥docker0创建一对对等虚拟设备接口一个叫veth,另一个叫eth0,成对匹配。
            + 整个宿主机的网桥模式都是docker0,类似一个交换机有一堆接口，每个接口叫veth，在本地主机和容器内分别创建一个虚拟接口，并让他们彼此联通（这样一对接口叫veth pair）;
            + 每个容器实例内部也有一个网卡，每个接口叫eth0
            + docker0上面的每个veth匹配某个容器实例内部的eth0,两两配对，一一匹配。
          + 通过上述，将宿主机上的所有容器都连接到这个内部网络上，两个容器在同一个网络下，会从这个网关下各自拿到分配的ip，此时两个容器的网络是互通的。
        + 代码
          + docker run -d -p 8081:8080 --name tomcat81 billygoo/tomcat8-jdk8
          + docekr run -d -p 8082:8080 --name tomcat82 billygoo/tomcat8-jdk8
          + docker容器都是会有eth0，宿主机内查看会多了两个veth,数字就看前方和结尾
      
    + host
      + 说明
        + 容器将不会获得一个独立的Network Namespace，而是和宿主机共用一个Network Namespace。容器将不会虚拟出自己的网卡而是使用宿主机的IP和端口。
        + ![image-20221213235309609](D:\note\Docker.assets\image-20221213235309609.png)
        + 代码
          + 警告
            + docker run -d -p 8083:8080 --network host --name tomcat83 billygoo/tomcat-jdk8
            + ![image-20221214204600649](D:\note\Docker.assets\image-20221214204600649.png)
            + 问题
              + docker启动时总是遇见标题中的警告
            + 原因
              + docker启动时指定network=host或-net=host，如果还指定了-p映射端口，那这个时候就会有此警告，并且通过-p设置的参数将不会起到任何作用，端口号会以主机端口号为主，重复时则递增。
            + 解决
              + 解决的办法就是使用docker的其它网络模式，例如--network=bridge，这样就可以解决问题，或直接无视
          + 正确
            + docker run -d  --network host --name tomcat83 billygoo/tomcat-jdk8
        + 无之前的配对显示了，看容器实例内部
        + 没有设置-p的端口映射了，如何访问启动的tomcat83?
          + 就是宿主机的ip+默认端口
      
    + none

      + 是什么
        + 禁用网络功能，只有lo标识（就是127.0.0.1表示本地回环）
        + 在none模式下，并不为Docker容器进行任何网络配置
        + 也就是说，这个Docker容器没有网卡、IP、路由等信息，只有一个lo
        + 需要我们自己为Docker容器添加网卡、配置IP等
      + 案例
        + docker run -d -p 8084:8080 --network none -name tomcat84 billygoo/tomcat-jdk8

    + container

      + 是什么
        + 新建的容器和已经存在的一个容器共享一个网络ip配置而不是和宿主机共享。新创建的容器不会创建自己的网卡，配置自己的IP，而是和一个指定的容器共享IP、端口范围等。同样，两个容器除了网络方面，其它的如文件系统、进程列表还是隔离的。
        + ![image-20221214210529364](D:\note\Docker.assets\image-20221214210529364.png)
      + 案例1
        + docker run -d -p 8085:8080 --name tomcat85 billygoo/tomcat8-jdk8
        + docker run -d -p 8086:8080 --network container:tomcat85 --name tomcat86 billygoo/tomcat8-jdk8
        + 运行结果
          + ![image-20221214210931845](D:\note\Docker.assets\image-20221214210931845.png)
          + 相当于tomcat85和tomcat86公用同一个ip同一个端口，导致端口冲突
      + 案例2
        + Alpine操作系统是一个面向安全的轻型linux发行版
          + Apline Linux是一款独立的、非商业的通用linux发行版，专为追求安全性、简单性和资源效率的用户而设计。可能很多人没听说过这个Linux发行版本，但是经常用Docker的朋友可能都用过，因为他小，简单，安全著称，所以作为基础镜像是非常好的一个选择，可谓是麻雀虽小但五脏俱全，镜像非常小巧，不到6M的大小，所以特别适合容器打包。
        + docker run -it --name alpine1 alpine /bin/sh
        + docker run -it  --network container:alpine2 --name alpine2 alpine /bin/sh
        + 运行结果，验证共用搭桥
        + 假如此时关闭alpine1,再看看alpine2

    + 自定义网络

      + 自定义桥接网络，自定义网络默认使用的是桥接网络bridge

      + 新建自定义网络
        + docker network ls
        + docker network create zzyy_network
      + 新建容器加入上一步新建的自定义网络
        + docker run -d -p 8081:8080 --network zzyy_network --name tomcat81 billygoo/tomcat8-jdk8
        + docker run -d -p 8082:8080 --network zzyy_network --name tomcat82 billygoo/tomcat8-jdk






# Docker-Compose



## 是什么

Docker-Compose是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。

Compose是Docker公司推出的一个工具软件，可以管理多个Docker容器组成一个应用，你需要定义一个YAML格式的配置文件docker-compose.yml，写好多个容器之间的调用关系。然后，只要一个命令，就能同时启动/关闭这些容器。







## 能干嘛

Docker建议我们每一个容器中只运行一个服务，因为Docker容器本身占用资源极少，所以最好是将每个服务单独的分割开来但是我们又面临了一个问题？

如果我们需要同时部署好多个服务，难道要每个服务单独写Dockerfile然后再构建镜像，构建容器，这样累都累死了，所以Docker官方给我们提供了docker-compose多服务部署的工具

Compose允许用户通过一个单独的docker-compose.yml模板文件（YAML格式）来定义一组相关联的应用容器为一个项目（project）。

可以很容易地用一个配置文件定义一个多容器的应用，然后使用一条指令安装这个应用的所有依赖，完成构建。Docker-Compose解决了容器与容器之间如何管理编排的问题。



## 下载安装

+ 下载地址：https://docs.docker.com/compose/install/other/
+ 赋予权限：chmod +x /usr/local/bin/docker-compose
+ 测试：docker-compose --version



## 核心概念



### 一文件

docker-compose.yml



### 两要素

+ 服务（service）
  + 一个个应用容器实例，比如订单微服务、库存微服务，mysql容器
+ 工程（project）
  + 由一组关联的应用容器组成的一个完整业务单元，在docker-compose.yml文件中定义。
  + 



## Compose使用的三个步骤

+  编写Dockerfile定义各个微服务应用并构建出对应的镜像文件
+ 使用docker-compose.yml定义一个完整业务单元，安排好整体应用中的各个容器服务
+ 最后，执行docker-compose up命令来启动并运行整个应用程序，完成一键部署上线







### 使用Compose

+ 服务编排，一套带走，安排

+ 编写docker-compose.yml文件

  + ```yaml
    version: "3"
     
    services:
      microService:
        image: zzyy_docker:1.6
        container_name: ms01
        ports:
          - "6001:6001"
        volumes:
          - /app/microService:/data
        networks: 
          - atguigu_net 
        depends_on: 
          - redis
          - mysql
     
      redis:
        image: redis:6.0.8
        ports:
          - "6379:6379"
        volumes:
          - /app/redis/redis.conf:/etc/redis/redis.conf
          - /app/redis/data:/data
        networks: 
          - atguigu_net
        command: redis-server /etc/redis/redis.conf
     
      mysql:
        image: mysql:5.7
        environment:
          MYSQL_ROOT_PASSWORD: '123456'
          MYSQL_ALLOW_EMPTY_PASSWORD: 'no'
          MYSQL_DATABASE: 'db2021'
          MYSQL_USER: 'zzyy'
          MYSQL_PASSWORD: 'zzyy123'
        ports:
           - "3306:3306"
        volumes:
           - /app/mysql/db:/var/lib/mysql
           - /app/mysql/conf/my.cnf:/etc/my.cnf
           - /app/mysql/init:/docker-entrypoint-initdb.d
        networks:
          - atguigu_net
        command: --default-authentication-plugin=mysql_native_password #解决外部无法访问
     
    networks: 
       atguigu_net: 
    ```

+ 第二次修改微服务工程docker_boot

  + 修改yml文件，通过服务名访问，IP无关
  + mvn package命令将微服务形成新的Jar包并上传到Linux服务器/mydocekr目录下
  + 编写Dockerfile
  + 构建镜像
    + docker build -t zzyy_docker:1.6 .

+ 执行docker-compose up 或者 执行 docker-compose up -d

+ 进入mysql容器实例并新建库db2021+新建表t_user

+ 测试通过

+ Compose常用命令

  + ```
    docker-compose -h                           # 查看帮助
    docker-compose up                           # 启动所有docker-compose服务
    docker-compose up -d                        # 启动所有docker-compose服务并后台运行
    docker-compose down                         # 停止并删除容器、网络、卷、镜像。
    docker-compose exec  yml里面的服务id                 # 进入容器实例内部  docker-compose exec docker-compose.yml文件中写的服务id /bin/bash
    docker-compose ps                      # 展示当前docker-compose编排过的运行的所有容器
    docker-compose top                     # 展示当前docker-compose编排过的容器进程
     
    docker-compose logs  yml里面的服务id     # 查看容器输出日志
    dokcer-compose config     # 检查配置
    dokcer-compose config -q  # 检查配置，有问题才有输出
    docker-compose restart   # 重启服务
    docker-compose start     # 启动服务
    docker-compose stop      # 停止服务
    ```

  + 

+ 关停





# Docker-Portainer



## 是什么

Portainer是一款轻量级的应用，它提供了图形化界面，用于方便地管理Docker环境，包括单机环境和集群环境。



## 安装

+ docker 命令安装
  + docker run -d -p 8000:8000 -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock -vportainer_data:/data  portainer/portainer
+ 第一次登陆需创建admin，访问地址：xxx.xxx.xxx:9000
+ 设置admin用户和密码后首次登陆
+ 选择local选项后本地docker详细信息展示
+ 上一步的图形展示，能想得起对应命令吗？
  + docker system df







# Docker容器监控





## 原生命令

通过docker stats命令可以很方便的看到当前宿主机上所有容器的CPU，内存以及网络流量等。

但是，docker stats统计结果只能是当前宿主机的全部容器，数据资料是实时的，没有地方存储、没有健康指标过线预警等功能



## 是什么



### 容器监控3剑客

CAdvisor监控收集+InfluxDB存储数据+Granfana展示图表





#### CAdvisor

CAdvisor是一个容器资源监控工具包括容器的内存,CPU,网络I0,磁盘I0等监控,同时提供了一个WEB页面用于查看容器的实时运行状态。CAdvisor默认存储2分钟的数据,而且只是针对单物理机。不过，CAdvisor提供了很多数据集成接口,支持InfluxDB,Redis,Kafka,Elasticsearch等集成,可以加上对应配置将监控数据发往这些数据库存储起来。



CAdvisor功能主要有两点：

+ 展示Host和容器两个层次的监控数据
+ 展示历史变化数据



#### InfluxDB

lnfluxDB是用Go语言编写的一个开源分布式时序、事件和指标数据库,无需外部依赖。
CAdvisor默认只在本机保存最近2分钟的数据，为了持久化存储数据和统一收集展示监控数据，需要将数据存储到InfluxDB中。InfluxDB是一个时序数据库,专门用于存储时序相关数据，很适合存储CAdvisor的数据。而且，CAdvisor本身已经提供了InfluxDB的集成方法，丰启动容器时指定配置即可。



InfluxDB主要功能：

+ 基于时间序列，支持与时间有关的相关函数（如最大、最小、求和等）；
+ 可度量性：你可以实时对大量数据进行计算
+ 基于事件：它支持任意的事件数据



#### Granfana

Grafana是一个开源的数据监控分析可视化平台，支持多种数据源配置（支持的数据源包括InfluxDB,MySQL，Elasticsearch，OpenTSDB，Graphite等）和丰富的插件及模板功能，支持图表权限控制和报警。



Granfana主要特性：

+ 灵活丰富的图形化选项
+ 可以混合多种风格
+ 支持白天和夜间模式
+ 多个数据源





### Compose容器编排，一套带走

+ 新建目录

  + mkdir /mydocker/cig

+ 新建3件套组合的docker-compose.yml

  + ```yaml
    version: '3.1'
    
    volumes:
      grafana_data: {}
    
    services:
      influxdb:
        image: tutum/influxdb:0.9
        restart: always
        environment:
          - PRE_CREATE_DB=cadvisor
        ports:
          - "8083:8083"
          - "8086:8086"
        volumes:
          - ./data/influxdb:/data
    
      cadvisor:
        image: google/cadvisor
        links:
          - influxdb:influxsrv
        command: -storage_driver=influxdb -storage_driver_db=cadvisor -storage_driver_host=influxsrv:8086
        restart: always
        ports:
          - "8080:8080"
        volumes:
          - /:/rootfs:ro
          - /var/run:/var/run:rw
          - /sys:/sys:ro
          - /var/lib/docker/:/var/lib/docker:ro
    
      grafana:
        user: "104"
        image: grafana/grafana
        restart: always
        links:
          - influxdb:influxsrv
        ports:
          - "3000:3000"
        volumes:
          - grafana_data:/var/lib/grafana
        environment:
          - HTTP_USER=admin
          - HTTP_PASS=admin
          - INFLUXDB_HOST=influxsrv
          - INFLUXDB_PORT=8086
          - INFLUXDB_NAME=cadvisor
          - INFLUXDB_USER=root
          - INFLUXDB_PASS=root
    ```

    + 使用命令检查文件填写是否正确
      + docker-compose config -q

+ 启动通过docker-compose文件

  + docker-compose up -d 后台启动

+ 查看三个服务容器是否启动

  + 浏览cAdvisor收集服务，http://ip:8080/
  + 浏览influxdb存储服务，http://ip:8083/
  + 浏览grafana展现服务，http://ip:3000/
    + 配置步骤
      + 配置数据源
      + 选择influxdb数据源
      + 配置细节
      + 配置面板panel

+ 测试





































