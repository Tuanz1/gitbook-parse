# 会话\(sessions\)

会话表示登录到设备的用户的实例。当用户登录或注册时会自动创建会话。当用户注销时，它们会被自动删除。每个用户安装对都有一个不同的Session对象;如果用户从已经登录的设备发出登录请求，则该用户之前该Session的对象将被自动删除。会话对象存储在Session类中的Parse中，您可以在Parse.com数据浏览器上查看它们。我们提供一组API来管理您的应用程序中的会话对象。

会话是Parse对象的子类，因此您可以以与在Parse上操作正常对象相同的方式来查询，更新和删除会话。因为Parse Cloud会在您登录或注册用户时自动创建会话，所以您不应手动创建Session对象，除非您正在构建“对于IoT解析”应用程序（例如Arduino或Embedded C）。删除会话将会将用户从当前正在使用此会话令牌的设备中记录下来。

与其他Parse对象不同，Session类没有Cloud Code触发器。所以你不能为Session类注册一个beforeSave或afterSave处理程序。

### 会话的属性

Session对象具有以下特殊字段：

> sessionToken（只读）：用于在Parse API请求上进行身份验证的字符串令牌。在会话查询的响应中，只有当前的Session对象将包含会话令牌。
>
> user：（只读）指向此会话用户对象的指针。
>
> createdWith（只读）：有关如何创建此会话的信息（例如{“action”：“login”，“authProvider”：“password”}）。
>
> > action 可以具有值：login，signup，create或upgrade。create操作是当开发人员通过保存Session对象手动创建会话。upgrade操作是当用户从旧会话令牌升级到可撤销会话时。
> >
> > authProvider可以有值：password，anonymous，Facebook或Twitter。
>
> restricted（只读）：布尔值是否限制此会话。



