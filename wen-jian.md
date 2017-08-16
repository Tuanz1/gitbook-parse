# parse文件

### 创建一个Parse.File文件

Parse.File允许您将应用程序文件存储在云中，否则可能会太大或麻烦，以适应常规Parse.Object。最常见的用例是存储图像，但您也可以将其用于文档，视频，音乐和任何其他二进制数据（最多10MB）。

Parse.File入门很简单。有几种方法来创建文件。第一个是使用base64编码的String。

```js
var base64 = "V29ya2luZyBhdCBQYXJzZSBpcyBncmVhdCE=";
var file = new Parse.File("myfile.txt", { base64: base64 });
```

或者，您可以从字节值数组创建一个文件：

```js
var bytes = [ 0xBE, 0xEF, 0xCA, 0xFE ];
var file = new Parse.File("myfile.txt", bytes);
```

  


Parse将根据文件扩展名自动检测您正在上传的文件类型，但可以使用第三个参数指定Content-Type：

