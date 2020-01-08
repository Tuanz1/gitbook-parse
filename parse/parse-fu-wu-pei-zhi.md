# \# 准备工作

# Nginx，SSL证书，Nodejs, MongoDB

# \# 1. 按照官方配置

# \`\`\`sh

# \# 新建个目录

# sh &lt;\(curl -fsSL https://raw.githubusercontent.com/parse-community/parse-server/master/bootstrap.sh\)

# npm i 

# npm start

# \`\`\`

# 其中config.json就是parse的配置文件，所有的东西都在这里配置

# 

# \# Parse Server的config文件

# publicServerURL决定你外部访问不是用locahost

# \`\`\`json

# {

#   "appId": "GGmbVq9sjSw9uODaf1fHsqMn2AL8tooE0OkLJGRz",

#   "cloud":"./cloud/main.js",

#   "javascriptKey":"GGmbVq9sjSw9uODaf1fHsqMn2AL8tooE0OkLJGRz",

#   "clientKey":"GGmbVq9sjSw9uODaf1fHsqMn2AL8tooE0OkLJGRz",

#   "restAPIKey":"GGmbVq9sjSw9uODaf1fHsqMn2AL8tooE0OkLJGRz",

#   "masterKey": "DCKKrJR2Uc0dkLKWuWSXigmDaLXS9BvLH8qntN86",

#   "appName": "carddiary",

#   "cloud": "./cloud/main.js",

#   "databaseURI": "mongodb://carddiary:ryan1997@127.0.0.1:27017/carddiary",

#   "port":8888,

#   "serverURL":"http://localhost:8888/parse",

#   "publicServerURL":"https://shellcode.vip:1337/parse"

# }

# \`\`\`



