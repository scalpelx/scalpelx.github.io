---
layout:     post
title:      "C++两个类的互相调用"
subtitle:   ""
description: "C++ 类 互相调用"
date:       2016-12-10 01:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- C++
---

今晚WDL在设计两个类互相调用的时候遇到了一些问题，我们查了会资料，在这里记一下解决方案：   
**首先说一下容易犯错的地方：**   
　　两个类不能互相include对方的头文件，两者也不能都是实体对象，必须其中一个为指针。	
因为两个类相互引用，不管哪个类在前面，都会出现有一个类未定义的情况，所以可以提前声明一个类，而类的声明就是提前告诉编译器，所要引用的是个类，但此时后面的那个类还没有定义，因此无法给对象分配确定的内存空间，因此只能使用类指针。如果非得互相引用实体，那应该是错误的设计。   
　　用指针的原因是：假设两个类分别为A和B，在B中用指针调用A，那么在A需要知道B占空间大小的时候，就会去找到B的定义文件，虽然B的定义文件中并没有导入A的头文件，不知道A的占空间大小，但是由于在B中调用A的时候用的指针形式，B只知道指针占4个字节就可以，不需要知道A真正占空间大小，也就是说，A也是知道B的占空间大小的。   
**具体设计方法如下：**   
　　在A的头文件a.h中包含B的头文件b.h，在B的头文件中声明`class A;`，并且在b.cpp中包含A的头文件，以下是具体的代码：

~~~cpp
//a.h

#ifndef A_H
#define A_H

#include "b.h" //包含B的头文件

class A {
    B b;
};

#endif
~~~

~~~cpp
//a.cpp

#include "a.h"

A::A() {}
~~~

~~~cpp
//b.h

#ifndef B_H
#define B_H

class A; //前置声明

class B {
    A *a; //类指针
};

#endif
~~~

~~~cpp
//b.cpp

#include "b.h"
#include "a.h" // 包含A的头文件

B::B() {}
~~~

~~~cpp
//main.cpp

#include "a.h"
#include "b.h"

int main()
{
    A a;
    B b;
    return 0;
}
~~~