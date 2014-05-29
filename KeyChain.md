### Keychain 基础

  钥匙串中的条目称为`SecItem`，但它是存储在`CFDictionary`中的。`SecItem`有五类：
  
  * 通用密码: `kSecClassGenericPassword`
  * 互联网密码: `kSecClassInternetPassword`
  * 证书: `kSecClassCertificate`
  * 密钥: `kSecClassKey`
  * 身份: `kSecClassIdentity`

### SecItem 属性

  在大多数情况下，我们用到的都是通用密码`kSecClassGenericPassword`。许多问题都是开发人员尝试用互联网密码造成的。互联网密码要复杂得多，而且相比之下优势寥寥无几，除非开发Web浏览器，否则没必要用它。

  最后，我们需要在钥匙串中搜索需要的内容。密钥有很多个部分可用来搜索，但最好的办法是将自己的标识符赋给它，然后搜索。通用密码条目都包含属性`kSecAttrGeneric`，可以用它来存储标识符。
  
  钥匙串中的条目都有几个可搜索的**属性**和一个加密过的**值**。对于通用密码条目，比较重要的属性有账户`kSecAttrAccount`、服务`kSecAttrService`和标识符`kSecAttrGeneric`。而值通常是密码。
  
  * 1.kSecClass key 定义属于那一种类型的keyChain
  * 2.不同的类型包含不同的Attributes,这些attributes定义了这个item的具体信息
  * 3.每个item可以包含一个密码项来存储对应的密码
  
### SecItem 操作

    // 查询
    OSStatus SecItemCopyMatching(CFDictionaryRef query, CFTypeRef *result);
    // 添加
    OSStatus SecItemAdd(CFDictionaryRef attributes, CFTypeRef *result);
    // 更新
    KeyChain中的ItemOSStatus SecItemUpdate(CFDictionaryRef query, CFDictionaryRef attributesToUpdate);
    // 删除
    KeyChain中的ItemOSStatus SecItemDelete(CFDictionaryRef query)
    


@end
