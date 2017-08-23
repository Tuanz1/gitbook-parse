# 实时查询-Live Queries

标准API

正如我们在LiveQuery协议中讨论的，我们维护一个WebSocket连接以与Parse LiveQuery服务器通信。当使用服务器端时，我们使用ws包，并在浏览器中使用window.WebSocket。我们认为在大多数情况下，没有必要直接处理WebSocket连接。因此，我们开发了一个简单的API，让您专注于自己的业务逻辑。

注意：只有在使用JS SDK〜1.8的分析服务器中才支持实时查询。

## 创建订阅-Create a subscription

```js
let query = new Parse.Query('Game');
let subscription = query.subscribe();
```

您获得的订阅实际上是一个事件发射器。有关事件发射器的更多信息，请查看此处。您将通过此订阅获取LiveQuery活动。第一次拨打订阅，我们将尝试打开与您的LiveQuery服务器的WebSocket连接。

## 事件处理-Event Handling

我们定义您将通过订阅对象获得的几种类型的事件：

> OPEN EVENT-打开事件

```js
subscription.on('open', () => {
 console.log('subscription opened');
});
```

当调用query.subscribe（）时，我们向LiveQuery服务器发送订阅请求。当我们从LiveQuery服务器获得确认后，将发出此事件。

当客户端失去与LiveQuery服务器的WebSocket连接时，我们将尝试自动重新连接LiveQuery服务器。如果我们重新连接LiveQuery服务器并成功重新订阅ParseQuery，那么您也将获得此事件。

> CREATE EVENT-创建事件

```js
subscription.on('create', (object) => {
  console.log('object created');
});
```



