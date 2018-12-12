
#  Nginx安装

## Nginx安装所需环境
Nginx 是 C语言 开发，建议在 Linux 上运行，当然，也可以安装 Windows 版本，本篇则使用 CentOS 7 作为安装环境。

### 一. gcc 安装
cd /usr/local
安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，则需要安装：
```
yum install gcc-c++
```
### 二. PCRE pcre-devel 安装
PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。命令：
```
yum install -y pcre pcre-devel
```
### 三. zlib 安装
zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip ，所以需要在 Centos 上安装 zlib 库。
```
yum install -y zlib zlib-devel
```
### 四. OpenSSL 安装
OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。
nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。
```
yum install -y openssl openssl-devel
```
官网下载
1.直接下载.tar.gz安装包，地址：https://nginx.org/en/download.html

2.使用wget命令下载（推荐）。
```
wget -c https://nginx.org/download/nginx-1.15.7 .tar.gz
```
解压
依然是直接命令：
```
tar -zxvf nginx-1.15.7.tar.gz
cd nginx-1.15.7
``
配置
其实在 nginx-1.15.7 版本中你就不需要去配置相关东西，默认就可以了。当然，如果你要自己配置目录也是可以的。
1.使用默认配置

./configure
2.自定义配置（不推荐）
```
./configure \
--prefix=/usr/local/nginx \
--conf-path=/usr/local/nginx/conf/nginx.conf \
--pid-path=/usr/local/nginx/conf/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi
```
注：将临时文件目录指定为/var/temp/nginx，需要在/var下创建temp及nginx目录

编译安装
```
make
make install
```
查找安装路径：
```
whereis nginx
```
编译安装
```
make
make install
```
查找安装路径：
```
whereis nginx
```

开机自启动
即在rc.local增加启动代码就可以了。
```
vi /etc/rc.local
```
增加一行 /usr/local/nginx/sbin/nginx
设置执行权限：
```
chmod 755 rc.local
```

### 启动nginx时就报错！
Job for nginx.service failed because the control process exited with error code. See "systemctl status nginx.service" and "journalctl -xe" for details.

你修改的语句末尾少了分号;
