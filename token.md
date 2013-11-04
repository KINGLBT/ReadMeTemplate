关于第三方App(QQ)调用QQ安全中心App处理验证消息的实现方案aqtokenSDK(iOS)
=======

### 交互简要

  QQ安全中心App和第三方App或者WebApp之间通过aqtokenSDK进行数据交互，aqtokenSDK数据交互依赖URL的形式，原理为系统通过检测URL的scheme来决定运行什么App来处理该URL，具体形式见下：
  
### aqtokenSDK的URL格式

  * scheme: `qmtoken`为QQ安全中心App的适配URL Scheme，当系统检测到以`qmtoken://`开头时，会启动QQ安全中心App来处理该请求。
  * fragment: fragment分两种定义类型：`qmtoken-1`为第三方App请求QQ安全中心App；`qmtoken-2`为网页请求QQ安全中心App。fragment将会影响到数据的传递和解析方式，具体参见query。
  * query: query依赖于fragment类型:
    * fragment = `qmtoken-1`: `src`的数据表示共享内存地址，共享内存里面存放了第三方App传递给QQ安全中心的数据。
    * fragment = `qmtoken-2`: `src`的数据表示请求类型，目前`1`表示一键验证请求，`uin`的数据为请求验证QQ号。


### 第三方App和QQ安全中心App的数据交互(共享内存)

  第三方App需要嵌入aqtokenSDK.framework来实现请求QQ安全中心App一键验证，采用共享内存的方式有几点好处：
  * 数据格式灵活且安全: 第三方App请求数据可以为任何形式，且请求数据不会在URL请求明文里。
  * 简化第三方App请求QQ安全中心验证消息的接入过程(采用API的形式总会比拼接URL来得简单)。
  * 后续拓展协议，添加其他的验证请求容易。


#### 请求流程交互图(以第三方App请求QQ安全中心为例，回应同理)
  
  

#### 接入代码过程:
  
  * 1) (以一键验证消息为例):

        AQDualVerifyRequest *request = [AQDualVerifyRequest request]; //创建一键验证请求
        request.uin = ...; //设置请求QQ号
        request.userInfo = ...; //设置第三方App自定义信息
        [AQTokenApi sendRequest:request]; //发送请求

  * 2) 等待QQ安全中心处理结果...
  * 3) 处理来自QQ安全中心的结果应答: 
  
        [AQTokenApi handleOpenURL:url handleBlock:^(AQBaseRequest *request, AQBaseResponse *response) {
            switch (response.errCode) {
                case eToken_InvalidRequest: //无效请求
                case eToken_Success: //成功
                case eToken_TokenErr: //QQ安全中心内部错误
                case eToken_UserCancel: //用户拒绝
            }
        }];  
    

### WebApp和QQ安全中心App的数据交互(URL传递参数)

  WebApp请求QQ安全中心的形式比较简单，以一键验证为例，直接通过URL的形式传递请求数据且拉起QQ安全中心App
  
        openURL: "qmtoken://?src=1&uin=1206616327#qmtoken-2"
        
  关于QQ安全中心回调WebApp的问题:
  由于iOS系统的限制，App调用Web应用，只能打开一个新页面，因此需要Web前端能够带入身份信息参数以及处理身份信息的跳转，目前产品需求不用跳转回浏览器，这里mark一下，后续支持！

  
