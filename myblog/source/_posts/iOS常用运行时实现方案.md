---
title: iOS常用运行时实现
date: 2019-06-19 19:11:44
tags: iOS
---

## iOS常用运行时实现

##### 1.判断子类是不是覆盖了父类的方法

思路：

* 通过class_getInstanceMethod去获取目标方法
* 判断方法是否存在，如果不存在，就没有意义了
* 通过class_getSuperclass获取父类，然后class_getInstanceMethod拿取父类的Method
* 判断父类拿取的这个方法是否存在，如果父类没有,或者父类的实现Method跟子类不一样，那么子类已经覆盖了父类

```objc
inline BOOL isHasOverrideSuperclassMethod(Class targetClass, SEL targetSelector) {
    Method method = class_getInstanceMethod(targetClass,targetSelector);
    if (!method) return NO;
    Method methodOfSuperClass = class_getInstanceMethod(class_getSuperClass(targetClass),targetSelector)
    if (!methodOfSuperClass) return NO;
    return method != methodOfSuperClass;
}
```

##### 2.交换两个类的两个方法实现
思路：

* 分别获取两个类的两个方法，通过class_getInstanceMethod,
* 尝试将第二个类的方法实现添加到第一个类，如果成功了说明第一个类没有实现对应的方法，这时候要加上空的方法实现，避免第二个类去调用的时候crash
* 通过method_exchangeImplementations交换两个方法的实现

```objc
inline BOOL ExchangeImplementationsInTwoClass(Class fromClass,SEL originSelector,Class toClass, SEL newSelector){
    if(!fromClass || !toClass) return NO;
    Method originMethod = class_getInstanceMethod(fromClass,originSelector);
    Method newMethod = class_getInstanceMethod(toClass,newSelector);
    // 如果想交换的新的方法都不在，那么也没意义了
    if (!newMethod) return NO;
    
    BOOL isAddedNewMethod = class_addMethod(fromClass,originSelector,method_getImplementation(newMethod),method_getTypeEncoding(newMethod));
    if (isAddedNewMethod) {
        IMP originIMP = method_getImplementation(originMethod) ?: imp_implementationWithBlock(^(id selfObicet) {});
        const char *originMethonTypeEncoding = method_getTypeEncoding(originMethod) ?: "v@:"
        class_repalceMethod(toClass,newSelector,originIMP,originMethonTypeEncoding)
    } else {
        method_exchangeImplementations(originMethod,newMethod)
    }
}
```

##### 3.交换一个class的两个实现
思路：
* 与交换两个class的逻辑一样，也需要判断方法存在，不存在添加等操作

```objc
inline BOOL ExchangeImplementations(Class class, SEL originSelector, SEL newSelector) {
    // 使用上面的交换两个类的实现的方法
    return ExchangeImplementationsInTwoClass(class,originSelector,class,newSelector)
}
```
》  待更新

