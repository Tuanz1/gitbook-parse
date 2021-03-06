# 准备工作

> 安装Nginx，SSL证书，Nodejs, MongoDB

# 1. 按照官方配置

```sh
# 新建个目录
sh <(curl -fsSL 
https://raw.githubusercontent.com/parse-community/parse-server/master/bootstrap.sh\

npm i
npm start
```

这个项目下config.json就是parse的配置文件，你需要修改的配置都在这里

# 2. 编辑Parse Server的config文件

```json
{
"appId": "id",
"cloud":"./cloud/main.js",
"javascriptKey":"key",
"clientKey":"key",
"restAPIKey":"key",
"masterKey": "masterKey",
"appName": "name",
"cloud": "./cloud/main.js",
"databaseURI": "mongodb://...........",
"port":8888,
"serverURL":"http://localhost:8888/parse",
"publicServerURL":"https://{{yourdomain}}/parse"
}
```

> publicServerURL是按照你配置nginx的https访问修改的

# 3. 配置Nginx

略

# 4. 配置Parse-Dasboard

```sh
parse-dashboard --config config.json
```

config.json文件大概是这样写的

```json
{
    "apps": [{
    "serverURL": "http://{{yourserverURL}}/parse",
    "appId": "",
    "masterKey": "",
    "appName": ""
    }],

   "users": [
           {
          "user":"admin",
          "pass":"admin"
           }
       ]
}

```

具体的配置可以参考官方文档
