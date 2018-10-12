# JavaScript Stack
 - M**N Stack 
 -  [MEAN Stack](tree/02-MEAN-Stack)
 - MERN Stack
 - MKAN Stack 
 - MKRN Stack
 - MKVN Stack
 - RM**N

# RMKVN & RMKRN & RMKAN

# 网站应用微信登录开发指南

https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419316505&token=&lang=zh_CN

'''
const xcxConst = require('../config/common/xcx.config')

const request = require('request')

const BaseHandler = require('../libs/baseHandler')
let controllers = new BaseHandler()

/* 微信登陆 */
const AppID  = xcxConst.APPID
const SECRET = xcxConst.AppSecret
const URI    = xcxConst.WEB_URL

controllers.wx_login = async (ctx, next)=>{
   
    const STATE        = uuidv4().split('-').join('')
    // 第一步：用户同意授权，获取code
    const CALLBACK     = 'get_wx_access_token';
    const REDIRECT_URI = `${encodeURIComponent(URI)}${CALLBACK}`  
    const SCOPE        = 'snsapi_login'//'snsapi_base'//'snsapi_userinfo';
     // 这是编码后的地址
    let wxURL = `https://open.weixin.qq.com/connect/qrconnect?appid=${AppID}&redirect_uri=${REDIRECT_URI}&response_type=code&scope=${SCOPE}&state=${STATE}#wechat_redirect`
    //          `https://open.weixin.qq.com/connect/qrconnect?appid=${AppID}&redirect_uri=${return_uri}&response_type=code&scope=SCOPE&state=STATE#wechat_redirect`
    const url = `https://open.weixin.qq.com/connect/qrconnect?appid=${AppID}&redirect_uri=${REDIRECT_URI}&response_type=code&scope=snsapi_login&state=3d6be0a4035d839573b04816624a415e#wechat_redirect`
   // const url = `https://open.weixin.qq.com/connect/qrconnect?appid=${AppID}&redirect_uri=https%3A%2F%2Fpassport.yhd.com%2Fwechat%2Fcallback.do&response_type=code&scope=snsapi_login&state=3d6be0a4035d839573b04816624a415e#wechat_redirect`
  
   
    console.log(wxURL)
    ctx.redirect(wxURL)
    
}


controllers.get_wx_access_token = async(ctx, next)=>{
    //console.log("get_wx_access_token")
    //console.log("code_return: "+req.query.code)
    
    // 第二步：通过code换取网页授权access_token
  
    let {code} = {...ctx.params, ...ctx.query}
    console.log('code....',code)
    request.get(
        {   
            //  `https://api.weixin.qq.com/sns/oauth2/access_token?appid=APPID&secret=SECRET&code=CODE&grant_type=authorization_code`
            url:`https://api.weixin.qq.com/sns/oauth2/access_token?appid=${AppID}&secret=${SECRET}&code=${code}&grant_type=authorization_code`,
        },
           (error, response, body) =>{
            if(response.statusCode == 200){
                
                // 第三步：拉取用户信息(需scope为 snsapi_userinfo)
                //console.log(JSON.parse(body));
                const data = JSON.parse(body);
                const access_token = data.access_token;
                const openid = data.openid;
                
                request.get(
                    {
                       // url:'https://api.weixin.qq.com/sns/userinfo?access_token='+access_token+'&openid='+openid+'&lang=zh_CN',
                          url:`https://api.weixin.qq.com/sns/userinfo?access_token=${access_token}&openid=${openid}&lang=zh_CN`,
                    },
                        (error, response, body)=>{
                        if(response.statusCode == 200){
                            
                            // 第四步：根据获取的用户信息进行对应操作
                            const userinfo = JSON.parse(body);
                            //console.log(JSON.parse(body));
                            console.log('获取微信信息成功！');
                            
                            // 小测试，实际应用中，可以由此创建一个帐户
                            /*
                            res.send("\
                                <h1>"+userinfo.nickname+" 的个人信息</h1>\
                                <p><img src='"+userinfo.headimgurl+"' /></p>\
                                <p>"+userinfo.city+"，"+userinfo.province+"，"+userinfo.country+"</p>\
                            ");
                            */
                           ctx.body = `<h1>${userinfo.nickname} 的个人信息</h1>
                                       <p><img src='${userinfo.headimgurl}' /></p>
                                       <p>${userinfo.city}, ${userinfo.province},${userinfo.country}</p>`
                            
                        }else{
                            console.log(response.statusCode);
                        }
                    }
                );
            }else{
                console.log(response.statusCode);
            }
        }
    );
}
'''

## -----------------

环境说明 
- CentOS 7.x 最小安装 
- 配置网络连接

## 1. 安装Git及创建用户
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
## 2. 创建证书登录
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
## 3.初始化Git仓库
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

## 4. 克隆仓库
```
在另一台电脑上（下面称为：Client），安装git，并且运行下面的命令：
$ sudo git clone git@39.108.147.42:/srv/gitrepo/koa-redis-waiter.git
```


## 5. 验证安装
### 5.1. 推送到远程服务器
```
在本地Client的project目录下，创建一个文件：text.txt，内容随意，然后上传到远端：
$ git add . 添加所有文件  注意有个 . 
$ git commit -m '注释' 提交本地
$ git push origin master提交给默认分支
```
### 5.2. 检验
在本地Client的另外一个目录下，克隆一下：
```
$ git clone git@39.108.147.42:/srv/gitrepo/project.git
```
看看text.txt文件是否存在，内容是否对。

### 5.3. 常用的Git命令：
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
