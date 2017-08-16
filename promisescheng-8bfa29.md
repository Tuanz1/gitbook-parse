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



