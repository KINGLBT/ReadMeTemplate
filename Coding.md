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

@end

