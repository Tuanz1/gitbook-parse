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

### 用户对象的安全

Parse.User类默认保护。存储在Parse.User中的数据只能由该用户修改。默认情况下，任何客户端仍然可以读取数据。因此，一些Parse.User对象被认证并可以被修改，而其他对象是只读的。

具体来说，除非使用经过身份验证的方法（如logIn或signUp）获取Parse.User，否则您无法调用任何保存或删除方法。这确保只有用户可以更改自己的数据。

安全策略说明:\(根本没看过\)

```js
var user = Parse.User.logIn("my_username", "my_password", {
  success: function(user) {
    user.set("username", "my_new_username");  // attempt to change username
    user.save(null, {
      success: function(user) {
        // This succeeds, since the user was authenticated on the device

        // Get the user from a non-authenticated method
        var query = new Parse.Query(Parse.User);
        query.get(user.objectId, {
          success: function(userAgain) {
            userAgain.set("username", "another_username");
            userAgain.save(null, {
              error: function(userAgain, error) {
                // This will error, since the Parse.User is not authenticated
              }
            });
          }
        });
      }
    });
  }
});
```

从Parse.User.current（）获取的Parse.User将始终被认证。  
如果需要检查Parse.User是否经过身份验证，则可以调用已认证的方法。您不需要通过经过验证的方法获取的Parse.User对象来检查身份验证。

### 其它对象的安全\(这个部分不是特别熟悉\)

适用于Parse.User的相同安全模型可以应用于其他对象。对于任何对象，您可以指定哪些用户可以读取对象，哪些用户可以修改对象。为了支持这种类型的安全性，每个对象都有一个由Parse.ACL类实现的　ACL\(access control list\) 列表。

使用Parse.ACL的最简单方法是指定对象只能由单个用户读取或写入。这通过使用Parse.User初始化Parse.ACL来完成：new Parse.ACL（user）生成一个限制对该用户的访问的Parse.ACL。与对象的任何其他属性一样，对象的ACL被保存时被更新。因此，创建只能由当前用户访问的私人注释：

```js
let Note = Parse.Object.extend("Note");
let privateNote = new Note();
privateNote.set("content", "This note is private!");
privateNote.setACL(new Parse.ACL(Parse.User.current()));
privateNote.save();
```

此笔记只能由当前用户访问，但该用户登录的任何设备都可以访问此功能。此功能对于要在多个设备上访问用户数据的应用程序（例如个人待办事项）很有用名单。

也可以在每个用户的基础上授予权限。您可以使用setReadAccess和setWriteAccess单独添加到Parse.ACL的权限。例如，假设您有一个消息将发送到一组几个用户，其中每个用户都有权读取和删除该消息：

```js
let Message = Parse.Object.extend("Message");
let groupMessage = new Message();
let groupACL = new Parse.ACL();

// userList is an array with the users we are sending this message to.
for (var i = 0; i < userList.length; i++) {
  groupACL.setReadAccess(userList[i], true);
  groupACL.setWriteAccess(userList[i], true);
}

groupMessage.setACL(groupACL);
groupMessage.save();
```

您也可以使用setPublicReadAccess和setPublicWriteAccess一次向所有用户授予权限。这允许模式，如在留言板上发表评论。例如，创建一个只能由作者编辑的帖子，但可以由任何人阅读：

```js
let publicPost = new Post();
let postACL = new Parse.ACL(Parse.User.current());
postACL.setPublicReadAccess(true);
publicPost.setACL(postACL);
publicPost.save();
```

被禁止的操作（如删除不具有写入访问权限的对象）会导致Parse.Error.OBJECT\_NOT\_FOUND错误代码。为了安全起见，这样可以防止客户端区分哪些对象id存在但是是安全的，哪些对象id根本不存在。

其它的应用安全策略后面会有说明

### 重置用户密码

这是一个事实，一旦你将密码引入系统，用户就会忘记它们。在这种情况下，Parse 的库提供了一种让他们安全地重置密码的方法。要启动密码重设流程，请向用户询问他们的电子邮件地址，然后致电：

```js
Parse.User.requestPasswordReset("email@example.com").then( ()=>
  // Password reset request was sent successfully
  }).catch(error=>{
    // Show the error message somewhere
    alert("Error: " + error.code + " " + error.message);
});
```

这将尝试将给定的电子邮件与用户的电子邮件或用户名字段进行匹配，并向他们发送密码重置电子邮件。通过这样做，您可以选择让用户使用他们的电子邮件作为用户名，或者您可以分别收集它们并将其存储在电子邮件字段中。

> 密码重置流程如下：
>
> 1. 用户通过输入他们的电子邮件请求重置密码。
>
> 2. Parse发送电子邮件到他们的地址，一个特殊的密码重置链接。
>
> 3. 用户点击重置链接，重置密码
>
> 4. 用户使用新密码登录

请注意，此流程中的消息传递将通过您在Parse上创建此应用程序时指定的名称来引用您的应用程序。

### 检索

要查询用户，您可以简单地为Parse.Users创建一个新的Parse.Query:

```js
let query = new Parse.Query(Parse.User);
query.equalTo("gender", "female");  // find all the women
query.find({
  success: function(women) {
    // Do stuff
  }
});
```

### 关联性\(Associations\)

> 谷歌翻译为协会

涉及到Parse.User工作权限的关联性。例如，假设您正在制作一个博客应用程序。要存储用户的新帖子并检索他们的所有帖子：

```js

let user = Parse.User.current();

// 创建新的post
var Post = Parse.Object.extend("Post");
var post = new Post();
post.set("title", "My New Post");
post.set("body", "This is some great content.");
post.set("user", user);
post.save(null, {
  success: function(post) {
    // Find all posts by the current user
    var query = new Parse.Query(Post);
    query.equalTo("user", user);
    query.find({
      success: function(usersPosts) {
        // userPosts contains all of the posts by the current user.
      }
    });
  }
});
```



