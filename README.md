学历代表你的过去，能力代表你的现在，学习代表你的将来！

[Android Studio](http://www.android-studio.org/)

### CentOS 7启动nginx
```
whereis nginx
nginx: /usr/local/nginx
-bash-4.2# cd /usr/local/nginx/
sbin/nginx
```

### Mac 启动MongoDB
```
brew services restart mongodb@3.6
```
### CentOS/Mac 命令行远程连服务器
```
ssh root@39.108.147.42
root@39.108.147.42's password: 
```


# 0、JavaScript Stack
 - [1、MXXN Stack](../../tree/01-MXXN-Stack) 
 - [2、MEAN Stack](../../tree/02-MEAN-Stack)
 - [3、MERN Stack](../../tree/03-MERN-Stack)
 - [4、MKAN Stack](../../tree/04-MKAN-Stack) 
 - [5、MKRN Stack](../../tree/05-MKAN-Stack) 
 - [6、MKVN Stack](../../tree/06-MKAN-Stack) 
 - [7、RMXXN Stack](../../tree/07-RMXXN-Stack) 

# 1、MongoDB
- [1、MongoDB简介](../../tree/11-mongodb-introduction)
- [2、MongoDB安装](../../tree/11-mongodb-install)
- [3、MongoDB数据模型](../../tree/11-mongodb-data-model)
- [4、MongoDB查询数据](../../tree/11-mongodb-query-data)
- [5、MongoDB更新数据](../../tree/11-mongodb-Update-data)
- [6、MongoDB删除数据](../../tree/11-mongodb-delete-data])
- [7、MongoDB数据库管理](../../tree/11-mongodb-Database-management)
- [8､ MongoDB数据库备份还原](../../tree/11-mongodb-Database-backup-restore)
- [9、MongoDB 使用地理空间索引](../../tree/11-MongoDB-uses-geospatial-ndex)

# 2、Redis
- [Redis 的应用场景](../../tree/01-redis-application-cenarios)
- [Mastering Redis](https://github.com/PacktPublishing/Mastering-Redis)

# 3、Distributed Version Control System Git
- [1、Git 服务器搭建及存储库的创建](../../tree/31-Git-install)

# 4､ RMKVN & RMKRN & RMKAN

# 5､ 网站应用微信登录开发指南

# 6、RESTful API
- [创建API](../../tree/01-RESTful-API)
- [命令行测试API](../../tree/02-RESTful-API)

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


