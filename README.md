# Distributed Version Control System Git

## 3.1 、Git 服务器搭建及存储库的创建

环境说明 
- CentOS 7.x 最小安装 
- 配置网络连接

### 1. 安装Git及创建用户
```
# 安装Git
$ yum install git

# 创建一个git用户组和用户，用来运行git服务
$ groupadd git
$ adduser git -g git

禁止git用户登录:
修改/etc/passwd文件，修改
# 找到这句：
git:x:503:503::/home/git:/bin/bash

# 改为：
git:x:503:503::/home/git:/bin/git-shell
```
### 2. 创建证书登录
```
$ mkdir /home/git/.ssh
$ chmod 700 /home/git/.ssh
$ touch 700 /home/git/.ssh/authorized_keys
$ chmod 600 /home/git/.ssh/authorized_keys


注意，如果是采用的sudo方式来创建git和相应的文件的，需要设置/home/git/.ssh/的owner为git，否则还是每次要输入密码的。
# owner改为git
$ sudo chown -R git:git /home/git/.ssh/


编辑/home/git/.ssh/authorized_keys，把客户端的公钥放进去，1个公钥1行。
附：如何创建私钥：
# 创建私钥，文件位于用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件
$ ssh-keygen -t rsa -C "youremail@example.com"

ssh-keygen -t rsa -C jack@servefuture.com 


```
### 3.初始化Git仓库
```
$ cd /srv
$ mkdir gitrepo
$ chown git:git gitrepo/
$ cd gitrepo

# 创建一个空的Git仓库，服务器上的Git仓库通常都以.git结尾
$ git init --bare koa-redis-waiter.git

# 将仓库所属用户改为git
$ chown -R git:git koa-redis-waiter.git
```

### 4. 克隆仓库
```
在另一台电脑上（下面称为：Client），安装git，并且运行下面的命令：
$ sudo git clone git@39.108.147.42:/srv/gitrepo/koa-redis-waiter.git
```


### 5. 验证安装
#### 5.1. 推送到远程服务器
```
在本地Client的project目录下，创建一个文件：text.txt，内容随意，然后上传到远端：
$ git add . 添加所有文件  注意有个 . 
$ git commit -m '注释' 提交本地
$ git push origin master提交给默认分支
```
#### 5.2. 检验
在本地Client的另外一个目录下，克隆一下：
```
$ git clone git@39.108.147.42:/srv/gitrepo/project.git
```
看看text.txt文件是否存在，内容是否对。

#### 5.3. 常用的Git命令：
```
$ git add . 添加所有文件  注意有个 . 
$ git commit -m '注释' 提交本地
$ git push origin master提交给默认分支
$ git -rm 删除
$ git pull origin master 从默认分支下载
$ git branch -v 查看所有分支
```

- 使用git push origin master是出现如下问题； 

Username for 'https://github.com':

- 解决办法： 

git remote set-url origin git+ssh://git@github.com/username/reponame.git
