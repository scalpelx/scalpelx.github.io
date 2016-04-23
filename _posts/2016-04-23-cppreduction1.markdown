---
layout:     post
title:      "C++类型推断（一）模版类型推断"
subtitle:   "C++ Template Type Reduction"
description: "C++ Template Type Reduction"
date:       2016-04-23 09:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- C++
- 读书笔记
- Effective Modern C++

---
模版是C++非常重要的组成部分，是泛型编程的基础，当我们定义一个函数模版时，经常会遇到推测其类型的情况，本文分成三种情况来讨论。模版的定义形式如下：  

~~~cpp
template<typename T>
void f(ParamType param);

f(expr); // 调用f
~~~

编译器会根据expr的类型来推断ParamType和T的类型，这两者通常是不同的，ParamType会有一些修饰符，比如const、reference等。
##首先介绍第一种也是最简单的一种情况，ParamType既不是指针也不是引用：

~~~cpp
template<typename T>
void f(T param); 
~~~

param将通过值的拷贝来传递，类型推断将按照以下两种规则：  
1、如果调用f时，expr的类型是引用，则忽略引用；  
2、然后如果还是const，则忽略，如果是volatile，同样忽略。  

~~~cpp
int x = 27; 
const int cx = x; 
const int& rx = x; 

f(x); // T's and param's types are both int
f(cx); // T's and param's types are again both int
f(rx); // T's and param's types are still both int
~~~

注意：只有传递的方式是by-value时，才可以忽略const和volatile。  

##第二种ParamType是指针或者引用，但不是Universal  Reference：
*这里先说一下什么是Universal Reference，C++11新增了右值引用，但不是所有的诸如Type&&这样形式的都是右值引用，当发生类型推断时，有时意味着rvalue reference，有时意味着lvalue reference，对于这种非常灵活的引用，Scott Meyers把它称之为Universal Reference，我也比较喜欢这种叫法。这种情况仅仅发生在type reduction中，最终类型取决于初始化的值类型。*  
回归正传，当expr是引用时，忽略引用，然后通过expr的类型与ParamType进行模式匹配，从而推断T。  

~~~cpp
template<typename T>
void f(T& param);  // param is a reference

int x = 27;        // x is an int
const int cx = x;  // cx is a const int
const int& rx = x; // rx is a reference to x as a const int

f(x);  // T is int, param's type is int&
f(cx); // T is const int, param's type is const int&
f(rx); // T is const int, param's type is const int&
~~~

右值引用也是如此，当param的类型是reference-to-const时，稍微有些变化,在T的类型中没有必要再有const：  

~~~cpp
template<typename T>
void f(const T& param); // param is now a ref-to-const

int x = 27; // as before
const int cx = x; // as before
const int& rx = x; // as before

f(x); // T is int, param's type is const int&
f(cx); // T is int, param's type is const int&
f(rx); // T is int, param's type is const int&
~~~

当param的类型是指针时，规则也是如此：  

~~~cpp
template<typename T>
void f(T* param); // param is now a pointer

int x = 27; // as before
const int *px = &x; // px is a ptr to x as a const int

f(&x); // T is int, param's type is int*
f(px); // T is const int, param's type is const int*
~~~

##第三种ParamType是Universal Reference

如果expr是一个左值，则T和ParamType都被推断为左值引用（注意，这是把T推断为引用的唯一一种情况），如果expr是一个右值，可以参照上一个规则。  

~~~cpp
template<typename T>
void f(T&& param); // param is now a universal reference

int x = 27; 
const int cx = x; 
const int& rx = x; 

f(x);  // x is lvalue, so T is int&, param's type is also int&
f(cx); // cx is lvalue, so T is const int&, param's type is also const int&
f(rx); // rx is lvalue, so T is const int&, param's type is also const int&
f(27); // 27 is rvalue, so T is int, param's type is therefore int&&
~~~

另外当数组作为参数通过值传递的时候会退化为指针，我们可以通过引用的方式来传递，避免这种退化。函数也是如此。
