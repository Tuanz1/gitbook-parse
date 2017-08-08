# Parse文档翻译 {#文档翻译}

> 本文档应用部分是使用针对Ionic项目

官方文档: [http://docs.parseplatform.org/js/guide/](http://docs.parseplatform.org/js/guide/ "官方文档")

### 基础配置部分

```
//在web app中使用
let Parse = require('parse');
// node.js服务中使用
var Parse = require('parse/node');
//在React Native app中使用
var Parse = require('parse/react-native');
```

### 使用JavaScript初始化Parse-Server，使用以下代码替换初始化代码

```
Parse.initialize("YOUR_APP_ID"); 
Parse.serverURL = 'http://YOUR_PARSE_SERVER:1337/parse'  
//这个部分依照自己的配置修改
```

> Parse JavaScript SDK 是基于现在十分流行的 Backbone.js框架，但是parse提供更灵活的API，这些API让你能与你喜欢的JS库配对。Parse的目的在于尽量减少配置，让开发者快速在Parse上构建JavaScript和Html5应用。

### FAQ:如何在Ionic项目中使用parse?

[ionic 项目中配置parse](#)
