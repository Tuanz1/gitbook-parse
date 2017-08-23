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

```js
var acl = new Parse.ACL();
acl.setPublicReadAccess(true);
acl.setWriteAccess(Parse.User.current().id, true);
```

有时在每个用户的基础上管理权限是不方便的，而且您希望有一组用户受到相同的处理（例如一组具有特殊权限的管理员）。角色是一种特殊的对象，可以让您创建一组可以分配给ACL的用户。角色最好的是，您可以添加和删除角色中的用户，而无需更新限制为该角色的每个对象。要创建只能由管理员写入的对象：

```js
var acl = new Parse.ACL();
acl.setPublicReadAccess(true);
acl.setRoleWriteAccess("admins", true);
```

当然，这段代码假定你已经创建了一个名为“admins”的角色。当您在开发应用程序时设置一小组特殊角色时，这通常是合理的。角色也可以即时创建和更新 - 例如，在建立每个连接后，将新朋友添加到“friendOf\_\_\_”角色。 所有这一切只是开始。应用程序可以通过ACL和类级别权限来强制执行各种复杂的访问模式。例如：

  


* 对于私有数据，读写权限可以限于所有者。 
* 对于留言板上的帖子，作者和“主持人”角色的成员可以具有“写入”权限，而一般公众可以具有“读取”权限。 对于仅由开发人员通过使用主密钥的REST API访问的数据，ACL可以拒绝所有权限。
*  由特权用户组或开发人员创建的数据，如当天的全局消息，可以具有公共读取权限，但限制对“管理员”角色的写入访问。
*  从一个用户发送到另一个用户的消息可以给这些用户“读取”和“写入”访问。

  


对于好奇，这里是ACL的格式，限制对所有者的读取和写入权限（其objectId由“aSaMpLeUsErId”标识），并允许其他用户读取该对象：

```
{
    "*": { "read":true },
    "aSaMpLeUsErId": { "read" :true, "write": true }
}
```

  


以下是使用角色的ACL格式的另一个示例：

```
{
    "role:RoleName": { "read": true },
    "aSaMpLeUsErId": { "read": true, "write": true }
}
```



