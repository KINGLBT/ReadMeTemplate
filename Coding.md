### 声明分类方法
 
    /** Make all categories in the current file loadable without using -load-all.
    *
    * Normally, compilers will skip linking files that contain only categories.
    * Adding a call to this macro adds a dummy class, which causes the linker
    * to add the file.
    *
    * @param UNIQUE_NAME A globally unique name.
    */
    #define MAKE_CATEGORIES_LOADABLE(UNIQUE_NAME) @interface FORCELOAD_##UNIQUE_NAME @end @implementation FORCELOAD_##UNIQUE_NAME @end
     
    MAKE_CATEGORIES_LOADABLE(SomeConcreteClass_MyAdditions);@implementation SomeConcreteClass (MyAdditions)...@end

### Implement the description Method

    ...
    // Description method for EOCPerson
    - (NSString*)description {
        return [NSString stringWithFormat:@"<%@: %p, \"%@ %@\">", [self class], self, _firstName, _lastName];
    }
    ...

### Understand Message Forwarding

  必要时我们可以通过实现`+ (BOOL)resolveClassMethod:(SEL)sel`和`+ (BOOL)resolveInstanceMethod:(SEL)sel`方法来动态的为选择器（selector）提供对应的实现（implementation）。
    
    + (BOOL)resolveInstanceMethod:(SEL)selector {
    NSString *selectorString = NSStringFromSelector(selector);
    if (/* selector is from a @dynamic property */) {
        if ([selectorString hasPrefix:@"set"]) {
            class_addMethod(self, selector, (IMP)autoDictionarySetter, "v@:@");
        } else {
            class_addMethod(self, selector, (IMP)autoDictionaryGetter, "@@:");
        }
        return YES;
    }
    return [super resolveInstanceMethod:selector];
    }

### Consider Method Swizzling to Debug Opaque Methods

  我们可以通过runtime提供的`method_exchangeImplementations`方法来交换两个方法的实现。
  
    // Exchanging methods
    Method originalMethod = class_getInstanceMethod([NSString class], @selector(lowercaseString));
    Method swappedMethod = class_getInstanceMethod([NSString class], @selector(uppercaseString));
    method_exchangeImplementations(originalMethod, swappedMethod);
    
### Avoid Properties in Categories
  
  Objective-C分类中是不允许增加成员变量的（Instance variables may not be placed in categories），我们可以通过运行时函数`objc_setAssociatedObject` 和 `objc_getAssociatedObject` 来让分类支持保存和获取一些数据，从而支持属性。
  
    //EOCPerson+FriendShip.h
    @interface EOCPerson (FriendShip)
    @property (nonatomic, strong) NSArray *friends;
    @end

    //EOCPerson+FriendShip.m
    static const char* kFriendsPropertyKey = "kFriendsPropertyKey";
    @implementation EOCPerson (Friendship)
    - (NSArray*)friends {
        return objc_getAssociatedObject(self, kFriendsPropertyKey);
    }
    - (void)setFriends:(NSArray*)friends {
        objc_setAssociatedObject(self, kFriendsPropertyKey, friends, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    }
    @end



@end


### 查看sdk版本信息

    xcodebuild -showsdks




  
    typedef enum {
       UILineBreakModeWordWrap = 0,        以单词为单位换行，以单位为单位截断。
       UILineBreakModeCharacterWrap,       以字符为单位换行，以字符为单位截断。
       UILineBreakModeClip,                        以单词为单位换行。以字符为单位截断。
       UILineBreakModeHeadTruncation,     以单词为单位换行。如果是单行，则开始部分有省略号。如果是多行，则中间有省略号，省略号后面有4个字符。
       UILineBreakModeTailTruncation,         以单词为单位换行。无论是单行还是多行，都是末尾有省略号。
       UILineBreakModeMiddleTruncation,    以单词为单位换行。无论是单行还是多行，都是中间有省略号，省略号后面只有2个字符 
    } UILineBreakMode;

    
    @property(nonatomic)         CGFloat                    estimatedRowHeight ;// default is 0, which means there is no estimate
    @property(nonatomic)         CGFloat                    estimatedSectionHeaderHeight ;// default is 0, which means there is no estimate
    @property(nonatomic)         CGFloat                    estimatedSectionFooterHeight ;// default is 0, 
    // Use the estimatedHeigh(估算高度)t methods to quickly calcuate guessed values which will allow for fast load times of the table.
    // If these methods are implemented, the above -tableView:heightForXXX calls will be deferred until views are ready to be displayed, so more expensive logic can be placed there.
    - (CGFloat)tableView:(UITableView *)tableView estimatedHeightForRowAtIndexPath:(NSIndexPath *)indexPath
    // the background color of the section index while not being touched（当section不被触摸时候的背景颜色）
    @property(nonatomic,retain)UIColor *sectionIndexBackgroundColor; 

    
    NSDictionary* attrs = @{ NSForegroundColorAttributeName: [UIColorblackColor], NSFontAttributeName: [UIFont fontWithName:@"AmericanTypewriter"size:0.0] };
    [[UINavigationBar appearance] setTitleTextAttributes:attrs];
    [[UINavigationBar appearance] setTintColor:[UIColor redColor]];
    
    
    [progress setProgressImage:[UIImage imageNamed:@"bg.png"]];
    [progress setProgressViewStyle:UIProgressViewStyleBar];
    [progress setTrackImage:[UIImage imageNamed:@"sss.png"]];

    
    [searchBar setBarStyle:UIBarStyleDefault];
    [searchBar setBarTintColor:[UIColor redColor]];
    [searchBar setBackgroundImage:[UIImage imageNamed:@"bg.png"] forBarPosition:UIBarPositionAny barMetrics:UIBarMetricsDefault];
    searchBar.showsCancelButton =YES;
    
    
    - (UIBarPosition)positionForBar:(id<UIBarPositioning>)bar
    - (void)setShadowImage:(UIImage *)shadowImage forToolbarPosition:(UIBarPosition)topOrBottom NS_AVAILABLE_IOS(6_0) UI_APPEARANCE_SELECTOR;
    - (UIImage *)shadowImageForToolbarPosition:(UIBarPosition)topOrBottom NS_AVAILABLE_IOS(6_0) UI_APPEARANCE_SELECTOR;
    
    
    - (void)setAttributedTitle:(NSAttributedString *)title forState:(UIControlState)stateNS_AVAILABLE_IOS(6_0);// default is nil. title is assumed to be single line
    
    
    
    

 





@end
