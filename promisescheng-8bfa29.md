Promises

除了回调之外，Parse JavaScript SDK中的每个异步方法返回一个Promise。有了承诺，您的代码可以比使用回调的嵌套代码更干净。

### 介绍

promises代表了JavaScript编程中的下一个伟大范例。但是了解他们为什么这么伟大是不容易的事情。它的核心是承诺，这是一项任务的结果，可能已经完成也可能没有完成任务。 Promise唯一的接口要求是具有一个调用函数，可以在承诺得到满足或失败时给予回调。这在[CommonJS承诺/ A](http://wiki.commonjs.org/wiki/Promises/A "Common JS Promises/ A proposal")提案中概述。例如，考虑保存Parse.Object，这是一个异步操作。在旧的回调范例中，您的代码将如下所示：

```js
object.save({ key: value }, {
  success: function(object) {
    // the object was saved.
  },
  error: function(object, error) {
    // saving the object failed.
  }
});
```

在新的Promise范式中，同样的代码将如下所示：

```js
object.save({key: vaue}).then( result =>{
    //对象保存成功
    }).catch(err=>{
    //对象保存失败
    })
```

没有太大的不同，对吧？那么什么大事呢？那么承诺的真正力量来自于将其中的多个链接在一起。调用promise.then（func）返回一个新的承诺，直到func完成才能实现。但是，使用func的方式有一个非常特别的事情。如果提供的回调函数返回一个新的承诺，那么当回覆的回应满足之前，那么返回的承诺将不会被满足。行为的细节在[Promises / A +](https://github.com/promises-aplus/promises-spec "Promises / A +")提案中进行了说明。这是一个复杂的话题，但也许一个例子会更清楚。

想象你正在编写登录代码，找到一个对象，然后更新它。在旧的回调范式中，你最终会得到我们所说的金字塔代码：

```js
Parse.User.logIn("user", "pass", {
  success: function(user) {
    query.find({
      success: function(results) {
        results[0].save({ key: value }, {
          success: function(result) {
            // the object was saved.
          }
        });
      }
    });
  }
});
```

这很可笑，即使没有任何错误处理代码。但是由于承诺链工作的方式，代码现在可以更加平淡：

```js
Parse.User.logIn("user", "pass").then((user) => {
  return query.find();
}).then((results) => {
  return results[0].save({ key: value });
}).then((result) => {
  // 保存查询到的对象
});
```

> 相比较原先的嵌套，回调函数，使用承诺的链式写法是代码更简洁，更容易理解。

### Then 方法

每个Promise都有一个命名的方法，它需要一个回调函数。如果承诺得到解决，则调用第一个回调.then\(\)，而如果承诺被拒绝，则调用第二个回调.catch\(\)。

### 链式承诺-Chaining Promises Together

承诺有点神奇，因为他们让你链接他们没有嵌套。如果一个承诺的回调返回一个新的承诺，那么第一个将不会被解决，直到第二个。这样，您可以执行多个操作，而不会产生您将使用回调获得的金字塔代码。

### 承诺的错误处理-Error Handling With Promises

原先的承诺部分加上错误处理:

```js
Parse.User.logIn("user", "pass", {
  success: function(user) {
    query.find({
      success: function(results) {
        results[0].save({ key: value }, {
          success: function(result) {
            // the object was saved.
          },
          error: function(result, error) {
            // An error occurred.
          }
        });
      },
      error: function(error) {
        // An error occurred.
      }
    });
  },
  error: function(user, error) {
    // An error occurred.
  }
});
```

因为承诺知道他们是否已经履行或失败，他们可以传播错误，而不会在遇到错误处理程序之前调用任何回调函数。例如，上面的代码可以简单地写成：

```js
Parse.User.logIn("user", "pass").then((user) => {
  return query.find();
}).then((results) => {
  return results[0].save({ key: value });
}).then((result) => {
  // 保存查询到的对象
}).catch(err){
  //错误处理部分
  });
```

一般来说，开发人员认为一个失败的承诺是异步的等同于抛出异常。事实上，如果一个回调传递给然后抛出一个错误，那么返回的promise将会失败并出现该错误。如果链中的任何Promise返回错误，则将跳过所有成功回调，直到遇到错误回调。错误回调可以转换错误，或者它可以通过返回未被拒绝的新Promise来处理该错误。你可以想到被拒绝的承诺有点像抛出异常。错误回调就像一个可以处理错误或重新抛出错误的catch块。

```js
// 官方代码 es5版本，拥有两个catch错误模块
var query = new Parse.Query("Student");
query.descending("gpa");
query.find().then(function(students) {
  students[0].set("valedictorian", true);
  // Force this callback to fail.
  return Parse.Promise.error("There was an error.");

}).then(function(valedictorian) {
  // Now this will be skipped.
  return query.find();

}).then(function(students) {
  // This will also be skipped.
  students[1].set("salutatorian", true);
  return students[1].save();
}, function(error) {
  // This error handler WILL be called. error will be "There was an error.".
  // Let's handle the error by returning a new promise.
  return Parse.Promise.as("Hello!");

}).then(function(hello) {
  // Everything is done!
}, function(error) {
  // This isn't called because the error was already handled.
});
```

拥有长链成功回调通常很方便，最后只有一个错误处理程序。

### 创建承诺-Creating Promises

先前使用的大部分函数其实已经使用了承诺，类似save\(\), find\(\)函数等，但是，对于有些场景需要自己创建一个新的承诺，然后根据实际执行结果调用解析，或者拒绝其触发回调。

```js
let successful = new Parse.Promise();
successful.resolve("The good result.");

let failed = new Parse.Promise();
failed.reject("An error message.");
```

如果您知道创建时承诺的结果，则可以使用一些方便的方法。

```js
var successful = Parse.Promise.as("The good result.");

var failed = Parse.Promise.error("An error message.");
```

### 串联承诺-Promises in Series

当你想连续做一系列的任务时，承诺很方便，每一个等待以前完成。例如，假设您想删除博客上的所有评论。

```js
var query = new Parse.Query("Comments");
query.equalTo("post", post);

query.find().then(function(results) {
  // Create a trivial resolved promise as a base case.
  var promise = Parse.Promise.as();
  _.each(results, function(result) {
    // For each item, extend the promise with a function to delete it.
    promise = promise.then(function() {
      // Return a promise that will be resolved when the delete is finished.
      return result.destroy();
    });
  });
  return promise;

}).then(function() {
  // Every comment was deleted.
});
```

当你想连续做一系列的任务时，承诺很方便，每一个等待以前完成。例如，假设您想删除博客上的所有评论。

```js
//暂时没有用过，依旧使用官网代码
var query = new Parse.Query("Comments");
query.equalTo("post", post);

query.find().then(function(results) {
  // Create a trivial resolved promise as a base case.
  var promise = Parse.Promise.as();
  _.each(results, function(result) {
    // For each item, extend the promise with a function to delete it.
    promise = promise.then(function() {
      // Return a promise that will be resolved when the delete is finished.
      return result.destroy();
    });
  });
  return promise;

}).then(function() {
  // Every comment was deleted.
});
```

### 平行承诺-Promises in Parallel

您也可以使用promises来并行执行多个任务，使用when方法。您可以一次启动多个操作，并使用Parse.Promise.when创建一个新的承诺，当所有的输入承诺得到解决时，它将被解决。如果没有一个传递的承诺失败，新的承诺将会成功;否则，最后一个错误将失败。并行执行操作将比串行执行更快，但可能会消耗更多的系统资源和带宽。

```js
//暂时没有用过，依旧使用官网代码
var query = new Parse.Query("Comments");
query.equalTo("post", post);

query.find().then(function(results) {
  // Collect one promise for each delete into an array.
  var promises = [];
  _.each(results, function(result) {
    // Start this delete immediately and add its promise to the list.
    promises.push(result.destroy());
  });
  // Return a new promise that is resolved when all of the deletes are finished.
  return Parse.Promise.when(promises);

}).then(function() {
  // Every comment was deleted.
});
```



