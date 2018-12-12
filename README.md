
# 1、命令
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
