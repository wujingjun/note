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



# Nginx核心配置文件结构





## 全局块



### user指令

1. user:用于配置运行Nginx服务器的worker进程的用户和用户组。

| 语法   | user user[group] |
| ------ | ---------------- |
| 默认值 | nobody           |
| 位置   | 全局块           |

该属性也可以在编译的时候指定，语法如下`./configure --user=use--group=group`，如果两个地方都进行了设置，最终生效的是配置文件中的配置。

该指令的使用步骤:

1. 在nginx.conf配置文件全局块中设置一个用户信息"www"

```
user lcn;
```

```
[root@james sbin]# ./nginx -t
nginx: [emerg] getpwnam("lcn") failed in /usr/local/nginx/conf/nginx.conf:2
nginx: configuration file /usr/local/nginx/conf/nginx.conf test failed
```

2. 创建一个用户

```
useradd lcn
```

3. 创建`/root/html/index.html`页面。添加如下内容

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
<p><em>I am lcn</em></p>
</body>
</html>
```

5. 修改nginx.conf

```
location / {
	root   /root/html;
	index  index.html index.htm;
}
```

6. 测试启动访问

页面会报403拒绝访问的错误

7. 分析原因

因为当前和用户没有访问/root/html目录的权限

8. 将文件创建到`/home/lcn/html/index.html`修改配置

```
location / {
	root   /home/lcn/html;
	index  index.html index.htm;
}
```

综上所述，使用user指令可以指定启动运行工作进程的用户及用户组，这样对于系统的权限范围跟控制的更加精细，也更加安全。