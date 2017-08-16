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

```js
var file = new Parse.File("myfile.zzz", fileData, "image/png");
```

但是最常见的是HTML5应用，您将需要使用带有文件上传控件的html表单。在现代浏览器上，这很简单。创建文件输入标签，允许用户从本地驱动器中选择一个文件进行上传：

```js
//在ionic项目中ion-input不允许传file类型
<input type="file" id="imgUpload" />
```

在ionic项目中使用以下代码获取图片文件

```js
let fileCtrl = document.getElementById('imgUpload')["files"];
//由于files可以存储多个文件，但是我们需要的知识第一个文件
if(fileCtrl.length >0){
    let img = fileCtrl[0];
    let name = "demo.jpg";
    
    let parseFile = new Parse.File(name,img);
    }
```



