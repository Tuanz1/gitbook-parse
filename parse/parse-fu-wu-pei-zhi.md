# mongodb 安装\(ubuntu 16.04\)

```
添加匙
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
添加源
echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
更新并安装
sudo apt-get update
sudo apt-get install -y mongodb-org
```

[官方安装手册\(其它平台\)](https://docs.mongodb.com/tutorials/)

### 开启mongodb服务

> mongod --dbpath db\_path\(数据库文件路径）

### 然后开始玩mongodb

mongo命令打开shell

查看教程进行简单的数据库学习  
[菜鸟教程](http://www.runoob.com/mongodb/mongodb-tutorial.html)  
[官网手册](https://docs.mongodb.com/manual/)  
[**MongoDB工具推荐**](http://www.shirlman.com/tec/20160605/424)

# 安装parse并启动

### 安装命令

```
cnpm i -g parse-server parse-dashboard
```

启动parse服务

```
parse-server --appId 123 --masterKey 123 --databaseURI mongodb://username:password@localhost:27017/dbname
```

直接访问localhost:1337/parse ,出现{“error”:”unauthorized”}，说明parse server在正常运行

### 配置config.json（参照上面的server配置\)

> ```
> {
>   "apps": [
>     {
>       "serverURL": "http://locahost:1337/parse",
>       "appId": "123",
>       "masterKey": "123",
>       "appName": "dbname"
>     }
>   ]
> }
> ```

### 运行

```
parse-dashboard --config config.json
```

> 访问 localhost:4040 进入parse-dashboard

[parse-server 详细配置](https://github.com/parse-community/parse-dashboard)

#### 效果截图



