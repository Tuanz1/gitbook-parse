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
* 由特权用户组或开发人员创建的数据，如当天的全局消息，可以具有公共读取权限，但限制对“管理员”角色的写入访问。
* 从一个用户发送到另一个用户的消息可以给这些用户“读取”和“写入”访问。

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

### POINTER PERMISSIONS {#pointer-permissions}

指针权限是一种特殊类型的类级权限，可以根据存储在这些对象的指针字段中的用户在类中的每个对象上创建虚拟ACL。例如，给定具有所有者字段的类，对所有者设置读取指针权限将使该类中的每个对象只能由该对象的所有者字段中的用户读取。对于具有发件人和接收者字段的类，对接收者字段的读取指针权限以及发件人字段上的读取和写入指针权限将使得用户在发件人和接收者字段中可读取类中的每个对象，并且可写仅由发件人字段中的用户。

给定对象通常已经具有应该具有对象权限的用户的指针，指针权限提供了一个简单而快速的解决方案，用于使用已经存在的数据保护您的应用程序，这不需要编写任何客户端代码或云代码。

指针权限就像虚拟ACL。它们不会出现在ACL列中，如果您熟悉ACL的工作原理，您可以将其视为ACL。在上面的示例中，使用发件人和接收者，每个对象的行为就好像它具有以下ACL：

```
{
    "<SENDER_USER_ID>": {
        "read": true,
        "write": true
    },
    "<RECEIVER_USER_ID>": {
        "read": true
    }
}
```

请注意，这个ACL实际上并不是在每个对象上创建的。当您添加或删除指针权限时，任何现有的ACL都不会被修改，如果用指针权限创建的虚拟ACL和对象上已经存在的实际ACL允许，任何尝试与对象交互的用户只能与该对象交互互动。因此，有时可能会混淆指针权限和ACL，因此我们建议为没有设置很多ACL的类使用指针权限。幸运的是，如果您以后决定使用云代码或ACL来保护应用程序，则很容易删除指针权限。

要求认证许可（需要PARSE-SERVER&gt;= 2.3.0） 启动版本2.3.0，parse-server引入了一个新的Class Level Permission requiresAuthentication。此CLP可防止任何未经身份验证的用户执行CLP保护的操作。 例如，您希望允许经过身份验证的用户从您的应用程序中查找和发布公告，并且您的管理员角色具有所有权限，您将设置CLP：

```js
// POST http://my-parse-server.com/schemas/Announcement
// Set the X-Parse-Application-Id and X-Parse-Master-Key header
// body:
{
  classLevelPermissions:
  {
    "find": {
      "requireAuthentication": true,
      "role:admin": true
    },
    "get": {
      "requireAuthentication": true,
      "role:admin": true
    },
    "create": { "role:admin": true },
    "update": { "role:admin": true },
    "delete": { "role:admin": true },
  }
}
```

###  REQUIRES AUTHENTICATION PERMISSION \(REQUIRES PARSE-SERVER &gt;= 2.3.0\) {#requires-authentication-permission-requires-parse-server---230}

从2.3.0版本开始，parse-server引入了一个新的Class Level Permission requiresAuthentication。 此CLP可防止任何未经身份验证的用户执行CLP保护的操作。



例如，您希望允许经过身份验证的用户从您的应用程序中查找和发布公告，并且您的管理员角色具有所有权限，您将设置CLP：

```js
// 这个权限设置有点类似于mongodb的用户管理
// POST http://my-parse-server.com/schemas/Announcement
// Set the X-Parse-Application-Id and X-Parse-Master-Key header
// body:
{
  classLevelPermissions:
  {
    "find": {
      "requireAuthentication": true,
      "role:admin": true
    },
    "get": {
      "requireAuthentication": true,
      "role:admin": true
    },
    "create": { "role:admin": true },
    "update": { "role:admin": true },
    "delete": { "role:admin": true },
  }
}
```

作用:

* 非认证的用户将无法做任何事情。
* 经过身份验证的用户（具有有效sessionToken的任何用户）将能够读取该类中的所有对象。
* 属于admin角色的用户将能够执行所有操作。

> ：警告：请注意，这绝对不会保护您的内容，如果允许任何人登录到您的服务器，每个客户端仍然可以查询此对象。

### CLP AND ACL INTERACTION {#clp-and-acl-interaction}

类级别权限（CLP）和访问控制列表（ACL）是保护应用程序的强大工具，但并不总是完全与您期望的方式进行交互。 它们实际上代表了每个请求必须通过的两个独立的安全层，以返回正确的信息或进行预期的更改。 这些层，一个在类级别，一个在对象级别，如下所示。 请求必须通过两层检查才能授权。 请注意，尽管行为类似于ACL，指针权限是一种类级别权限，因此请求必须通过指针权限检查才能通过CLP检查。

![](/assets/Api Request.png)  
如您所见，当您使用CLP和ACL两者时，用户是否有权提出请求可能会变得复杂。我们来看一个例子来更好地了解CLP和ACL如何交互。说我们有一个Photo类，一个对象，photoObject。我们的应用程序中有2个用户，user1和user2。现在让我们说，我们在Photo类上设置一个Get CLP，禁用public Get，但允许user1执行Get。现在我们还在photoObject上设置一个ACL，以允许只读user2的读取（包括GET）。



您可能会期望这将允许user1和user2都可以获取PhotoObject，但是由于CLP层的身份验证和ACL层始终都有效，所以它实际上使得user1和user2都不能获取photoObject。如果user1尝试获取PhotoObject，它将通过CLP层验证，但是将被拒绝，因为它不通过ACL层。以同样的方式，如果user2尝试获取PhotoObject，它也将在CLP认证层拒绝。



现在看看使用指针权限的示例。说我们有一个Post类，一个对象是myPost。我们的应用程序，海报和查看器中有2个用户。假设我们添加了一个指针权限，它允许Post类的“创建者”字段中的任何人对该对象进行读写访问，对于myPost对象，海报是该字段中的用户。对象上还有一个访问控制列表，可以向读者提供读取访问权限。您可能会希望这样可以让海报读取和编辑myPost，并且查看器读取它，但是查看器将被指针权限拒绝，海报将被ACL拒绝，因此再次，用户将无法访问目的。



由于CLP，指针权限和ACL之间的复杂交互，我们建议在一起使用时小心。通常只能使用CLP来禁用特定请求类型的所有权限，然后对其他请求类型使用指针权限或ACL。例如，您可能要禁用照片类的删除，但是在照片上放置指针权限，因此创建它的用户可以对其进行编辑，但不要删除它。由于指针权限和ACL相互作用特别复杂，我们通常建议仅使用这两种安全机制之一。

###  SECURITY EDGE CASES {#security-edge-cases}

Parse中有一些特殊的类不遵循所有其他类的所有相同的安全规则。 并不是所有的[Class-Level Permissions \(CLPs\)](http://docs.parseplatform.org/js/guide/#class-level-permissions)或[Access Control Lists \(ACLs\)](http://docs.parseplatform.org/js/guide/#object-level-access-control)，它们如何被定义，并且这些异常被记录在案。 这里“正常行为”是指CLP和ACL正常工作，而任何其他特殊行为都在脚注中描述。  


|  |
| :--- |


| Method | `_User` | `_Installation` |
| :--- | :--- | :--- |
| Get | normal behaviour \[1, 2, 3\] | ignores CLP, but not ACL |
| Find | normal behavior \[3\] | master key only \[6\] |
| Create | normal behavior \[4\] | ignores CLP |
| Update | normal behavior \[5\] | ignores CLP, but not ACL \[7\] |
| Delete | normal behavior \[5\] | master key only \[7\] |
| Add Field | normal behavior | normal behavior |

1. 登录, 或`/1/login REST API 不遵守用户类上获取CLP`, 登录仅基于用户名和密码，不能使用CLP进行禁用。

2. 根据REST API中的/ 1 / users / me，检索当前用户或成为基于会话令牌的用户不遵守用户类上的Get CLP。

3. 读取ACL不适用于登录用户。例如，如果所有用户具有禁用读取的ACL，则对用户执行查询查询仍将返回已登录的用户。但是，如果查找CLP被禁用，则尝试对用户执行查找仍将返回错误。

4. 创建CLP也适用于注册。因此，在用户类中禁用创建CLP也会禁止用户在没有主密钥的情况下注册。

5. 用户只能自己更新和删除。更新和删除的公共CLP可能仍然适用。例如，如果禁用用户类的公共更新，则用户无法自己编辑。但是无论用户写入ACL是什么，该用户仍然可以自行更新或删除，并且没有其他用户可以更新或删除该用户。然而，一如以往，使用主密钥允许用户更新其他用户，不受CLP或ACL的影响。

6. 获取安装请求按照正常的ACL进行。除非提供installId作为约束，否则不允许查找不带主密钥的请求。

7. 对安装的更新请求确实遵循安装上定义的ACL，但删除请求仅限于主密钥。有关安装如何工作的更多信息，请查看REST指南的安装部分。

##  {#data-integrity-in-cloud-code}

  


