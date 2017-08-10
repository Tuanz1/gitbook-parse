# 用户模块

许多app应用都是以用户账户为核心，让用户以安全的方式访问他们的信息。Parse提供一个Parse.User的类，可以自动处理账户管理所需要的大部分逻辑功能。

这个部分教程是让你学会在应用程序中添加账户功能。

Parse.User 也是Parse.Object的一个子类，具有所有相同的功能，如灵活的模式，自动持久化和键值接口。 Parse.Object中的所有方法也存在于Parse.User中。区别在于Parse.User具有特定于用户帐户的特殊添加。

### Parse.User 的属性

Parse.User 有几个与Parse.Object 不同的值：

* username: 用户名\(必须属性\)

* password: 密码\(必须属性\)

* email: 用户邮箱\(可选，用户密码重置\)

### 注册

你的应用程序第一件事可能就是要求用户注册，以下代码是注册的范例：

```js
let user = new Parse.User();
//设置一下属性之前，一般先用正则表达式进行匹配
user.set('username',"yourusername");
user.set('password',"yourpasswd");
user.set('email','test@qq.com');
//可以设置其它属性
user.set('phone','123456789');

//调用signUp方法
user.signUp().then( user=>{
    console.log("成功注册");
    }).catch( (user, error)=>{
    alert("Error" + error.code + " "+error.message);
    });
```

此调用将在您的Parse App中异步创建一个新用户。在此之前，它还会检查以确保用户名和电子邮件都是唯一的。此外，它使用bcrypt安全地在云中安装密码。我们从不以明文形式存储密码，也不会以明文形式传送密码给客户端。

请注意，我们使用了signUp方法，而不是save方法。新的Parse.Users应始终使用signUp方法创建。可以通过调用save来完成用户的后续更新。

如果注册不成功，您应该读取返回的错误对象。最可能的情况是用户名或电子邮件已被其他用户占用。app应该根据错误返回提示用户修改信息。

