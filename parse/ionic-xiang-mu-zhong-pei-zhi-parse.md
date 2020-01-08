# ionic2 中配置parse服务

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
3. index.html，在cordova.js之前引入&lt;script src="./assets/scripts/parse.min.js"&gt;&lt;/script&gt;
4. src/declarations.d.ts中写入  declare const Parse:any; \(没有该文件可以新建\)
5. 程序中直接使用全局变量Parse即可，无需任何imports

### 使用示例

* JS-SDK使用文档：[http://docs.parseplatform.org/js/guide/](http://docs.parseplatform.org/js/guide/)

* 函数API查询文档：[http://parseplatform.org/Parse-SDK-JS/api/](http://parseplatform.org/Parse-SDK-JS/api/)

* **相关文章 **

  \[driftyco/ionic app scripts\#88\]\([https://github.com/driftyco/ionic-app-scripts/issues/88](https://github.com/driftyco/ionic-app-scripts/issues/88)

  \[rollup/rollup plugin commonjs\#43\]\([https://github.com/rollup/rollup-plugin-commonjs/pull/43\](https://github.com/rollup/rollup-plugin-commonjs/pull/43%29%29\)

  \[rollup/rollup\#594\]\([https://github.com/rollup/rollup/issues/594\](https://github.com/rollup/rollup/issues/594%29%29\)

  \[官方\]\([http://ionicframework.com/docs/v2/resources/app-scripts/\](http://ionicframework.com/docs/v2/resources/app-scripts/%29%29\)



