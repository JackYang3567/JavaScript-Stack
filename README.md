# 1、Node.js Request发短信

## 一、PaaS 容联·云通讯 https://www.yuntongxun.com/
## 二、Node.js实现
### 1、所需中间件
```
const crypto = require('crypto');
const moment = require(`moment`);;
const request = require('request')
const Promise = require('bluebird')
```
### 2、路由
```
const handler = require('../controllers/sms_check_code.controller')

module.exports = (router) => {
    router.post('/sms/get-sms-code', handler.getSmsCode)
    router.post('/sms/send-template-sms', handler.sendTemplateSms)
    
}
```
### 3、控制器
```
const utils = require('../libs/utils.js')
const SmsHandler = require('../models/sms_code/sms_check_code.handler')

const BaseHandler = require('../libs/baseHandler.js')
let controllers = new BaseHandler()


/**
 *  获取短信码
 *  @param ctx
 *  @param next
 */
controllers.sendTemplateSms = async (ctx, next) => {
    let sms = ctx.request.body
   
    
    try{
        let phoneNumbers = []
        let templateContent = []
       
         phoneNumbers.push(sms.phone)
         for(let key in sms){
             if(key!='templateId'){
                templateContent.push(sms[key].toString())  
             }
            
        }
       
       let retObj = await utils.sendTemplateSMS(phoneNumbers,templateContent)
       controllers.restSuccess(ctx, retObj)
    }catch(e){
        throw new Error(`${__filename}'s sendTemplateSms not parsable!`);
    }
}

/**
 *  获取短信码
 *  @param ctx
 *  @param next
 */
controllers.getSmsCode = async (ctx, next) => {
    let { phone ,token } = ctx.request.body    
    let code = utils.generateSmsCode()
    try{
         utils.sendTexto(code, phone)
         let isExistCode = await SmsHandler.isCodeExist(`phone:${phone}`)
        if(isExistCode){
            let error_code = 0
            let error_message = `获取短信码间隔时间太短，请稍后再试！`
            controllers.restError(ctx, error_code, error_message)    
        }else{
            SmsHandler.setSmsKey(`phone:${phone}`,code)
            SmsHandler.setSmsKey(`sms:${code}`,phone)
            controllers.restSuccess(ctx, {"phone": phone ,"code": code })
         }  
    
     }catch(e){
         throw new Error(`${__filename}'s getSmsCode not parsable!`);
     }
  }
module.exports = controllers
```

### 4、服务model
```
const path = require('path');
const crypto = require('crypto');
const moment = require(`moment`);
const superagent = require('superagent');
const request = require('request')
const _ = require('underscore')
const Promise = require('bluebird')
const gConfig = require('../config/common/wx.const')
const redisPromise = require('./redis-promise.js').redisClient;
const auth = require('./auth.js')(redisPromise);

// 开发者主账号ACCOUNT SID
const ACCOUNT_SID = '8a216da85d158d1b015d402b660b12fe';

// 账户授权令牌
const AUTH_TOKEN = gConfig.sms_AUTH_TOKEN || '';

// appId
const APP_ID = '8aaf070867c517d20167e371835e1152';

// host
const HOST = 'https://app.cloopen.com';

// port
const PORT = '8883';

//模板ID
const templateId = 402833

class Utils{
    constructor(){

    }
    
    /**
 * 发送模板短信
 * @param {Array} phoneNumbers 用户电话数组，如：[13911281234,15010151234,13811431234]
 * @param {String} templateId 模板ID
 * @param {Array} templateContent 模板内容，如：["替换内容","替换内容"]，将替换{1}，{2}对应的内容
 */ 
    async sendTemplateSMS(phoneNumbers, templateContent){
        let uri = `${HOST}:${PORT}/2013-12-26/Accounts/${ACCOUNT_SID}/SMS/TemplateSMS`
        let data = {
            to: phoneNumbers.join(','),
            appId: APP_ID,
            templateId,
            datas: templateContent
        }
       
        let timeStamp = moment().format('YYYYMMDDhhmmss')       
        return await this.invokingRequest(uri,data,timeStamp)
    }
/**
  * 调用Request公共方法
  * @param {*} apiMethod 
  * @param {*} data 
  */
 async invokingRequest(uri,data,timeStamp){
    const buffer = Buffer.from(`${ACCOUNT_SID}:${timeStamp}`);
    const Authorization = buffer.toString('base64');
    
    let options = {
        url: `${uri}`,
        qs: {sig: this.getSignatureSms(ACCOUNT_SID, AUTH_TOKEN, timeStamp)},
        method: 'POST', 
        body: JSON.stringify(data),
        headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json;charset=utf-8',
            'Authorization': Authorization
        }       
      };
    
    return new Promise((resolve, reject) => {
        request(
         options 
        , (err, response, body) => {
            if (!err && response.statusCode == 200) {   
                console.log('==Request===')      
                resolve(body)  
            } else {
                reject(new Error('请求微信网络错误'))
            }
        })
    }) 
}

/**
 * 携程服务端调用API签名算法
 * @param {*} timeStamp 时间戳
 * @param {*} sign      签名appkey
 * @param {*} data      请求主体(数据)
 */
 getSignatureSms(ACCOUNT_SID, AUTH_TOKEN, timeStamp){
    try {
        //用于加密的字符 
        let signStr = ACCOUNT_SID + AUTH_TOKEN + timeStamp;
        let md = crypto.createHash('md5').update(signStr, 'utf8').digest('hex').toUpperCase();
        //返回经过加密后的字符串
        return md 
    } catch (e) {
        return null;
    }
  }

}

module.exports = new Utils();
```

