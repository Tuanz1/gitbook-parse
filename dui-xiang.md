# 对象

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
    //函数返回一个新Parse.Object的子
```

你可以添加额外的方法和属性到你的`Parse.Object`子类：

```ts
// 一个复杂的Parse.Object子类
var Monster = Parse.Object.extend("Monster", {
  // 实例方法
  hasSuperHumanStrength: function () {
    return this.get("strength") > 18;
  },
  // 实例属性
  initialize: function (attrs, options) {
    this.sound = "Rawr"
  }
}, {
  // 类方法
  spawn: function(strength) {
    var monster = new Monster();
    monster.set("strength", strength);
    return monster;
  }
});

var monster = Monster.spawn(200);
alert(monster.get('strength'));  // Displays 200.
alert(monster.sound); // Displays Rawr.
```

如果想为任意一个对象创建一个实例，你也可以使用`Parse.Object`直接构造。`new Parse.Object(className)`将会创建一个指定类名的实例。

如果你的项目已经使用了ES6，恭喜！从1.6.0版本开始，JavaScript SDK 已经和ES6类兼容，你可以使用`extends`关键词继承`Parse.Object`：

```ts
class Monster extends Parse.Object {
  constructor() {
    // Pass the ClassName to the Parse.Object constructor
    super('Monster');
    // All other initialization
    this.sound = 'Rawr';
  }

  hasSuperHumanStrength() {
    return this.get('strength') > 18;
  }

  static spawn(strength) {
    var monster = new Monster();
    monster.set('strength', strength);
    return monster;
  }
}

```

但是，当你使用extends时，SDK将不会自动识别你的子类。如果你想要查询返回的对象使用你的`Parse.Object`子类，你将需要注册这个子类，就像我们在其他平台做的一样：

```ts
// 指定Monster子类以后
Parse.Object.registerSubclass('Monster', Monster);
```
# 对象的基本操作\(CRUD\)

### 保存对象\(create\) {#save-object}

假设您想将上述GameScore内容保存到Parse Cloud中。界面类似于Backbone.Model，包括save方法：

使用object.save\(\)函数保存实例对象

> 举例: \(代码部分根据最新的es6修改\)

```js
let GameScore = Parse.Object.extend("GameScore")
let gameScore = new GameScore();
gameScore.set("score", 1337);
gameScore.set("playerName", "Sean Plott");
gameScore.set("cheatMode", false);

gameScore.save(null).then(gameScore=>{
    //执行保存对象后应发生的逻辑。
    alert('New object created with objectId: ' + gameScore.id);
  }).catch(err=>{
    // 执行保存失败时应发生的任何逻辑。
    // 错误是具有错误代码和消息的Parse.Error。
    alert('Failed to create new object, with error code: ' + error.message);
  }

});
```

 parse-dashboard中看到的结果

![](/assets/example1.png)

这里有两件事要注意。 在运行此代码之前，你不必配置或设置名为GameScore的新类。 你的Parse应用程序首次遇到时，会为您轻松创建此类。

还有一些字段，你不需要指定为方便提供。objectId是每个保存的对象的唯一标识符。createdAt和updatedAt表示每个对象在云中创建和最后修改的时间。每个这些字段都由Parse填充，因此在保存操作完成之前它们不存在于Parse.Object。

如果你愿意，你可以直接在调用中设置属性save。

```js
let GameScore = Parse.Object.extend("GameScore");
let gameScore = new GameScore();
gameScore.save({
  score: 1337,
  playerName: "Sean Plott",
  cheatMode: false
}).then(gameScore=>{
    // 该对象已成功保存。
  }).catch(err=>{
    // 保存失败。
    // 错误是具有错误代码和消息的Parse.Error。
})
```

根据参数的不同,object.save函数还有几种不同的形式

1. object.save\(\)
2. object.save\(null. options\)
3. object.save\(attrs, options\)
4. object.save\(key, values, options\)

> 原理上来说基本上一致，可以参考官方文档的参数说明

### 检索对象\(Retrieving\)

将数据保存到云端很有趣，但再次获取数据更为有趣。如果你有objectId，你可以使用Parse.Query检索整个Parse.Object：

```js
let GameScore = Parse.Object.extend("GameScore");
let query = new Parse.Query(GameScore);
query.get("xWMyZ4YEGZ").then(gameScore=>{
    // 对象已成功检索。
  }).catch(err=>{
    // 对象未成功检索。
    // 错误是具有错误代码和消息的Parse.Error。
})
```

要从中获取值Parse.Object，请使用该get方法。

```js
let score = gameScore.get("score");
let playerName = gameScore.get("playerName");
let cheatMode = gameScore.get("cheatMode");
```

三个特殊保留值作为属性提供，并且不能使用'get'方法检索，也不能使用'set'方法进行修改

```js
let objectId = gameScore.id;
let updatedAt = gameScore.updatedAt;
let createdAt = gameScore.createdAt;
```

如果您需要刷新“Parse Cloud”中已有的最新数据的对象，则可以像这样调用fetch方法：

```js
myObject.fetch().then(myObject=>{
    // 该对象已成功刷新。
  }).then(err=> {
    // 该对象未成功刷新。
    //错误是具有错误代码和消息的Parse.Error。
})
```

根据objectId确实可以获得你想要的那一条数据，答案是有些场景下需要使用某些条件匹配多条数据，而使用匹配条件来检索对象使用find\(\)函数，类似与mongodb中的find函数

```js
let GameScore = Parse.Object.extend("GameScore");
let query = new Parse.Query(GameScore);
//score：1337为匹配条件
query.find({score:1337}).then(gameScore =>{
  console.log(gameScore);
}).catch(err =>{
  console.log(err);
})
}
```

> 不同于mongodb, parse不具备findOne函数，对于所有的find返回的都是Parse.Query\(详细看官网文档\)

检索对象是一个app应用中重要组成部分，更多所方法会在后面涉及，如需要特定查询功能，参阅官方文档。

### 更新对象

更新对象很简单。只需设置一些新的数据，并调用save方法。例如：

```js
// 创建对象
let GameScore = Parse.Object.extend("GameScore");
let gameScore = new GameScore();
gameScore.set("score", 1337);
gameScore.set("playerName", "Sean Plott");
gameScore.set("cheatMode", false);
gameScore.set("skills", ["pwnage", "flying"]);
gameScore.save(null).then(gameScore=> {
    // 现在我们来更新一些新的数据。在这种情况下，只有作弊模式和得分两种情况
    // 将被发送到云端。 playerName没有改变。
    gameScore.set("cheatMode", true);
    gameScore.set("score", 1338);
    gameScore.save();
})
```

自动分析并显示哪些数据已更改，因此只有被修改字段\(dirty field\)将发送到Parse Cloud。你不打算更新的数据，因此不需要担心压缩数据。

计数器  
上面的例子包含一个常见的用例。“分数”字段是一个计数器，我们需要不断更新玩家的最新分数。使用上面的方法是有效的，但麻烦的是，如果有多个客户端试图更新相同的计数器，那就可能会出现一些问题。  
为了帮助存储计数器类型的数据，Parse提供了原子地增加（或减少）任何数字字段的方法。所以，同样的更新可以重写为：

```js
gameScore.increment("score");
gameScore.save();
```

您还可以通过传递第二个参数来增加任何数量increment。当没有指定数量时，默认情况下使用1。

数组  
为了帮助存储数组数据，有三个操作可用于原子地更改与给定键相关联的数组：  
add 将给定对象附加到数组字段的末尾。  
addUnique只有当它不包含在数组字段中时才添加它。插入位置不能保证。  
remove 从数组字段中删除给定对象的所有实例。  
例如，我们可以添加项目到类似“技能”字段，如下所示：

```js
gameScore.addUnique("skills", "flying");
gameScore.addUnique("skills", "kungfu");
gameScore.save();
```

请注意，目前不可能在同一个保存中从数组中原子添加和删除项目。您将要在每种不同类型的数组操作之间调用save。

### 销毁对象

要从云中删除对象：

```js
myObject.destroy().then(myObject=>{
    // 该对象已从Parse Cloud中删除。
  }).catch(err=>{
    // 删除失败。
    // 错误是具有错误代码和消息的Parse.Error。
})
您可以使用unset方法从对象中删除单个字段：
// 之后，playerName字段将为空
myObject.unset("playerName");
// 将字段删除保存到Parse Cloud。
// 如果对象的字段是数组，则在每个unset（）操作之后调用save（）。
myObject.save();
```

> 请注意，不推荐使用object.set（null）从对象中删除字段，并将导致意想不到的功能。

关系数据  
对象可能与其他对象有关系。例如，在博客应用程序中，Post对象可能有很多Comment对象。Parse支持各种关系，包括一对一，一对多和多对多。

一对一和一对多关系  
通过Parse.Object将另一个对象中的值保存为一个值来建模一对一和一对多关系。例如，Comment博客应用中的每个应用可能对应于一个Post。  
要创建一个新的带有的Comment的Post，你可以写：

```js
// 声明类型。
let Post = Parse.Object.extend("Post");
let Comment = Parse.Object.extend("Comment");
// 创建帖子
let myPost = new Post();
myPost.set("title", "I'm Hungry");
myPost.set("content", "Where should we go for lunch?");
// 创建评论
let myComment = new Comment（）;
myComment.set("content", "Let's do Sushirrito.");
// 在评论中添加帖子作为值
myComment.set("parent", myPost);
// 这将保存myPost和myComment
myComment.save();
在内部，Parse框架将会将引用对象存储在一个位置，以保持一致性。你也可以使用他们的objectId像这样来链接对象：
let post = new Post();
post.id = "1zEcyElZ80";
myComment.set("parent", post);
默认情况下，当获取对象时，Parse.Object不会获取相关的对象。这些对象的值无法检索，直到它们被这样读取：
let post = fetchedComment.get("parent");
post.fetch().then(post=>{
    let title = post.get("title");
})
```

多对多关系  
使用多对多关系建模Parse.Relation。这类似于将Parse.Object的数组存储为关键值，除了您不需要一次获取关系中的所有对象。此外，这允许Parse.Relation比数组的Parse.Object方法扩展更多的对象。例如，User可能可能会喜欢许多Posts。在这种情况下，你可以存储一组Posts，一个User喜欢使用relation。为了添加Post到“喜欢”列表中User，你可以做：

```js
let user = Parse.User.current();
let relation = user.relation("likes");
relation.add(post);
user.save();
你可以从Parse.Relation删除帖子:
relation.remove(post);
user.save();
你可以调用add和remove调用保存之前多次：
relation.remove(post1);
relation.remove(post2);
user.save();
你也可以在数组Parse.Object来add和remove：
relation.add([post1, post2, post3]);
user.save();
默认情况下，此关系中的对象列表未下载。您可以通过使用Parse.Query返回的结果获取用户喜欢的帖子列表query。代码看起来像：你可以通过使用query返回Parse.Query获取用户喜欢的帖子列表。 代码如下：
relation.query().find().then(list=>{
    // 列表包含当前用户喜欢的帖子。
})
如果你只想要看帖子的一部分，你可以添加额外条件来约束Parse.Query返回的查询值，就像下面一样：
let query = relation.query();
query.equalTo("title", "I'm Hungry");
query.find().then(list=>{
    // 列表包含由当前用户喜欢的帖子，标题为“我饿了”。
})
```

有关Parse.Query的详细信息， 请参阅本指南的查询部分。Parse.Relation行为类似于Parse.Object用于查询的数组，因此您可以在一组对象上执行任何查询，您可以在一个对象上执行Parse.Relation。

数据类型  
到目前为止，我们使用过得值类型有String，Number和Parse.Object。Parse也支持Dates和null。你可以嵌套JSON Object和JSON Array以在单个内存中存储更多的结构化数据Parse.Object。总的来说，对象中的每个字段允许使用以下类型：

* String =&gt; String  
* Number =&gt; Number  
* Bool =&gt; bool  
* Array =&gt; JSON Array  
* Object =&gt; JSON Object  
* Date =&gt; Date  
* File =&gt; Parse.File  
* Pointer =&gt; other Parse.Object  
* Relation =&gt; Parse.Relation  
* Null =&gt; null  
  一些例子：

```js
let number = 42;
let bool = false;
let string = "the number is " + number;
// Date()函数支持sring参数初始化
let date = new Date();
let date = new Date('1995-12-17T03:24:00');
let date = new Date(1995, 11, 17);
let date = new Date(1995, 11, 17, 3, 24, 0)
let array = [string, number];
let object = { number: number, string: string };
let pointer = MyClassName.createWithoutData(objectId);
let BigObject = Parse.Object.extend("BigObject");
let bigObject = new BigObject();
bigObject.set("myNumber", number);
bigObject.set("myBool", bool);
bigObject.set("myString", string);
bigObject.set("myDate", date);
bigObject.set("myArray", array);
bigObject.set("myObject", object);
bigObject.set("anyKey", null); // this value can only be saved to an existing key
bigObject.set("myPointerKey", pointer); // shows up as Pointer <MyClassName> in the Data Browser
bigObject.save();
```

我们不建议存储大量二进制数据，如图像或文档Parse.Object。Parse.Objects不应超过128千字节大小。我们建议您使用Parse.Files存储图像，文档和其他类型的文件。您可以通过实例化Parse.File对象并将其设置在字段上来实现。

> 有关Parse如何处理数据的更多信息，请查看官方数据文档。








