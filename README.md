### 关于目前iOS-QQ安全中心代码的一些弊端

  目前iOS-QQ安全中心代码经过了多次的版本协议调整，几位开发人员的参与，从开头的QQ手机令牌到现在的版本，代码基本没有过重构，
老的一些模块逻辑不清晰，代码重复率高，程序性能不好且不稳定。具体表现为：

  * 1) 网络模块性能
    
    目前网络的处理采用单一子线程同步阻塞处理模式，见下图：

    
    这个模型的最大缺点在于线性的叠加了所有网络包的等待收包时间，假如同一时间发送4个请求，那么第4个请求的时间基本就是总4个
时间的线性叠加，更糟糕的是，假如前面的任务1，任务2的超时，任务3，任务4也会跟着等待。v2.1版本开始暂时对这部分模块在现有代码
的基础上做了一些优化，部分请求再额外单独开启子线程来处理，但是还是基于同步阻塞模式。

  * 2) 消息数据存储(v2.2版本已重构完成)
    
    下面是各个版本的消息数据库结构和数据迁移
    
    
    
        //12-8-1增加字段(detail TEXT, site TEXT, ip TEXT, money INTEGER)
        //12-9－6 site,money,detail删除
        //12-8-17增加字段(countryId INTEGER, provId INTEGER, cityId INTEGER)
        //12-8-20增加字段(typeC INTEGER)
        //12-9－6增加字段(msgTable TEXT)
        //13-3－5增加字段(actionBtnText TEXT, feedbackType TEXT, feedbackTarget TEXT, feedbackBtnText TEXT, textBefore TEXT, textAfter TEXT)
        [self createDBTable:kLoginMsgTableName];// 创建新表
        [self copyAndDropTable:kOldLoginMsgTableName toTable:kLoginMsgTableName];// 拷贝老表
        [self copyAndDropTable2:kOldLoginMsgTableName2 toTable:kLoginMsgTableName];// 拷贝老表

    v2.2版本关于数据存储的处理
    
    
    
    v2.2版本为了避免后续协议字段的添加和修改，把服务器下发的json字符串作为一整个信息存储下来，读取的时候再实时解析。
    这么做有两个好处：
      * a) 处理版本兼容变得很简单；
      * b) 协议数据不会因为数据库的原因丢失，旧版本升级上来，还可以读取因为旧版本无法处理的协议内容；

  * 3) 底层协议模块不清晰，逻辑交互复杂，上层代码重复率高

    从开头的QQ手机令牌，经过几次的产品需求调整，模块之间划分越来越多，模块之间交叉的地方也越来越多，重复代码率相当高，
以获取绑定QQ号为例，假如软件一启动需要拉取绑定的QQ号，这存在几种情况，当前在哪个界面啊，当前绑定没有啊，当前初始化种子
没有啊，之类的，然后就会出现下面的交互图：

    
    










