# Object-Level Access Control

一旦你锁定了架构和类级别的权限，现在是考虑用户访问数据的时候了。对象级访问控制使一个用户的数据与另一个用户的数据保持分离，因为类中的某些不同对象需要不同的人访问。例如，用户的私人个人数据只能由他们访问。  
Parse还支持匿名用户对那些希望存储和保护用户特定数据而不需要明确登录的应用程序的概念。  
当用户登录到应用程序时，它们会与Parse启动会话。通过这个会话，他们可以添加和修改自己的数据，但是不能修改其他用户的数据。

### ACCESS CONTROL LISTS {#access-control-lists}

通过访问控制列表（通常称为ACL）来控制谁可以访问哪些数据的最简单的方法。 ACL背后的想法是每个对象都有用户和角色的列表，以及用户或角色具有的权限。用户需要读取权限（或必须属于具有读取权限的角色）才能检索对象的数据，并且用户需要写入权限（或必须属于具有写入权限的角色）才能更新或删除该权限目的。

拥有用户后，您可以开始使用ACL。记住：用户可以通过传统的用户名/密码登录，通过第三方登录系统（如Facebook或Twitter），甚至使用Parse的自动匿名用户功能创建。要将当前用户的数据ACL设置为不公开可读，您只需要执行以下操作：

```js
var user = Parse.User.current();
user.setACL(new Parse.ACL(user));
```

大多数应用程序应该这样做如果您存储任何敏感的用户数据（例如电子邮件地址或电话号码），则需要设置此类ACL，以便其他用户不可见该用户的私人信息。如果一个对象没有ACL，那么每个人都可以读写。唯一的例外是\_User类。我们从不允许用户写对方的数据，但默认情况下可以读取它们。 （如果您作为开发人员需要更新其他\_User对象，请记住，您的主密钥可以提供此功能）。 为了使每个对象创建用户专用ACL都非常简单，我们有一种方法可以设置将为您创建的每个新对象使用的默认ACL：

```js
// not available in the JavaScript SDK
```

如果您希望用户拥有一些公开的数据，另外一些是私有的，那么最好有两个独立的对象。您可以从公共数据添加一个指向私有数据的指针。

```js
var privateData = Parse.Object.extend("PrivateUserData");
privateData.setACL(new Parse.ACL(Parse.User.current()));
privateData.set("phoneNumber", "555-5309");

Parse.User.current().set("privateData", privateData);
```

  


当然，您可以对对象设置不同的读写权限。例如，这是您如何为用户创建公共帖子的ACL，任何人都可以读取它：

