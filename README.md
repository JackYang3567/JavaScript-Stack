
# 1、命令
## (1)、
```
重启防火墙：

systemctl restart firewalld.service 

关闭防火墙：

systemctl stop firewalld.service 

查看监听(Listen)的端口

netstat -lntp

检查端口被哪个进程占用

netstat -lnp|grep 8080

```
## （2）、CentOS 7打开TCP 22端口，基于SSH协议

第一步

#查看本机是否安装SSH软件包

[root@localhost ~]# rpm -qa | grep ssh

openssh-server-6.6.1p1-12.el7_1.x86_64

openssh-clients-6.6.1p1-12.el7_1.x86_64

libssh2-1.4.3-8.el7.x86_64

openssh-6.6.1p1-12.el7_1.x86_64

#如果没有，则需要安装

[root@localhost /]# yum install openssh-server

第二步

#开启 SSH 服务

[root@localhost ~]# service sshd start

Redirecting to /bin/systemctl start  sshd.service

#查看TCP 22端口是否打开

[root@localhost ~]# netstat -ntpl | grep 22

tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      17816/sshd          

tcp6       0      0 :::22                   :::*                    LISTEN      17816/sshd

第三步

#接下来便可使用终端仿真程序（例如putty）去登陆远程主机

如果你在客户端不能连接SSH服务的话，那可能是防火墙的原因，终端命令行中输入 iptables -nL 来看是否开放了ssh tcp 22 端口：

[root@localhost ~]# iptables -nL

你可以将防火墙中的规则条目清除掉：

[root@localhost ~]# iptables -F

## (3)、 安装Nginx

第一步 - 添加Nginx存储库
要添加CentOS 7 EPEL仓库，请打开终端并使用以下命令：
sudo yum install epel-release

第二步 - 安装Nginx
现在Nginx存储库已经安装在您的服务器上，使用以下yum命令安装Nginx ：
```
sudo yum install nginx
```
在对提示回答yes后，Nginx将在服务器上完成安装。

第三步 - 启动Nginx
Nginx不会自行启动。要运行Nginx，请输入：
```
sudo systemctl start nginx
```
第四步 - 停止Nginx
```
systemctl stop nginx
```

## (4)、 ssh root@39.108.XX.XX 连接报错
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:nuUdUUDbJT2RqWoZztrFfFw22wmvx8RLXH44FvdtbaU.
Please contact your system administrator.
Add correct host key in /Users/doer06/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /Users/doer06/.ssh/known_hosts:5
ECDSA host key for 39.108.XXX.XX has changed and you have requested strict checking.
Host key verification failed.
```
解决办法：
原因是这样，一旦使用本机ssh连接过目标机，则会在/Users/doer06/.ssh/known_hosts文件下生成目标机的公钥，以便下次可以直接使用。所以，我们可以把该文件下39.108.XXX.XX对应的公钥删除掉

vi /Users/doer06/.ssh/known_hosts
删除掉39.108.XXX.XX那一行。

