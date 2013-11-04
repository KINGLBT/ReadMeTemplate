### 方法顺序
### 枚举改进
    typedef enum NSNumberFormatterStyle : NSUInteger {
        NSNumberFormatterNoStyle,
        NSNumberFormatterDecimalStyle,
        NSNumberFormatterCurrencyStyle,
        NSNumberFormatterPercentStyle,
        NSNumberFormatterScientificStyle,
        NSNumberFormatterSpellOutStyle
    } NSNumberFormatterStyle;
 
### 属性自动绑定

   * 除非开发者在实现文件中提供getter或setter，否则将自动生成
   * 除非开发者同时提供getter和setter，否则将自动生成实例变量
   * 只要写了`synthesis`，无论有没有跟实例变量名，都将生成实例变量
   * `dynamic`优先级高于`synthesis`

### 简写
  * NSNumber:
    * `[NSNumber numberWithChar:‘X’]`简写为`@‘X’`
    * `[NSNumber numberWithInt:12345]`简写为`@12345`
    * `[NSNumber numberWithUnsignedLong:12345ul]`简写为`@12345ul`
    * `[NSNumber numberWithLongLong:12345ll]`简写为`@12345ll`
    * `[NSNumber numberWithFloat:123.45f]`简写为`@123.45f`
    * `[NSNumber numberWithDouble:123.45]`简写为`@123.45`
    * `[NSNumber numberWithBool:YES]`简写为`@YES`
  * NSArray
    * `[NSArray array]`简写为`@[]`
    * `[NSArray arrayWithObject:a]`简写为`@[ a ]`
    * `[NSArray arrayWithObjects:a, b, c, nil]`简写为`@[ a, b, c ]`
  * NSDictionary 
    * `[NSDictionary dictionary]`简写为`@{}`
    * `[NSDictionary dictionaryWithObject:o1 forKey:k1]`简写为`@{ k1 : o1 }`
    * `[NSDictionary dictionaryWithObjectsAndKeys:o1, k1, o2, k2, o3, k3, nil]`简写为`@{ k1 : o1, k2 : o2, k3 : o3 }`
  * 下标
    * `[array objectAtIndex:idx]`简写为`array[idx]`
    * `[array replaceObjectAtIndex:idx withObject:newObj]`简写为`array[idx] = newObj`
    * `[dic objectForKey:key]`简写为`dic[key]`
    * `[dic setObject:object forKey:key]`简写为`dic[key] = newObject`

### 固定桥接
  对于ARC来说，最让人迷惑和容易出错的地方大概就是桥接的概念。由于历史原因，CF对象和NSObject对象的转换一直存在一些微妙的关系，而在引入ARC之后，这些关系变得复杂起来：主要是要明确到底应该是由CF还是由NSObject来负责内存管理的问题。 在Xcode4.4之后，之前区分到底谁拥有对象的工作可以模糊化了。在代码块区间加上`CF_IMPLICIT_BRIDGING_ENABLED`和`CF_IMPLICIT_BRIDGING_DISABLED`，在之前的桥接转换就都可以简单地写作CF和NS之间的强制转换，而不再需要加上`__bridging的`关键字了。
  
@end

 
