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



