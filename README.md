
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
