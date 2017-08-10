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

您可以自由使用电子邮件地址作为用户名。只需要您的用户输入他们的电子邮件，但填写用户名属性 - Parse.User将正常工作。我们将介绍如何处理重置密码部分。

### 登录

注册之后，用户需要登录自己的账号，此部分需要用login方法

```js
Parse.User.logIn('myusername','mypasswd').then( user =>{
    console.log("sucess to login" + myusername);
    }).catch((user, error)=>{
    //登录失败后的逻辑，解析错误部分，然后提示用户
    });
```

### 邮箱验证

在应用程序设置中启用电子邮件验证允许应用程序为具有确认电子邮件地址的用户预留部分体验。电子邮件验证将emailVerified键添加到Parse.User对象。当Parse.User的电子邮件设置或修改时，emailVerified设置为false。解析然后向用户发送一个将将emailVerified设置为true的链接

emailVerified属性有三种状态

> 1. true - 用户通过点击通过电子邮件发送给他们的链接来确认他或她的电子邮件地址。解析。当用户帐户首次创建时，用户永远不会有真正的价值。
> 2. false - 在Parse.User对象上次刷新时，用户尚未确认他或她的电子邮件地址。如果emailVerified为false，请考虑在Parse.User上调用fetch。
>
> 3. missing-Parse.User是在关闭电子邮件验证或Parse.User没有电子邮件时创建的。

### 当前用户

如果用户每次打开您的应用程序时都必须登录，这将是麻烦的。您可以通过使用缓存的当前Parse.User对象来避免这种情况。

无论何时使用任何注册或登录方法，用户都将被缓存在localStorage中。你可以将此缓存视为会话，并自动假定用户已登录：

```js
var currentUser = Parse.User.current();
if (currentUser) {
    // 用户正常逻辑
} else {
    // 返回登录界面
}
```

如何让用户登出:

```js
Parse.User.logOut().then(() => {
  var currentUser = Parse.User.current();  // 当前用户为空
});
```

### 设置当前用户

如果您创建了自己的身份验证模块，或者以其他方式登录到服务器端的用户，则可以将会话令牌传递给客户端，并使用成为方法。此方法将确保会话令牌在设置当前用户之前是有效的。

```js
Parse.User.become("session-token-here").then((user) => {
  // 将当前用户设置为user
}).catch( error=>{
  // 无法验证令牌
});

```







