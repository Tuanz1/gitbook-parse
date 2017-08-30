# 错误处理

大多数Parse JavaScript函数使用具有回调的对象报告其成功或失败，类似于Backbone“options”对象。 使用的两个主要回调是成功和错误。 只要操作完成而没有错误，就会调用成功。 一般来说，在save或get的情况下，它的参数是Parse.Object，或者是用于find的Parse.Object数组。



对于通过网络与Parse Cloud进行交互时出现的任何类型的错误都会调用错误。 这些错误与连接到云的问题或执行请求的操作的问题有关。 我们来看看另一个例子。 在下面的代码中，我们尝试使用不存在的objectId来获取对象。 Parse Cloud将返回一个错误 - 所以这里是如何在你的回调中正确处理它：

```js

var query = new Parse.Query(Note);
query.get("aBcDeFgH").then(results=> {
    // This function will *not* be called.
    alert("Everything went fine!");
  }).catch(model,error)=>{
    // This will be called.
    // error is an instance of Parse.Error with details about the error.
    if (error.code === Parse.Error.OBJECT_NOT_FOUND) {
      alert("Uh oh, we couldn't find the object!");
    }
  })
});
```

对于影响特定Parse.Object的save和signUp等方法，错误函数的第一个参数将是对象本身，第二个参数将是Parse.Error对象。 这是为了与Backbone类型框架兼容。



有关所有可能的Parse.Error代码的列表，请向下滚动到错误代码，或查看JavaScript API Parse.Error的Parse.Error部分。

# 错误代码

以下是Parse API可以返回的所有错误代码的列表。 您还可以参考[RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html "RFC2616")了解http错误代码的列表。 确保查看错误消息以获取更多详细信息。

## API Issues {#api-issues}

| Name | Code | Description |
| :--- | :--- | :--- |
| `UserInvalidLoginParams` | 101 | Invalid login parameters. Check error message for more details. |
| `ObjectNotFound` | 101 | The specified object or session doesn’t exist or could not be found. Can also indicate that you do not have the necessary permissions to read or write this object. Check error message for more details. |
| `InvalidQuery` | 102 | There is a problem with the parameters used to construct this query. This could be an invalid field name or an invalid field type for a specific constraint. Check error message for more details. |
| `InvalidClassName` | 103 | Missing or invalid classname. Classnames are case-sensitive. They must start with a letter, and a-zA-Z0-9\_ are the only valid characters. |
| `MissingObjectId` | 104 | An unspecified object id. |
| `InvalidFieldName` | 105 | An invalid field name. Keys are case-sensitive. They must start with a letter, and a-zA-Z0-9\_ are the only valid characters. Some field names may be reserved. Check error message for more details. |
| `InvalidPointer` | 106 | A malformed pointer was used. You would typically only see this if you have modified a client SDK. |
| `InvalidJSON` | 107 | Badly formed JSON was received upstream. This either indicates you have done something unusual with modifying how things encode to JSON, or the network is failing badly. Can also indicate an invalid utf-8 string or use of multiple form encoded values. Check error message for more details. |
| `CommandUnavailable` | 108 | The feature you tried to access is only available internally for testing purposes. |
| `NotInitialized` | 109 | You must call Parse.initialize before using the Parse library. Check the Quick Start guide for your platform. |
| `ObjectTooLarge` | 116 | The object is too large. Parse Objects have a max size of 128 kilobytes. |
| `ExceededConfigParamsError` | 116 | You have reached the limit of 100 config parameters. |
| `InvalidLimitError` | 117 | An invalid value was set for the limit. Check error message for more details. |
| `InvalidSkipError` | 118 | An invalid value was set for skip. Check error message for more details. |
| `OperationForbidden` | 119 | The operation isn’t allowed for clients due to class-level permissions. Check error message for more details. |
| `CacheMiss` | 120 | The result was not found in the cache. |
| `InvalidNestedKey` | 121 | An invalid key was used in a nested JSONObject. Check error message for more details. |
| `InvalidACL` | 123 | An invalid ACL was provided. |
| `InvalidEmailAddress` | 125 | The email address was invalid. |
| `DuplicateValue` | 137 | Unique field was given a value that is already taken. |
| `InvalidRoleName` | 139 | Role’s name is invalid. |
| `ReservedValue` | 139 | Field value is reserved. |
| `ExceededCollectionQuota` | 140 | You have reached the quota on the number of classes in your app. Please delete some classes if you need to add a new class. |
| `ScriptFailed` | 141 | Cloud Code script failed. Usually points to a JavaScript error. Check error message for more details. |
| `FunctionNotFound` | 141 | Cloud function not found. Check that the specified Cloud function is present in your Cloud Code script and has been deployed. |
| `JobNotFound` | 141 | Background job not found. Check that the specified job is present in your Cloud Code script and has been deployed. |
| `SuccessErrorNotCalled` | 141 | success/error was not called. A cloud function will return once response.success\(\) or response.error\(\) is called. A background job will similarly finish execution once status.success\(\) or status.error\(\) is called. If a function or job never reaches either of the success/error methods, this error will be returned. This may happen when a function does not handle an error response correctly, preventing code execution from reaching the success\(\) method call. |
| `MultupleSuccessErrorCalls` | 141 | Can’t call success/error multiple times. A cloud function will return once response.success\(\) or response.error\(\) is called. A background job will similarly finish execution once status.success\(\) or status.error\(\) is called. If a function or job calls success\(\) and/or error\(\) more than once in a single execution path, this error will be returned. |
| `ValidationFailed` | 142 | Cloud Code validation failed. |
| `WebhookError` | 143 | Webhook error. |
| `InvalidImageData` | 150 | Invalid image data. |
| `UnsavedFileError` | 151 | An unsaved file. |
| `InvalidPushTimeError` | 152 | An invalid push time was specified. |
| `HostingError` | 158 | Hosting error. |
| `InvalidEventName` | 160 | The provided analytics event name is invalid. |
| `ClassNotEmpty` | 255 | Class is not empty and cannot be dropped. |
| `AppNameInvalid` | 256 | App name is invalid. |
| `MissingAPIKeyError` | 902 | The request is missing an API key. |
| `InvalidAPIKeyError` | 903 | The request is using an invalid API key. |

## Push related errors {#push-related-errors}

| Name | Code | Description |
| :--- | :--- | :--- |
| `IncorrectType` | 111 | A field was set to an inconsistent type. Check error message for more details. |
| `InvalidChannelName` | 112 | Invalid channel name. A channel name is either an empty string \(the broadcast channel\) or contains only a-zA-Z0-9\_ characters and starts with a letter. |
| `InvalidSubscriptionType` | 113 | Bad subscription type. Check error message for more details. |
| `InvalidDeviceToken` | 114 | The provided device token is invalid. |
| `PushMisconfigured` | 115 | Push is misconfigured in your app. Check error message for more details. |
| `PushWhereAndChannels` | 115 | Can’t set channels for a query-targeted push. You can fix this by moving the channels into your push query constraints. |
| `PushWhereAndType` | 115 | Can’t set device type for a query-targeted push. You can fix this by incorporating the device type constraints into your push query. |
| `PushMissingData` | 115 | Push is missing a ‘data’ field. |
| `PushMissingChannels` | 115 | Non-query push is missing a ‘channels’ field. Fix by passing a ‘channels’ or ‘query’ field. |
| `ClientPushDisabled` | 115 | Client-initiated push is not enabled. Check your Parse app’s push notification settings. |
| `RestPushDisabled` | 115 | REST-initiated push is not enabled. Check your Parse app’s push notification settings. |
| `ClientPushWithURI` | 115 | Client-initiated push cannot use the “uri” option. |
| `PushQueryOrPayloadTooLarge` | 115 | Your push query or data payload is too large. Check error message for more details. |
| `InvalidExpirationError` | 138 | Invalid expiration value. |
| `MissingPushIdError` | 156 | A push id is missing. Deprecated. |
| `MissingDeviceTypeError` | 157 | The device type field is missing. Deprecated. |

## File related errors {#file-related-errors}

| Name | Code | Description |
| :--- | :--- | :--- |
| `InvalidFileName` | 122 | An invalid filename was used for Parse File. A valid file name contains only a-zA-Z0-9\_. characters and is between 1 and 128 characters. |
| `MissingContentType` | 126 | Missing content type. |
| `MissingContentLength` | 127 | Missing content length. |
| `InvalidContentLength` | 128 | Invalid content length. |
| `FileTooLarge` | 129 | File size exceeds maximum allowed. |
| `FileSaveError` | 130 | Error saving a file. |
| `FileDeleteError` | 131 | File could not be deleted. |

## Installation related errors {#installation-related-errors}

| Name | Code | Description |
| :--- | :--- | :--- |
| `InvalidInstallationIdError` | 132 | Invalid installation id. |
| `InvalidDeviceTypeError` | 133 | Invalid device type. |
| `InvalidChannelsArrayError` | 134 | Invalid channels array value. |
| `MissingRequiredFieldError` | 135 | Required field is missing. |
| `ChangedImmutableFieldError` | 136 | An immutable field was changed. |

## Purchase related errors {#purchase-related-errors}

| Name | Code | Description |
| :--- | :--- | :--- |
| `ReceiptMissing` | 143 | Product purchase receipt is missing. |
| `InvalidPurchaseReceipt` | 144 | Product purchase receipt is invalid. |
| `PaymentDisabled` | 145 | Payment is disabled on this device. |
| `InvalidProductIdentifier` | 146 | The product identifier is invalid. |
| `ProductNotFoundInAppStore` | 147 | The product is not found in the App Store. |
| `InvalidServerResponse` | 148 | The Apple server response is not valid. |
| `ProductDownloadFilesystemError` | 149 | The product fails to download due to file system error. |

## User related errors {#user-related-errors}

| Name | Code | Description |
| :--- | :--- | :--- |
| `UsernameMissing` | 200 | The username is missing or empty. |
| `PasswordMissing` | 201 | The password is missing or empty. |
| `UsernameTaken` | 202 | The username has already been taken. |
| `UserEmailTaken` | 203 | Email has already been used. |
| `UserEmailMissing` | 204 | The email is missing, and must be specified. |
| `UserWithEmailNotFound` | 205 | A user with the specified email was not found. |
| `SessionMissing` | 206 | A user object without a valid session could not be altered. |
| `MustCreateUserThroughSignup` | 207 | A user can only be created through signup. |
| `AccountAlreadyLinked` | 208 | An account being linked is already linked to another user. |
| `InvalidSessionToken` | 209 | The device’s session token is no longer valid. The application should ask the user to log in again. |

## Linked services errors {#linked-services-errors}

| Name | Code | Description |
| :--- | :--- | :--- |
| `LinkedIdMissing` | 250 | A user cannot be linked to an account because that account’s id could not be found. |
| `InvalidLinkedSession` | 251 | A user with a linked \(e.g. Facebook or Twitter\) account has an invalid session. Check error message for more details. |
| `InvalidGeneralAuthData` | 251 | Invalid auth data value used. |
| `BadAnonymousID` | 251 | Anonymous id is not a valid lowercase UUID. |
| `FacebookBadToken` | 251 | The supplied Facebook session token is expired or invalid. |
| `FacebookBadID` | 251 | A user with a linked Facebook account has an invalid session. |
| `FacebookWrongAppID` | 251 | Unacceptable Facebook application id. |
| `TwitterVerificationFailed` | 251 | Twitter credential verification failed. |
| `TwitterWrongID` | 251 | Submitted Twitter id does not match the id associated with the submitted access token. |
| `TwitterWrongScreenName` | 251 | Submitted Twitter handle does not match the handle associated with the submitted access token. |
| `TwitterConnectFailure` | 251 | Twitter credentials could not be verified due to problems accessing the Twitter API. |
| `UnsupportedService` | 252 | A service being linked \(e.g. Facebook or Twitter\) is unsupported. Check error message for more details. |
| `UsernameSigninDisabled` | 252 | Authentication by username and password is not supported for this application. Check your Parse app’s authentication settings. |
| `AnonymousSigninDisabled` | 252 | Anonymous users are not supported for this application. Check your Parse app’s authentication settings. |
| `FacebookSigninDisabled` | 252 | Authentication by Facebook is not supported for this application. Check your Parse app’s authentication settings. |
| `TwitterSigninDisabled` | 252 | Authentication by Twitter is not supported for this application. Check your Parse app’s authentication settings. |
| `InvalidAuthDataError` | 253 | An invalid authData value was passed. Check error message for more details. |
| `LinkingNotSupportedError` | 999 | Linking to an external account not supported yet with signup\_or\_login. Use update instead. |

## Client-only errors {#client-only-errors}

| Name | Code | Description |
| :--- | :--- | :--- |
| `ConnectionFailed` | 100 | The connection to the Parse servers failed. |
| `AggregateError` | 600 | There were multiple errors. Aggregate errors have an “errors” property, which is an array of error objects with more detail about each error that occurred. |
| `FileReadError` | 601 | Unable to read input for a Parse File on the client. |
| `XDomainRequest` | 602 | A real error code is unavailable because we had to use an XDomainRequest object to allow CORS requests in Internet Explorer, which strips the body from HTTP responses that have a non-2XX status code. |

## Operational issues {#operational-issues}

| Name | Code | Description |
| :--- | :--- | :--- |
| `RequestTimeout` | 124 | The request was slow and timed out. Typically this indicates that the request is too expensive to run. You may see this when a Cloud function did not finish before timing out, or when a`Parse.Cloud.httpRequest`connection times out. |
| `InefficientQueryError` | 154 | An inefficient query was rejected by the server. Refer to the Performance Guide and slow query log. |
| `RequestLimitExceeded` | 155 | This application has exceeded its request limit \(legacy Parse.com apps only\). |
| `TemporaryRejectionError` | 159 | An application’s requests are temporary rejected by the server \(legacy Parse.com apps only\). |
| `DatabaseNotMigratedError` | 428 | You should migrate your database as soon as possible \(legacy Parse.com apps only\). |

## Other issues {#other-issues}

| Name | Code | Description |
| :--- | :--- | :--- |
| `OtherCause` | -1 | An unknown error or an error unrelated to Parse occurred. |
| `InternalServerError` | 1 | Internal server error. No information available. |
| `ServiceUnavailable` | 2 | The service is currently unavailable. |
| `ClientDisconnected` | 4 | Connection failure. |



