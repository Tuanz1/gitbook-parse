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

当一个新的ParseObject被创建并且它符合你订阅的ParseQuery时，你会得到这个事件。该对象是创建的ParseObject。

> UPDATE EVENT-更新事件

```js
subscription.on('update', (object) => {
  console.log('object updated');
});
```

当现有的ParseObject完成您订阅的ParseQuery时，它将被更新（ParseObject在更改之前和之后完成ParseQuery），您将获得此事件。对象是更新的ParseObject。其内容是ParseObject的最新值。

> ENTER EVENT

```js
subscription.on('enter', (object) => {
  console.log('object entered');
});
```

当现有ParseObject的旧值不符合ParseQuery但其新值满足ParseQuery时，您将获得此事件。该对象是输入ParseQuery的ParseObject。其内容是ParseObject的最新值。

> LEAVE EVENT-离开事件

```js
subscription.on('leave', (object) => {
  console.log('object left');
});
```

当现有ParseObject的旧值满足ParseQuery，但其新值不符合ParseQuery时，您将获得此事件。该对象是离开ParseQuery的ParseObject。其内容是ParseObject的最新值。

> DELETE EVENT-删除事件

```js
subscription.on('delete', (object) => {
  console.log('object deleted');
});
```

当现有ParseObject符合ParseQuery的ParseObject被删除时，你会得到这个事件。该对象是被删除的ParseObject。

> CLOSE EVENT-关闭事件

```js
subscription.on('close', () => {
  console.log('subscription closed');
});
```

当客户端失去与LiveQuery服务器的WebSocket连接，我们无法获得任何事件时，您将获得此事件。

## 取消订阅-Unsubscribe

```js
subscription.unsubscribe();
```

如果你想停止从ParseQuery接收事件，你可以取消订阅订阅。 之后，您将不会从订阅对象获取任何事件。

> 关闭实时查询

```js
Parse.LiveQuery.close();
```

完成使用LiveQuery后，可以调用Parse.LiveQuery.close（）。 此功能将关闭到LiveQuery服务器的WebSocket连接，取消自动重新连接，并根据此取消订阅所有订阅。 如果在此之后调用query.subscribe（），我们将创建一个新的WebSocket连接到LiveQuery服务器。

> 设置服务URL-Setup server URL

```js
Parse.liveQueryServerURL='ws://xxxxx'
```

大多数时候你不需要手动设置。 如果您设置了Parse.serverURL，我们将尝试提取主机名，并使用ws：// hostname作为默认的liveQueryServerURL。 但是，如果要定义自己的liveQueryServerURL或使用不同的协议（如wss），则应自行设置。

## 网络套接字状态-WebSocket Status

我们公开三个事件来帮助您监视WebSocket连接的状态：

> 打开事件-OPEN EVENT

```js
Parse.LiveQuery.on('open', () => {
  console.log('socket connection established');
});
```

> 关闭事件-CLOSE EVENT

```js
Parse.LiveQuery.on('close', () => {
  console.log('socket connection closed');
});

```

当我们失去与LiveQuery服务器的WebSocket连接时，您将收到此事件。

> 错误事件-ERROR EVENT

```js
Parse.LiveQuery.on('error', (error) => {
  console.log(error);
});
```

当发生某些网络错误或LiveQuery服务器错误时，您将收到此事件。

## 高级API-Advanced API

在我们的标准API中，我们为您管理全局WebSocket连接，适用于大多数情况。 但是，在某些情况下，就像当您有多个LiveQuery服务器并想要连接到所有这些服务器时，一个WebSocket连接是不够的。 我们已经为这些场景暴露了LiveQueryClient。

## 实时查询客户端-LiveQueryClient

LiveQueryClient是标准WebSocket客户端的包装器。 我们添加了几个有用的方法来帮助您连接/断开LiveQueryServer，并轻松订阅/取消订阅ParseQuery。

> 初始化-INITIALIZE

```js
let Parse = require('parse/node');
let LiveQueryClient = Parse.LiveQueryClient;
let client = new LiveQueryClient({
  applicationId: '',
  serverURL: '',
  javascriptKey: '',
  masterKey: ''
});
```

`applicationId`

* applicationId是必需的，它是您的Parse应用程序的applicationId
* liveQueryServerURL是必需的，它是LiveQuery服务器的URL
* `javascriptKey`
  javascriptKey和masterKey是可选的，它们用于在尝试连接到LiveQuery服务器时验证`LiveQueryClient。如果你设置它们，它们应该匹配你的Parse应用程序。 您可以在此查看LiveQuery协议以获取更多详细信息。`

> OPEN

```js
client.open();
```



调用它之后，LiveQueryClient将尝试向LiveQuery服务器发送连接请求。

> SUBSCRIBE

```js
let query = new Parse.Query('Game');
let subscription = client.subscribe(query, sessionToken); 
```

* query是强制性的，它是您要订阅的ParseQuery

* sessionToken是可选的，如果提供sessionToken，当LiveQuery服务器从解析服务器获取ParseObject的更新时，会尝试检查sessionToken是否满足ParseObject的ACL。 LiveQuery服务器只会将更新发送到sessionToken适合ParseObject的ACL的客户端。 您可以在这里查看LiveQuery协议了解更多详细信息。 您获得的订阅与我们的Standard API相同。 您可以查看我们的标准API，了解如何使用订阅获取事件。

> UBSUBSCRIBE

```js
client.unsubscribe(subscription);
```

订阅是强制性的，它是您要取消订阅的订阅。 调用它之后，您将不会从订阅对象获取任何事件。

> CLOSE

```js
client.close();
```

此函数将关闭与此LiveQueryClient的WebSocket连接，取消自动重新连接，并根据此取消订阅所有订阅。

## 事件处理\(Client\)-Event Handling



我们公开三个事件来帮助您监视LiveQueryClient的状态。  
&gt; OPEN EVENT

