# 实时查询-Live Queries

  


标准API

 正如我们在LiveQuery协议中讨论的，我们维护一个WebSocket连接以与Parse LiveQuery服务器通信。当使用服务器端时，我们使用ws包，并在浏览器中使用window.WebSocket。我们认为在大多数情况下，没有必要直接处理WebSocket连接。因此，我们开发了一个简单的API，让您专注于自己的业务逻辑。

 注意：只有在使用JS SDK〜1.8的分析服务器中才支持实时查询。

