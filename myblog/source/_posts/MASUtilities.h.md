---
title: MAS源码之工具类MASUtilities.h
date: 2018-06-11 17:04:39
tags: iOS
---
# 定义UILayoutPriority属性
```objc
    typedef UILayoutPriority MASLayoutPriority;
    static const MASLayoutPriority MASLayoutPriorityRequired = UILayoutPriorityRequired;
    static const MASLayoutPriority MASLayoutPriorityDefaultHigh = UILayoutPriorityDefaultHigh;
    static const MASLayoutPriority MASLayoutPriorityDefaultMedium = 500;
    static const MASLayoutPriority MASLayoutPriorityDefaultLow = UILayoutPriorityDefaultLow;
    static const MASLayoutPriority MASLayoutPriorityFittingSizeLevel = UILayoutPriorityFittingSizeLevel;
```

## va函数

### 举例：

```c
int SqSum(int n1, ...)
{
va_list arg_ptr;
int nSqSum = 0, n = n1;
va_start(arg_ptr, n1);
while (n > 0)
{
    nSqSum += (n * n);
    n = va_arg(arg_ptr, int);
}
va_end(arg_ptr);
return nSqSum;
}
// 调用时
int nSqSum = SqSum(7, 2, 7, 11, -1);
```

### 函数解析

* va_list
    
    定义一个指向个数可变的参数列表指针；

* va_start(arg_ptr, argN)

    使参数列表指针arg_ptr指向函数参数列表中的第一个可选参数

>  说明：argN是位于第一个可选参数之前的固定参数，（或者说，最后一个固定参数；…之前的一个参数），函数参数列表中参数在内存中的顺序与函数声明时的顺序是一致的。如果有一va函数的声明是void va_test(char a, char b, char c, …)，则它的固定参数依次是a,b,c，最后一个固定参数argN为c，因此就是va_start(arg_ptr, c)。

* va_arg(arg_ptr, type)

    返回参数列表中指针arg_ptr所指的参数，返回类型为type，并使指针arg_ptr指向参数列表中下一个参数。

* va_copy(dest, src)

    dest，src的类型都是va_list，va_copy()用于复制参数列表指针，将dest初始化为src。

* va_end(arg_ptr)
 
    清空参数列表，并置参数指针arg_ptr无效

> 说明：指针arg_ptr被置无效后，可以通过调用va_start()、va_copy()恢复arg_ptr。每次调用va_start() / va_copy()后，必须得有相应的va_end()与之匹配。参数指针可以在参数列表中随意地来回移动，但必须在va_start() … va_end()之内。

### 关键词指令解析

* @encode

    返回一个给定类型编码为一种内部表示的字符串 例如@encode(CGFloat) 可用于判断基础类型的类型相同
    
* inline

```c++

#include <stdio.h>
//函数定义为inline即:内联函数
inline char* dbtest(int a) {
    return (i % 2 > 0) ? "奇" : "偶";
} 
 
int main()
{
   int i = 0;
   for (i=1; i < 100; i++) {
       printf("i:%d    奇偶性:%s /n", i, dbtest(i));    
   }
}

```
在 c/c++ 中，为了解决一些频繁调用的小函数大量消耗栈空间（栈内存）的问题，特别的引入了 inline 修饰符，表示为内联函数。

栈空间就是指放置程序的局部数据（也就是函数内数据）的内存空间。

上面的例子就是标准的内联函数的用法，使用 inline 修饰带来的好处我们表面看不出来，其实，在内部的工作就是在每个 for 循环的内部任何调用 dbtest(i) 的地方都换成了 (i%2>0)?"奇":"偶"，这样就避免了频繁调用函数对栈内存重复开辟所带来的消耗。

> inline 的使用是有所限制的，inline 只适合涵数体内代码简单的涵数使用，不能包含复杂的结构控制语句例如 while、switch，并且不能内联函数本身不能是直接递归函数（即，自己内部还调用自己的函数）。

> inline 函数仅仅是一个对编译器的建议，所以最后能否真正内联，看编译器的意思，它如果认为函数不复杂，能在调用点展开，就会真正内联，并不是说声明了内联就会内联，声明内联只是一个建议而已。

> 建议 inline 函数的定义放在头文件中

> 内联函数并不是一个增强性能的灵丹妙药。只有当函数非常短小的时候它才能得到我们想要的效果；但是，如果函数并不是很短而且在很多地方都被调用的话，那么将会使得可执行体的体积增大。 
最令人烦恼的还是当编译器拒绝内联的时候。在老的实现中，结果很不尽人意，虽然在新的实现中有很大的改善，但是仍然还是不那么完善的。一些编译器能够足够的聪明来指出哪些函数可以内联哪些不能，但是大多数编译器就不那么聪明了，因此这就需要我们的经验来判断。如果内联函数不能增强性能，就避免使用它！
