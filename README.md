## 命令行测试API
### curl 模拟 GET\POST 请求，以及 curl post 上传文件
在命令行中，我们使用 curl 这个工具可模拟GET/POST 及POST上传文件
#### 1、curl GET 请求
- curl命令 + 请求接口的地址。
运行命令:
```
$ curl https://www.servefuture.com//holiday/show/5bbc8488a5373341cade1c41
```
输出结果:
```
{"IsSuccessful":true,"data":
  {"genre":1,"createdAt":"1539081352","_id":"5bbc8488a5373341cade1c41","country":"中国",
   "areaCode":"86","province":"四川","city":"凉山","zipCode":"615000","holiday":"火把节",
   "startTime":"2019-08-05","endTime":"2019-08-11","day":"7","updatedAt":"1539081352"
   }
}
```
