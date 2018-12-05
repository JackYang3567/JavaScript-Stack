## 8､ MongoDB数据库备份还原

### 一、阿里云上MongoDB数据库的备份
```
登录阿里云——>控制台——>云数据库MongoDB——>(实例后操作——>管理)
——>备份与恢复——>备份实例——>备份方法（逻辑备份）——>确定
```
 下载到目录下：
 /Users/doer06/Downloads

### 二、阿里云上MongoDB数据库的还原(恢复)
$cd /Users/doer06/Downloads
```
$ cat hins3065511_data_20181205091106.ar | mongorestore  -h 127.0.0.1 --port 27017  --drop --gzip --archive -vvvv --stopOnError  (没设用户名和密码) 
```
