# 用户模块

许多app应用都是以用户账户为核心，让用户以安全的方式访问他们的信息。Parse提供一个Parse.User的类，可以自动处理账户管理所需要的大部分逻辑功能。

这个部分教程是让你学会在应用程序中添加账户功能。

Parse.User 也是Parse.Object的一个子类，具有所有相同的功能，如灵活的模式，自动持久化和键值接口。 Parse.Object中的所有方法也存在于Parse.User中。区别在于Parse.User具有特定于用户帐户的特殊添加。

### Parse.User 的属性

Parse.User 有几个与Parse.Object 不同的值：

  + username: 用户名\(必须属性\) 

  + password: 密码\(必须属性\)

  + email: 用户邮箱\(可选，用户密码重置\)

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
```





