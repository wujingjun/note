# Nginx安装



## 方案一：源码安装

1. 进入官网查找需要下载版本的连接地址，然后使用wget命令进行下载

```shell
wget https://nginx.org/download/nginx-1.22.0.tar.gz
```

2. 将下载的资源进行包管理

```shell
mkdir -p nginx/core
mv nginx-1.22.0.tar.gz nginx/core
```

3. 解压缩

```shell
tar -zxf nginx-1.16.1.tar.gz
```

4. 进入资源文件钟，发现configure

```shell
./configure
```

5. 编译

```shell
make
```

6. 安装

```shell
make install
```





## 方案二：yum安装

Nginx官方网站即可查看

```
https://nginx.org/en/linux_packages.html#RHEL-CentOS
```







# Nginx卸载



+ 步骤一：需要将nginx的进程关闭

```
./nginx -s stop
```

+ 步骤二：将安装的nginx进行删除

```
rm -rf /usr/local/nginx
```

+ 步骤三：将安装包之前的编译的环境清除掉

```
make clean
```

