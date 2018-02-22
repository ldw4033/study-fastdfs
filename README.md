## 系统必要的依赖

```
yum install make cmake gcc gcc-c++
yum install -y lrzsz
```
## 安装 libfastcommon 和 FastDFS

### 下载libfastcommon

[https://github.com/happyfish100/libfastcommon/releases/tag/V1.0.36](https://github.com/happyfish100/libfastcommon/releases/tag/V1.0.36)

[https://github.com/happyfish100/libfastcommon/archive/V1.0.36.tar.gz](https://github.com/happyfish100/libfastcommon/archive/V1.0.36.tar.gz)

或者


```
cd /usr/local/src
wget https://github.com/happyfish100/libfastcommon/archive/V1.0.36.tar.gz
```


### 解压 libfastcommon

```
tar -zxvf libfastcommon-1.0.36.tar.gz
```

### 编译

```
cd libfastcommon-1.0.36
./make.sh
```
### 安装

```
./mark.sh install
```

libfastcommon 默认安装到了

/usr/lib64/libfastcommon.so

/usr/lib64/libfdfsclient.so

因为 FastDFS 主程序设置的 lib 目录是/usr/local/lib， 所以需要创建软链接.

```

ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
```


### 下载fastDFS

[https://github.com/happyfish100/fastdfs/releases/tag/V5.11](https://github.com/happyfish100/fastdfs/releases/tag/V5.11)
    
[https://github.com/happyfish100/fastdfs/archive/V5.11.tar.gz](https://github.com/happyfish100/fastdfs/archive/V5.11.tar.gz)

 或者
 
```
wget https://github.com/happyfish100/fastdfs/archive/V5.11.tar.gz
```

### 解压
```
tar -zxvf fastdfs-5.11.tar.gz
```
### 编译
```
cd fastfds-5.11
./make.sh
```
### 安装

```
./mark.sh install
```

### 采用默认安装的方式安装,安装后的相应文件与目录：
- A、 服务脚本在：

```
/etc/init.d/fdfs_storaged
/etc/init.d/fdfs_tracker
```
可以进入
**/user/bin** 目录使用以下命令查看 fdfs 的相关命令：

```
# cd /usr/bin/
# ls | grep fdfs
```


- B、 配置文件在（样例配置文件） :

```
/etc/fdfs/client.conf.sample
/etc/fdfs/storage.conf.sample
/etc/fdfs/tracker.conf.sample
```

- C、 命令工具在/usr/bin/目录下的：

```
fdfs_appender_test
fdfs_appender_test1
fdfs_append_file
fdfs_crc32
fdfs_delete_file
fdfs_download_file
fdfs_file_info
fdfs_monitor
fdfs_storaged
fdfs_test
fdfs_test1
fdfs_trackerd
fdfs_upload_appender
fdfs_upload_file
stop.sh
restart.sh
```

## 配置 FastDFS 跟踪器

### 复制 FastDFS 跟踪器样例配置文件,并重命名:

```
# cd /etc/fdfs/
# cp tracker.conf.sample tracker.conf
```

### 编辑跟踪器配置文件：

```
# vi /etc/fdfs/tracker.conf
```

修改的内容如下：

```
disabled=false #默认开启 
port=22122 #默认端口号
base_path=/data/fastdfs/tracker #下面要创建的目录
http.server_port=8080 #默认端口是8080
```
> （其它参数保留默认配置， 具体配置解释请参考官方文档说明：
> http://bbs.chinaunix.net/thread-1941456-1-1.html ）

### 创建基础数据目录（参考基础目录 base_path 配置） :

```
# mkdir -p /data/fastdfs/tracker
```

###  启动 Tracker：

```
# /etc/init.d/fdfs_trackerd start
```

（初次成功启动，会在/fastdfs/tracker 目录下创建 data、 logs 两个目录）

### 查看 FastDFS Tracker 是否已成功启动：

```
# ps -ef | grep fdfs
```

### 关闭 Tracker：

```
# /etc/init.d/fdfs_trackerd stop
```

### 设置 FastDFS 跟踪器开机启动：

```
# vi /etc/rc.d/rc.local
```

添加以下内容：

```
## FastDFS Tracker
/etc/init.d/fdfs_trackerd start
```

## 配置 FastDFS 存储
### 复制 FastDFS 存储器样例配置文件,并重命名:

```
# cd /etc/fdfs/
# cp storage.conf.sample storage.conf
```

编辑存储器样例配置文件：

```
# vi /etc/fdfs/storage.conf
```

修改的内容如下:

```
disabled=false
group_name=group1 #组名，根据实际情况修改 
port=23000 #设置storage的端口号，默认是23000，同一个组的storage端口号必须一致 
base_path=/data/fastdfs/storage #设置storage数据文件和日志目录 
store_path_count=1 #存储路径个数，需要和store_path个数匹配 
store_path0=/data/fastdfs/storage_data  #实际文件存储路径
tracker_server=192.168.4.121:22122 #跟踪器的地址，单台机器就是本机ip
http.server_port=8888
```
> 注意：这里有一个坑，我在本地测试tracker与storage在一台机器上，所以把IP配成了127.0.0.1，结果导致后续服务启动报错！！！

> （其它参数保留默认配置， 具体配置解释请参考官方文档说明：
> http://bbs.chinaunix.net/thread-1941456-1-1.html ）

### 创建基础数据目录（参考基础目录 base_path 配置） :

```
mkdir -p /data/fastdfs/storage
mkdir -p /data/fastdfs/storage_data 
```


### 启动 Storage：

```
# /etc/init.d/fdfs_storaged start
```

（初次成功启动，会在/data/fastdfs/storage 目录下创建 data、 logs 两个目录）

### 查看 FastDFS Storage 是否已成功启动

```
# ps -ef | grep fdfs
```
### 校验整合
```
/usr/bin/fdfs_monitor /etc/fdfs/storage.conf
```
成功后可以看到： 
ip_addr = 192.168.128.131 (localhost.localdomain) ACTIVE

### 关闭 Storage：

```
# /etc/init.d/fdfs_storaged stop
```

### 设置 FastDFS 存储器开机启动：

```
# vi /etc/rc.d/rc.local
```

添加：

```
## FastDFS Storage
/etc/init.d/fdfs_storaged start
```





## 文件上传测试

修改 Tracker 服务器中的客户端配置文件：

```
# cp /etc/fdfs/client.conf.sample /etc/fdfs/client.conf
# vi /etc/fdfs/client.conf
```

```
base_path=/data/fastdfs/tracker #tracker服务器文件路径
tracker_server=192.168.4.121:22122 #tracker服务器IP地址和端口号
http.tracker_server_port=8080 # tracker 服务器的 http端口号，必须和tracker的设置对应起来
```

2、 执行如下文件上传命令：

```
# /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/FastDFS_v5.05.tar.gz
```

返回 ID 号： group1/M00/00/00/wKgEfVUYNYeAb7XFAAVFOL7FJU4.tar.gz
（能返回以上文件 ID， 说明文件上传成功）


## 在每个存储节点上安装 nginx
> fastdfs-nginx-module 作用说明<p>FastDFS通过Tracker服务器,将文件放在Storage服务器存储，但是同组存储服务器之间需要进入文件复制，有同步延迟的问题。<p>假设Tracker服务器将文件上传到了192.168.4.125，上传成功后文件 ID已经返回给客户端。此时 FastDFS 存储集群机制会将这个文件同步到同组存储192.168.4.126，在文件还没有复制完成的情况下，客户端如果用这个文件 ID 在 192.168.4.126 上取文件,就会出现文件无法访问的错误。<p>而 fastdfs-nginx-module 可以重定向文件连接到源服务器取文件,避免客户端由于复制延迟导致的文件无法访问错误。<p>（解压后的 fastdfs-nginx-module 在 nginx 安装时使用） 

### 上传 fastdfs-nginx-module_v1.16.tar.gz 到/usr/local/src

下载地址：[https://github.com/happyfish100/fastdfs-nginx-module](https://github.com/happyfish100/fastdfs-nginx-module)

### 解压

```
# cd /usr/local/src/
# tar -zxvf fastdfs-nginx-module_v1.16.tar.gz
```

### ~~修改 fastdfs-nginx-module 的 config 配置文件~~（官方已修复）

```
# cd fastdfs-nginx-module/src
# vi config
```


```
CORE_INCS="$CORE_INCS /usr/local/include/fastdfs /usr/local/include/fastcommon/"
修改为：
CORE_INCS="$CORE_INCS /usr/include/fastdfs /usr/include/fastcommon/"
```

> （注意： 这个路径修改是很重要的，不然在 nginx 编译的时候会报错的）

### 上传当前的稳定版本Nginx(nginx-1.12.2.tar.gz)到/usr/local/src 目录

### 安装编译 Nginx 所需的依赖包

```
# yum install gcc gcc-c++ make automake autoconf libtool pcre* zlib openssl openssl-devel
```

编译安装 Nginx（添加 fastdfs-nginx-module 模块）

```
# cd /usr/local/src/
# tar -zxvf nginx-1.12.2.tar.gz
# cd nginx-1.12.2

# ./configure --prefix=/usr/local/nginx --add-module=/usr/local/src/fastdfs-nginx-module-master/src/

# make && make install
```

### 复制 fastdfs-nginx-module 源码中的配置文件到/etc/fdfs 目录， 并修改


```
# cp /usr/local/src/fastdfs-nginx-module/src/mod_fastdfs.conf  /etc/fdfs/
# vi /etc/fdfs/mod_fastdfs.conf
```

修改以下配置：

```
connect_timeout=10
base_path=/tmp
tracker_server=192.168.4.121:22122  #tracker服务器的IP地址以及端口号
storage_server_port=23000  #storage服务器的端口号
group_name=group1
url_have_group_name = true #文件 url 中是否有 group 名
store_path0=/data/fastdfs/storage_data #存储路径
```


### 复制 FastDFS 的部分配置文件到/etc/fdfs 目录

```
# cd /usr/local/src/FastDFS/conf
# cp http.conf mime.types /etc/fdfs/
```

10、 在/data/fastdfs/storage 文件存储目录下创建软连接,将其链接到实际存放数据的目录

```
# ln -s /data/fastdfs/storage_data/data/ /data/fastdfs/storage_data/data/M00
```

### 配置 Nginx
简洁版 nginx 配置样例：
/usr/local/nginx/conf
```
user root;

server {
listen 80;
server_name localhost;
location ~/group([0-9])/M00 {
#alias /fastdfs/storage/data;
ngx_fastdfs_module;
}

```
> 注意
> 
> - A、 ~~8888 端口值是要与/etc/fdfs/storage.conf 中的 http.server_port=8888 相对应，
> 因为 http.server_port 默认为 8888,如果想改成 80，则要对应修改过来。~~
> - B、 Storage 对应有多个 group 的情况下，访问路径带 group 名，如/group1/M00/00/00/xxx，
> 对应的 Nginx 配置为：
> 
> ```
> location ~/group([0-9])/M00 {
> ngx_fastdfs_module;
> }
> ```
> -  C、 如查下载时如发现老报 404， 将 nginx.conf 第一行 user nobody 修改为 user root 后重新启动。

### 启动 Nginx

```
# /usr/local/nginx/sbin/nginx
```

ngx_http_fastdfs_set pid=xxx

重启 Nginx 的命令为： 

```
/usr/local/nginx/sbin/nginx -s reload
```

### 测试


```
/usr/bin/fdfs_test /etc/fdfs/client.conf upload /usr/local/src/fastdfs-5.11/README.md

/usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/FastDFS_v5.05.tar.gz
```
### 通过浏览器访问测试时上传的文件
http://192.168.4.125:8888/group1/M00/00/00/wKgEfVUYNYeAb7XFAAVFOL7FJU4.tar.gz


> 要将 22122/23000 端口加入白名单

参考：

http://blog.csdn.net/vanthyanhon/article/details/73718568

http://blog.csdn.net/m0_37797991/article/details/73394873

龙果学院 第23节--FastDFS分布式文件系统的安装与使用