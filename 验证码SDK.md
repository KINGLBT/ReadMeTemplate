## TCWebCodesSDK验证码SDK使用说明
### 1.概述
  SDK工具包目录结构说明:
    
    TCWebCodesSDK.framework:包含TCWebCodesSDK.framework
    TCWebCodesSDKDemo:⽰示例⼯工程,演⽰示了如何使⽤用TCWebCodesSDK.framework
    
  本SDK运行环境与项目要求:
    
    适⽤用于iOS6.0及以上的系统版本
    

### 2.接口说明
    
    /**
    加载验证码H5，并且返回webView控件
    @param url，腾讯云返回的验证码URL
    @param frame, webView控件大小
    @param callback, H5验证码验证结果回调, 成功/失败可以通过 resultJSON[@"ret"] 判断，0为成功，非0为失败
    @return webView控件，用于显示
    @warning 由于内部需要webView判断跳转, 请不要设置返回webView控件的delegate
    */
    - (UIWebView*)startLoad:(NSString*)url webFrame:(CGRect)frame callback:(void (^)(NSDictionary *resultJSON, UIWebView *webView))callback;
    
  主要函数，通过腾讯云返回的验证码URL创建一个用于显示的WebView控件，验证结果将通过callback回调；
  
  
    /**
    返回单例
    */
    + (instancetype)sharedBridge;  
    
  返回 TCWebCodesBridge 全局对象；
  
  
    /**
    设置是否显示H5的内部导航头部
    */
    @property (nonatomic) BOOL showHeader;
    
  配置函数，设置是否显示H5页面的头部，默认不显示；
  
  
    /**
    设置其余属性
    @note 该接口是为了后续扩展
    @warning 请设置value为基本类型: string, number
    */
    - (void)setCapValue:(id)aValue forKey:(id<NSCopying>)aKey;  
    
  可扩展函数，用于设置后续显示H5界面的一些样式；
  
