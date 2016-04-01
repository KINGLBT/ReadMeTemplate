## TCWebCodesSDK验证码SDK使用说明
### 1.概述
  SDK工具包目录结构说明:
    
    TCWebCodesSDK.framework:包含TCWebCodesSDK.framework
    TCWebCodesSDKDemo:⽰示例⼯工程,演⽰示了如何使⽤用TCWebCodesSDK.framework
    TCWebCodesSDK API文档
    
  本SDK运行环境与项目要求:
    
    适⽤用于iOS6.0及以上的系统版本
    

### 2.接口说明
    
    /**
    开始加载H5
    @param url，腾讯云返回的验证码URL
    @param frame, webView控件大小
    @param callback, 回调, 成功/失败可以通过 resultJSON[@"ret"] 判断，0为成功，非0为失败
    @return 返回H5的webView控件
    @note 该函数用于开始加载，并且返回H5的webView控件，用于显示
    @warning 请不要设置返回webView控件的回调
    */
    - (UIWebView*)startLoad:(NSString*)url webFrame:(CGRect)frame callback:(void (^)(NSDictionary *resultJSON, UIWebView *webView))callback;
    
  主要函数，通过腾讯云返回的验证码URL创建一个用于显示的WebView控件，验证结果将通过callback回调；
  
  
    /**
    返回单例
    */
    + (instancetype)sharedBridge;  
    
  返回 TCWebCodesBridge 全局对象；
  
  
    /**
    设置是否显示H5的返回头部
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
  
