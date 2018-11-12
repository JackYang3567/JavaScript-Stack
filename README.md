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

#### 2、curl POST 请求
- POST请求用 -X POST来申明请求方法 用-d 参数，来传送参数。
运行命令：
```
curl -d "OrderId=3055219855&Field=UserName&Value=Jack123" "https://servefuture.com/orders/requestSynchOrder"
或
curl -H '{"User-Agent":"request","content-type":"application/json","timestamp":"1541994636000","appkey":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJkYXRhIjoiNiJ9.M0Zc-zoTO6ANvLASNxZSkxfaxLHreB29xYrpXzb_TOE","signature":"DB745095D5563CBE07253DACF8779978"}' -d '{"OrderId":"3055219855","Field":"UserName","Value":"Jack"}' "https://servefuture.com/orders/synchOrder"
```
输出结果：
