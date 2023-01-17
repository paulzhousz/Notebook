## FastDFS Docker配置
<p align="center">2022-03-29</p>

[TOC]

### 1. Docker 安装 FASTDFS
```
# 启动tracker服务，注意network参数
docker run -d --network=host --name tracker -v /app/fastdfs/tracker:/var/fdfs delron/fastdfs tracker
# 启动storage服务，注意TRACKER_SERVER ip为宿主机ip
docker run -d --network=host --name storage -e TRACKER_SERVER=172.17.16.5:22122 -v /app/fastdfs/storage:/var/fdfs -e GROUP_NAME=group1 delron/fastdfs storage
```
### 2. 安装 gd-devel.x86_64包
```
yum -y install gd-devel.x86_64
```
### 3. nginx 将http_image_filter_module包含进来
```
nginx -V 查看nginx当前配置
# 进入nginx安装目录，configure 增加 --with-http_image_filter_module
./configure --prefix=/usr/local/nginx --add-module=/tmp/nginx/fastdfs-nginx-module-master/src --with-http_image_filter_module

make && make install
``` 
### 4. 修改nginx配置文件
```
vi /usr/local/nginx/conf/nginx.conf
```
修改内容：
```
location ~ /group1/M00/(.+)_([0-9]+)x([0-9]+)\.(jpeg|jpg|gif|png) {
            alias /data0/fastdfs/storage/storage0/data;
            ngx_fastdfs_module;
            set $w $2;
            set $h $3;

            if ($w != "0") {
                rewrite group1/M00(.+)_(\d+)x(\d+)\.(jpeg|jpg|gif|png)$ group1/M00$1.$4 break;
            }

            if ($h != "0") {
                rewrite group1/M00(.+)_(\d+)x(\d+)\.(jpeg|jpg|gif|png)$ group1/M00$1.$4 break;
            }


            image_filter resize $w $h;

            image_filter_buffer 100M;

            #try_files group1/M00$1.$4 $1.jpg;
        }


        location ~ /group1/M00/(.+)\.?(.+){
              alias /data0/fastdfs/storage/storage0/data;
              ngx_fastdfs_module;
        }
    }

```
### 5. 关闭nginx并重启
```
/usr/local/nginx/sbin/nginx -s stop
/usr/local/nginx/sbin/nginx
```
