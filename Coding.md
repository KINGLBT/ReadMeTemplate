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

### Keep initialize and load Implementations Lean

  `+ (void)load;`
  Invoked whenever a class or category is added to the Objective-C runtime; implement this method to perform class-specific behavior upon loading.
  `+ (void)initialize;`
  Initializes the receiver before it’s used (before it receives its first message).
  
### Use Toll-Free Bridging for Collections with Custom Memory-Management Semantics

    // No-ops for non-retaining objects.
    static const void* EOCRetainNoOp(CFAllocatorRef allocator, const void *value) { return value; }
    static void EOCReleaseNoOp(CFAllocatorRef allocator, const void *value) { }
    
    NSMutableArray* EOCNonRetainArray(){
        CFArrayCallBacks callbacks = kCFTypeArrayCallBacks;
        callbacks.retain = EOCRetainNoOp;
        callbacks.release = EOCReleaseNoOp;
        return (NSMutableArray *)CFArrayCreateMutable(nil, 0, &callbacks);
    }
    
    
    NSMutableDictionary* EOCNonRetainDictionary(){
        CFDictionaryKeyCallBacks keyCallbacks = kCFTypeDictionaryKeyCallBacks;
        CFDictionaryValueCallBacks callbacks = kCFTypeDictionaryValueCallBacks;
        callbacks.retain = EOCRetainNoOp;
        callbacks.release = EOCReleaseNoOp;
        return (NSMutableDictionary *)CFDictionaryCreateMutable(nil, 0, &keyCallbacks, &callbacks);
    }

### Prefer Block Enumeration to for Loops

    // Block enumeration
    NSArray *anArray = /* … */;
    [anArray enumerateObjectsUsingBlock:^(id object, NSUInteger idx, BOOL *stop){
        // Do something with `object’
        if (shouldStop) {
            *stop = YES;
        }
    }];
    
    NSDictionary *aDictionary = /* … */;
    [aDictionary enumerateKeysAndObjectsUsingBlock:^(id key, id object, NSUInteger idx, BOOL *stop){
        // Do something with `key’ and `object’
        if (shouldStop) {
            *stop = YES;
        }
    }];
    
    NSSet *aSet = /* … */;
    [aSet enumerateObjectsUsingBlock:^(id object, NSUInteger idx, BOOL *stop){
        // Do something with `object’
        if (shouldStop) {
            *stop = YES;
        }
    }];
    

@end
