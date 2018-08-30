# doc

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
