# 查询
我们已经知道了Parse.Query是如何使用get从Parse云端查询一个对象的，下面还有许多其他方法，比如你可以一次查询多个对象，可以在一个查询上限制条件等。
## 基本查询

在许多情况下，想要检索到你想要的对象get是远远不够的。 Parse.Query提供了不同的方法来检索对象的列表，而不仅仅是一个单独的对象。

一般的方法是创建一个Parse.Query，并设置条件，然后使用find检索与Parse.Objects匹配的数组。 例如，要检索具有特定玩家姓名的得分数，请使用equalTo方法来约束键值。

```js
let GameScore = Parse.Object.extend("GameScore");
let query = new Parse.Query(GameScore);
query.equalTo("playerName", "Dan Stemkoski");
query.find().then(result=> {
    alert("Successfully retrieved " + results.length + " scores.");
    // 使用返回的Parse.Object值执行某些操作
    for (let i = 0; i < results.length; i++) {
      let object = results[i];
      alert(object.id + ' - ' + object.get('playerName'));
    }
  }).catch(err=>{
    alert("Error: " + error.code + " " + error.message);
    });
```

## 查询条件的限制

有几种方法可以通过Parse.Query来约束查找到的对象。 您可以使用notEqualTo过滤出具有特定键值对的对象：

```js
query.notEqualTo("playerName", "Michael Yabuti");
```

您可以给出多个约束，如果匹配所有约束，对象将只在结果中。 换句话说，它就像一个**与**约束条件。

```js
query.notEqualTo("playerName", "Michael Yabuti");
query.greaterThan("playerAge", 18);
```

您可以通过设置来限制结果数量。 默认情况下，结果限制为100，但从1到1000的任何值都是有效的限制：

```js
qeery.limit(10);
//返回最多10条结果
```

如果你确切想要一个结果，一个更方便的替代方法可能是使用first而不是使用find。

> 类似mongodb中的findOne函数。

```js
let GameScore = Parse.Object.extend("GameScore");
let query = new Parse.Query(GameScore);
query.equalTo("playerEmail", "dstemkoski@example.com");
query.first().then(result=> {
    // 成功检索对象。
  }).catch(err=>{
  alert("Error: " + error.code + " " + error.message);
  });
```

可以通过设置跳过特定数目的结果，例如在分页中使用

```js
query.skip(10);
//跳过前十条结果
```

对于可排序类型（如数字和字符串），您可以控制返回结果的顺序：

> 类似与mongodb中的sort函数

```
//通过得分字段按升序排列结果
query.ascending("score");

// 按比例字段降序排列结果
query.descending("score");
```

对于可排序类型，您还可以在查询中使用比较：

```js
// 限制 wins 小于50
query.lessThan("wins", 50);

//限制 wins 小于等于50
query.lessThanOrEqualTo("wins", 50);

// 限制 wins 大于50
query.greaterThan("wins", 50);

// 限制 wins 大于等于50
query.greaterThanOrEqualTo("wins", 50);
```

如果您想要检索匹配列表中任何值的对象，可以使用containsIn，提供可接受值的数组。 这通常在单个查询替换多个查询时是非常有用的。 例如，如果您想要检索特定列表中任何玩家的分数：

```js
// 找到Jonathan，Dario或Shawn的得分
query.containedIn("playerName",
         ["Jonathan Walsh", "Dario Wunsch", "Shawn Simon"]);
```

如果要检索具有特定键值的对象，可以使用exists。 相反，如果要在没有特定键值的情况下检索对象，则可以使用doNotExist。

```js
// 查找已设置分数的对象
query.exists("score");

// 查找没有设置分数的对象
query.doesNotExist("score");
```

您可以使用matchesKeyInQuery方法来获取对象，它的一个键值与另一个查询一组对象产生的结果中的键值匹配。 例如，如果您有一个包含运动队的类，并且将用户的家乡存储在用户的类中，则可以发出一个查询来查找其家乡团队具有获胜记录的用户列表。 查询将如下所示：

```js
let Team = Parse.Object.extend("Team");
let teamQuery = new Parse.Query(Team);
teamQuery.greaterThan("winPct", 0.5);
let userQuery = new Parse.Query(Parse.User);
userQuery.matchesKeyInQuery("hometown", "city", teamQuery);
userQuery.find().then(result=> {
    // 结果有一个拥有一个获胜记录的家乡团队的用户名单
})
.catch(err=>{
    });
```

相反，要获取一个键与另一个查询产生的对象集合中的键值不匹配的对象，请使用doNotMatchKeyInQuery。 例如，要找到家乡队失去记录的用户：

```js
let losingUserQuery = new Parse.Query(Parse.User);
losingUserQuery.doesNotMatchKeyInQuery("hometown", "city", teamQuery);
losingUserQuery.find().then(result=> {
    // 结果有一个拥有失败记录的家乡团队的用户名单
})
.catch(err=>{
    });
```

您可以通过使用键列表来调用select来限制返回的字段。 要检索仅包含得分和玩家姓名字段的文档（以及特殊的内置字段，如objectId，createdAt和updatedAt）：

```js
let GameScore = Parse.Object.extend("GameScore");
let query = new Parse.Query(GameScore);
query.select("score", "playerName");
query.find().then(function(results=> {
  // each of results will only have the selected fields available.
}))
.catch(err=>{
    });
```

稍后可以通过在返回的对象上调用fetch来获取剩余的字段：

```js
query.first().then(function(result=> {
  // only the selected fields of the object will now be available here.
  return result.fetch();
  )
}).then(function(result=> {
  // all fields of the object will now be available here.
}));
```

数组值查询

对于具有数组类型的键，可以通过以下方式找到键的数组值包含2的对象：

```js
// 查找arrayKey中的数组包含2的对象。
query.equalTo("arrayKey", 2);
您还可以找到键的数组值包含每个元素2,3和4的对象，其中包含以下内容：
//查找arrayKey中的数组包含所有元素2,3和4的对象。
query.containsAll("arrayKey", [2, 3, 4]);
查询字符串值
```

如果您正在尝试实现一般的搜索功能，我们建议您查看此博文：[http://blog.parse.com/learn/engineering/implementing-scalable-search-on-a-nosql-backend/](http://blog.parse.com/learn/engineering/implementing-scalable-search-on-a-nosql-backend/)  
使用startsWith限制以特定字符串开头的字符串值。 与MySQL LIKE运算符类似，它被索引，因此对于大型数据集是有效的：

```js
// Finds barbecue sauces that start with "Big Daddy's".
let query = new Parse.Query(BarbecueSauce);
query.startsWith("name", "Big Daddy's");
```

以上示例将匹配任何BarbecueSauce对象，其中“name”String键中的值以“Big Daddy's”开头。 例如，“Big Daddy’s”和““Big Daddy’s BBQ”都会匹配，但是“big daddy’s”还是“BBQ Sauce: Big Daddy’s”都不会。

具有正则表达式的约束查询非常昂贵，特别是对于具有超过100,000条记录的类。 Parse 限制了在任何时间可以在特定应用程序上运行多少这样的操作。

关系查询

有几种方法来发起关系数据查询。 如果要检索字段与特定Parse.Object匹配的对象，可以像其他数据类型一样使用equalTo。 例如，如果每个注释在其帖子字段中都有一个Post对象，您可以为特定的帖子获取注释：

```js
// Assume Parse.Object myPost was previously created.
let query = new Parse.Query(Comment);
query.equalTo("post", myPost);
query.find().then(result=> {
    // 评论现在包含myPost的评论
  })
.catch(err=>{
 });
```

如果您想要检索字段包含与其他查询匹配的Parse.Object的对象，则可以使用matchesQuery。 请注意，默认限制为100，最大限制为1000也适用于内部查询，因此使用大型数据集时，可能需要仔细构造查询才能获得所需的结果。 为了找到包含图像的帖子的评论，您可以：

```js
let Post = Parse.Object.extend("Post");
let Comment = Parse.Object.extend("Comment");
let innerQuery = new Parse.Query(Post);
innerQuery.exists("image");
let query = new Parse.Query(Comment);
query.matchesQuery("post", innerQuery);
query.find().then(result=> {

    // 评论现在包含有图片的帖子的评论。
 })
.catch(err=>{
 });
```

如果要检索字段包含与其他检索不匹配的Parse.Object的对象，则可以使用doNotMatchQuery。 为了找到没有图像的帖子的评论，你可以做：

```js
let Post = Parse.Object.extend("Post");
let Comment = Parse.Object.extend("Comment");
let innerQuery = new Parse.Query(Post);
innerQuery.exists("image");
var query = new Parse.Query(Comment);
query.doesNotMatchQuery("post", innerQuery);
query.find().then(result=> {
    // 评论现在不包含有图片的帖子的评论。
 })
.catch(err=>{
 });
```

您还可以通过objectId进行关系查询：

```js
let post = new Post();
post.id = "1zEcyElZ80";
query.equalTo("post", post);
```

在某些情况下，您希望在一个查询中返回多种类型的相关对象。 您可以使用include方法。 例如，假设您正在检索最后十条评论，并且您想要同时检索相关的帖子：

```js
let query = new Parse.Query(Comment);

// Retrieve the most recent ones
query.descending("createdAt");

// Only retrieve the last ten
query.limit(10);

// Include the post data with each comment
query.include("post");

query.find().then(result=> {
    // Comments now contains the last ten comments, and the "post" field
    // has been populated. For example:
    for (var i = 0; i < comments.length; i++) {
      // This does not require a network access.
      var post = comments[i].get("post");
  }
  }).catch(err=>{
 });
```

你也可以做多级包括使用点符号。 如果你想包括发表评论和帖子的作者，你可以做：

query.include\(\["post.author"\]\);  
您可以通过多次调用包括多个字段来发出查询。 此功能也适用于Parse.Query助手，如first和get。

### 统计查询

警告：计数查询的速率限制为每分钟最多160个请求。 他们还可以为具有超过1,000个对象的类返回不准确的结果。 因此，建议您的应用程序避免这种计数操作（例如使用计数器）。

如果您只需要计算与查询匹配的对象数量，但不需要检索匹配的所有对象，则可以使用count代替find。 例如，计算特定玩家玩过的游戏数量：

```js
let GameScore = Parse.Object.extend("GameScore");
let query = new Parse.Query(GameScore);
query.equalTo("playerName", "Sean Plott");
query.count().then(result=> {
    // 计数请求成功。 显示计数
    alert("Sean has played " + count + " games");
  })
 .catch(err=>{
    // 请求失败
});
```

### 复合查询
复合查询
更复杂的查询情况下，你可能需要用到组合查询。一个组合查询是多个子查询的组合(如"and或"or")。
需要注意的是，我们不支持在组合查询的子查询中使用GeoPint或者非过滤类型的约束条件。
#### 或查询
如果要查找与几个检索中的一个匹配的对象，可以使用Parse.Query.or方法来构造一个查询，该查询是传入的查询的OR。例如，如果您要查找具有很多 wins，你可以做到：

```js
let lotsOfWins = new Parse.Query("Player");
lotsOfWins.greaterThan("wins", 150);

let fewWins = new Parse.Query("Player");
fewWins.lessThan("wins", 5);

let mainQuery = Parse.Query.or(lotsOfWins, fewWins);
mainQuery.find().then(result=> {
// 结果包含玩家的名单，无论是赢得了很多游戏或仅赢得了几场比赛。
  }).catch(err=>{
    // 有一个错误。
});
```
#### 与查询
如果你想查询和所有条件都符合的数据，通常只需要一次请求。你可以添加额外的条件，它实际上就是与查询：
```ts
var query = new Parse.Query("User");
query.greaterThan("age", 18);
query.greaterThan("friends", 0);
query.find()
  .then(function(results) {
    // results contains a list of users both older than 18 and having friends.
  })
  .catch(function(error) {
    // There was an error.
  });
  ```
  但如果这个世界真的有这么简单那就好了。有时你可能需要用到与组合查询，Parse.Query.and方法可以构建一个由传入的子程序组成的与查询。比如你想查询用户年龄为16或者18，并且ta的好友少于两人的用户,你可以这样：
  ```ts
  var age16Query = new Parse.Query("User");
age16Query.equalTo("age", 16);

var age18Query = new Parse.Query("User");
age18Query.equalTo("age", 18);

var friends0Query = new Parse.Query("User");
friends0Query.equalTo("friends", 0);

var friends2Query = new Parse.Query("User");
friends2Query.greaterThan("friends", 2);

var mainQuery = Parse.Query.and(
  Parse.Query.or(age16Query, age18Query),
  Parse.Query.or(friends0Query, friends2Query)
);
mainQuery.find()
  .then(function(results) {
    // results contains a list of users in the age of 16 or 18 who have either no friends or at least 2 friends
    // results: (age 16 or 18) and (0 or >2 friends)
  })
  .catch(function(error) {
    // There was an error.
  });
  ```



