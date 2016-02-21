---
layout:     post
title:      "My first post"
subtitle:   "Hello everyone"
description: ""
date:       2016-02-12 01:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- 生活
---
　　拖了好长时间，终于克服了我的惰性，把这个网站给建成了，忙了一天，学了好多东西，正好下学期开Web课，可以用来练练手。  
　　另外感谢Hux提供的Theme [https://github.com/Huxpro/huxpro.github.io](https://github.com/Huxpro/huxpro.github.io)  
　　第一篇博文该写些什么呢，想不出有什么好写的，随便扯扯吧。大学时光已经过去一半了，到大四估计就得去实习了吧，学了这一年半，好歹把C++、数据结构、算法之类的学的还可以，通过参加ACM-ICPC，刷了一些题，培养了自己的思维能力，希望再接再厉，能取得个不错的成绩。自己会的东西还是太少太少，接下来的这一年半得更加努力学习了，多读书，多造轮子，为将来工作打好基础。最后写几行有意思的代码吧，顺便测试一下语法高亮。（模板对中文的支持貌似不怎么好，有中文的时候代码无法正常高亮，折腾了半天，换了个css）  
　　从标准输入读任意个数，然后输出它们的和，可以这么写：  

{% highlight cpp %}
#include <iostream>
#include <iterator>
#include <numeric>
using namespace std;
int main()
{
    istream_iterator<int> i(cin), e;
    cout << accumulate(i, e, 0);
    return 0;
}
{% endhighlight %}  
 \(ax^2 + bx + c = 0\) 

