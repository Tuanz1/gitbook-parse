## 数据在云代码中的完整性-Data Integrity in Cloud Code {#data-integrity-in-cloud-code}

对于大多数应用程序，关心密钥，类级别权限和对象级ACL，您需要保持应用程序和用户数据的安全。有时候，你会遇到一个不够的边缘的情况。对于其他一切，有云代码。



云代码允许您将JavaScript上传到Parse的服务器，我们将为您运行。与可能被篡改的用户设备上运行的客户端代码不同，Cloud Code保证是您编写的代码，因此可以信任更多的责任。



云代码的一个特别常见的用例是防止无效数据被存储。对于这种情况，特别重要的是恶意客户端无法绕过验证逻辑。



要创建验证函数，Cloud Code允许您为您的类实现一个beforeSave触发器。每当保存对象时都会运行这些触发器，并允许您修改对象或完全拒绝保存。例如，这是您如何在保存触发之前创建云代码，以确保每个用户都设置了一个电子邮件地址：

```js
Parse.Cloud.beforeSave(Parse.User, function(request, response) {
  var user = request.object;
  if (!user.get("email")) {
    response.error("Every user must have an email address.");
  } else {
    response.success();
  }
});
```

验证可以锁定您的应用程序，以便只有某些值可以接受。 您也可以使用afterSave验证来规范您的数据（例如，格式化所有电话号码或货币相同）。 您可以直接从客户端应用程序中保留访问Parse数据的大部分生产力优势，但您也可以即时为您的数据强制执行某些不变量。



需要验证的常见场景包括：



1. 确保电话号码的格式正确
2. 消毒数据使其格式正常化
3. 确保电子邮件地址看起来像一个真正的电子邮件地址
4. 要求每个用户指定特定范围内的年龄
5. 不让用户直接更改计算字段
6. 不允许用户删除特定对象，除非满足某些条件

## 在云代码中实现业务逻辑-Implementing Business Logic in Cloud Code

虽然验证在“云代码”中通常是有意义的，但是可能某些操作特别敏感，并应尽可能仔细地保护。 在这种情况下，您可以完全删除客户端的权限或逻辑，而是将所有这些操作转换为云代码功能。



当调用Cloud Code功能时，它可以使用可选的{useMasterKey：true}参数来获得修改用户数据的能力。 使用主密钥，您的云代码功能可以覆盖任何ACL并写入数据。 这意味着它将绕过您在前几节中已经放置的所有安全机制。



假设你想允许用户“喜欢”一个Post对象，而不会给它们对该对象的完全写入权限。 您可以通过让客户端调用Cloud Code功能而不是修改Post本身来完成此操作：



应该仔细使用主密钥。 将useMasterKey设置为true，仅在需要该安全覆盖的各个API函数调用中：

```js
Parse.Cloud.define("like", function(request, response) {
  var post = new Parse.Object("Post");
  post.id = request.params.postId;
  post.increment("likes");
  post.save(null, { useMasterKey: true }).then(function() {
    // If I choose to do something else here, it won't be using
    // the master key and I'll be subject to ordinary security measures.
    response.success();
  }, function(error) {
    response.error(error);
  });
});
```

Cloud Code的一个非常常见的用例是向特定用户发送推送通知。 一般来说，客户端不能被信任直接发送推送通知，因为他们可以修改警报文本，或推送他们不应该的人。 您的应用程序的设置将允许您设置是否启用“客户端推送”; 我们建议您确保已禁用。 相反，您应该编写云代码函数，以验证在发送推送之前要推送和发送的数据。

