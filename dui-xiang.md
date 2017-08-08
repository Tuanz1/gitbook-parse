# Parse.Object {#object}

Parse 储存的数据是利用Parse.Object 构造的，每个Parse.Object 包含兼容JSON格式的键值对。这些数据是无模式的，这意味着不需要在每个Parse.Object上指定存在的键。使用时，只需要设置需要的键值对，并在后端\(mongodb数据库中\)储存它。

举个例子来说，需要一个记录一场比赛的最高分，可以用一个简单的Parse.Object表示，它包括一下信息

```
score: 1337, playerName: "Sean Plott", cheatMode: false
```

这里的键值对是有限制的：键只能使用字母字符串，而值可以使string, number, boolean, array 或者 dictionarires - 这些可以被JSON编码的类型。

每个Parse.Object是一个具有类名称的特定子类的实例，您可以使用它来区分不同类型的数据。我们建议你的类名使用大驼峰写法，关键字名使用小驼峰写法，这样可以使你的代码看起来漂亮。

要创建一个新的子类，请使用Parse.Object.extends\(\)方法。任何Parse.Query将返回具有相同类名的任何Parse.Object的新类的实例。 如果您熟悉Backbone.Model，那么您已经知道如何使用Parse.Object。 它的设计是以相同的方式创建和修改的。

```
// 创建一个名为GameScore的子类
let GameScore = Parse.Object.extend("GameScore");

// 使用该子类创建一个实例
let gameScore = new GameScore();

// 使用Backbone语法
let Achievement = Parse.Object.extend({
  className: "Achievement"
});
```

> Parse.Object.extend\(\)函数说明：

```
 //函数接受三个参数，一个参数为string类型，为返回subClass的名称，
 //第二个参数protoProps <Object>为实例的属性，
 //第三个参数是 classProps <Object> 为类的属性
 let MyClass = Parse.Object.extend("MyClassName", {
        //实例方法
        Instance methods,
        //初始化函数
        initialize: (attrs, options) => {
            this.someInstanceProperty = [],
            Other instance properties
        }
    }, {
        //类的属性
        Class properties 
    });
    //函数返回一个新Parse.Object的子类
```

不是特别理解后面两个参数的作用

