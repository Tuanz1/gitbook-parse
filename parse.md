# 介绍

Parse-Server 是一款开源的、基于nodejs的后端框架 ， 可以基于它进行产品的后端restful api的开发 ，用于提高中小型应用的开发效率。

## 优点：

1. 通用接口支持（post , get , put ,delete）： 不同表名进行不同表的操作。

2. 默认使用mongodb作为数据库 ：可直接操作数据，无需第三方数据库orm。

3. 支持通用查询筛选 ：分页、条件筛选\(关联与非关联字段\)、排序等。

4. 支持云代码 : trigger ，自定义云函数的支持，方便编写业务逻辑。

5. 可结合express ： 拓展方便。

## 缺点

1. 数据库操作功能有限，对于一些功能较复杂的需求就不太适合。
2. 数据库不支持大文件，有时候图片文件稍微大一点就存不进去了。

Parse文档翻译

> 本文档应用部分是使用针对Ionic项目

官方文档: [http://docs.parseplatform.org/js/guide/](http://docs.parseplatform.org/js/guide/ "官方文档")

# Ionic4项目配置parse服务

## 初始化

> 在src/app/app.components.ts的import区域写入

```
Parse.initialize('appId');
Parse.serverURL = 'yourNewServerUrl.com';
```

> 实例：

```
Parse.initialize('demo', 'jsKey','masterKey');
Parse.serverURL = 'http://localhost:8001/parse';
//根据个人配置对应修改init后面两个参数可选
```

## Ionic4项目中使用Parse JS SDK

> 临时：使用&lt;scirpt&gt;标签在index.html引入dist/parse.min.js，同时在rollup中设置skip跳过parse

1. cnpm i parse --save // 可以不用save，只需要js文件就可以
2. 复制node\_modules/parse/dist/parse.js到/src/assets/scripts/parse.min.js
3. index.html，在cordova.js之前引入&lt;script src="./assets/scripts/parse.min.js"&gt;&lt;/script&gt;  //可能没有cordova.js就在头部引入就好了
4. src/declarations.d.ts中写入  declare const Parse:any; \(没有该文件可以新建\)
5. 程序中直接使用全局变量Parse即可，无需任何imports

### 使用示例

* JS-SDK使用文档：[http://docs.parseplatform.org/js/guide/](http://docs.parseplatform.org/js/guide/)

* 函数API查询文档：[http://parseplatform.org/Parse-SDK-JS/api/](http://parseplatform.org/Parse-SDK-JS/api/)

## 相关文章** **

- \[driftyco/ionic app scripts\#88\]\([https://github.com/driftyco/ionic-app-scripts/issues/88](https://github.com/driftyco/ionic-app-scripts/issues/88)

- \[rollup/rollup plugin commonjs\#43\]\([https://github.com/rollup/rollup-plugin-commonjs/pull/43\](https://github.com/rollup/rollup-plugin-commonjs/pull/43%29%29\)

- \[rollup/rollup\#594\]\([https://github.com/rollup/rollup/issues/594\](https://github.com/rollup/rollup/issues/594%29%29\)

- \[官方\]\([http://ionicframework.com/docs/v2/resources/app-scripts/\](http://ionicframework.com/docs/v2/resources/app-scripts/%29%29\)

