# 角色-Roles

随着应用程序的扩展范围和用户群体的增长，您可能会发现自己需要对用户链接的ACL可以提供的对数据的访问进行更粗略的控制。为了解决这个要求，Parse支持一种基于角色的访问控制。角色提供了一种合法的方法，将用户具有共享访问权限分组到您的Parse数据。角色是包含用户和其他角色的命名对象。授予角色的任何权限都会隐式授予其用户以及其所包含的任何角色的用户。



例如，在具有策略内容的应用程序中，您可能会有多个被认为是“主持人”的用户，并且可以修改和删除其他用户创建的内容。您可能还有一组用户是“管理员”，并且允许与版主具有相同的权限，但也可以修改应用程序的全局设置。通过将用户添加到这些角色，您可以确保新用户可以成为主持人或管理员，而无需为每个用户手动授予每个资源的权限。



我们提供了一个名为Parse.Role的专门类，代表客户端代码中的这些角色对象。 Parse.Role是Parse.Object的子类，并且具有所有相同的功能，例如灵活的模式，自动持久化和键值接口。 Parse.Object上的所有方法也存在于Parse.Role上。不同的是，Parse.Role有一些特定于角色管理的补充。

## Parse.Role 属性 {#parserole-properties}

Parse.Role有几个属性，它将它与Parse.Object分开：



1. ame：角色的名称。 此值是必需的，只能在创建角色时将其设置一次。 名称必须包含字母数字字符，空格， - 或\_ 。该名称将用于识别角色，而不需要其objectId。
2. users：与将继承授予包含角色的权限的用户集的关系。
3. roles：与用户和角色将继承授予包含角色的权限的角色集的关系。

## 角色对象的安全性



Parse.Role与Parse中的所有其他对象使用相同的安全性方案（ACL），除了需要显式设置ACL。 通常，只有具有极高提升权限的用户（例如主用户或管理员）应该能够创建或修改角色，因此您应该相应地定义其ACL。 请记住，如果您向用户授予对Parse.Role的写入权限，该用户可以将其他用户添加到角色，甚至完全删除角色。



要创建一个新的Parse.Role，你可以写：

```js

// By specifying no write privileges for the ACL, we can ensure the role cannot be altered.
var roleACL = new Parse.ACL();
roleACL.setPublicReadAccess(true);
var role = new Parse.Role("Administrator", roleACL);
role.save();
```

您可以通过Parse.Role上的“users”和“roles”关系添加应该继承新角色权限的用户和角色：

```js

var role = new Parse.Role(roleName, roleACL);
role.getUsers().add(usersToAddToRole);
role.getRoles().add(rolesToAddToRole);
role.save();
```

为角色分配ACL时，请格外小心，只有那些应该具有修改权限的用户才能修改它们。

## 其他对象的基于角色的安全



现在您已经创建了一组在应用程序中使用的角色，您可以使用它们与ACL来定义用户将获得的权限。 每个Parse.Object可以指定一个Parse.ACL，它提供一个访问控制列表，指示哪些用户和角色应被授予对对象的读取或写入访问权限。



给对象的读取或写入权限是直接的。 你可以使用Parse.Role：

```js

var moderators = /* Query for some Parse.Role */;
var wallPost = new Parse.Object("WallPost");
var postACL = new Parse.ACL();
postACL.setRoleWriteAccess(moderators, true);
wallPost.setACL(postACL);
wallPost.save();
```

您可以通过指定ACL的名称来避免查询角色：

```js
var wallPost = new Parse.Object("WallPost");
var postACL = new Parse.ACL();
postACL.setRoleWriteAccess("Moderators", true);
wallPost.setACL(postACL);
wallPost.save();
```

## 角色层次结构

如上所述，一个角色可以包含另一个角色，在两个角色之间建立父子关系。 这种关系的后果是，授予父角色的任何权限被隐式授予其所有子角色。



这些类型的关系通常在具有用户管理的内容（如论坛）的应用程序中找到。 用户的一小部分是“管理员”，最高级别的访问权限可以调整应用程序的设置，创建新的论坛，设置全局消息等。 另一组用户是“主持人”，谁负责确保用户创建的内容仍然适用。 任何具有管理员权限的用户也应被授予任何版主的权限。 要建立这种关系，您将使您的“管理员”角色扮演“主持人”的角色，如下所示：

```js
var administrators = /* Your "Administrators" role */;
var moderators = /* Your "Moderators" role */;
moderators.getRoles().add(administrators);
moderators.save();
```



