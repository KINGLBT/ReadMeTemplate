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


### 主题
  目录:

    mkdir -p ~/Library/Developer/Xcode/UserData/FontAndColorThemes/
    cd ~/Library/Developer/Xcode/UserData/FontAndColorThemes/

### 主题文件
  *.dvtcolortheme

### 编译
  跳过警告

    #pragma clang diagnostic push
    #pragma clang diagnostic ignored "-Wdeprecated-declarations"
    - (void) methodUsingDeprecatedStuff {
        //use deprecated stuff
    }
    #pragma clang diagnostic pop 

### Debug
  工程Edit Scheme，添加环境变量`NSZombieEnabled`=YES（`MallocStackLogging`=YES）
  * `NSZombieEnabled`: 僵尸Debug，当对已释放的指针变量访问时，会打印该变量地址；
  * `MallocStackLogging`: 打印该地址的内存变化函数调用栈（具体打印方法`malloc_history <pid> <address>`）

### Info.plist的Key定义
  * UIRequiresPersistentWiFi 在程序中弹出wifi选择的key（系统设置中需要将wifi提示打开）
  * UIAppFonts 内嵌字体（http://www.minroad.com/?p=412 有详细介绍）
  * UIApplicationExitsOnSuspend 程序是否在后台运行，自己在进入后台的时候exit(0)是很傻的办法
  * UIBackgroundModes 后台运行时的服务，具体看iOS4的后台介绍
  * UIDeviceFamily array类型（1为iPhone和iPod touch设备，2为iPad)
  * UIFileSharingEnabled 开启itunes共享document文件夹
  * UILaunchImageFile 相当于Default.png（更名而已）
  * UIPrerenderedIcon icon上是否有高光
  * UIRequiredDeviceCapabilities 设备需要的功能（具体点击这里查看）
  * UIStatusBarHidden 状态栏隐藏（和程序内的区别是在于显示Default.png已经生效）
  * UIStatusBarStyle 状态栏类型
  * UIViewEdgeAntialiasing 是否开启抗锯齿
  * CFBundleDisplayName app显示名
  * CFBundleIconFile、CFBundleIconFiles 图标
  * CFBundleName 与CFBundleDisplayName的区别在于这个是短名，16字符之内
  * CFBundleVersion 版本
  * CFBundleURLTypes 自定义url，用于利用url弹回程序
  * CFBundleLocalizations 本地资源的本地化语言，用于itunes页面左下角显示本地话语种
  * CFBundleDevelopmentRegion 也是本地化相关，如果用户所在地没有相应的语言资源，则用这个key的value来作为默认

### arm版本列表
  * armv6：iPhone 2G/3G，iPod 1G/2G
  * armv7：iPhone 3GS/4/4s，iPod 3G/4G，iPad 1G/2G/3G
  * armv7s：iPhone5

@end



 




