# 介绍

Parse-Server 是一款开源的、基于nodejs的后端框架 ， 可以基于它进行产品的后端restful api的开发 ，用于提高中小型应用的开发效率。

## 优点

1. 通用接口支持（post , get , put ,delete）： 不同表名进行不同表的操作。

   2. 默认使用mongodb作为数据库 ：可直接操作数据，无需第三方数据库orm。

   3. 支持通用查询筛选 ：分页、条件筛选\(关联与非关联字段\)、排序等。

支持云代码 : trigger ，自定义云函数的支持，方便编写业务逻辑。

可结合express ： 拓展方便。

  


  


作者：kelvv

  


链接：http://www.jianshu.com/p/4c28c804552a

  


來源：简书

  


著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

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

