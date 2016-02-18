---
layout:     post
title:      "Sublime Text 3(C++、Python)个人配置"
subtitle:   "Sublime Text 3(C++、Python) Config"
description: ""
date:       2016-02-18 11:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- 编程开发
---
分享一下自己的关于Sublime Text 3编写C++和Python代码的一些Package和Setting  
*Preferences User Setting*
{% highlight json %}
{
    "always_show_minimap_viewport": true,
    "caret_style": "phase",
    "draw_minimap_border": true,
    "fade_fold_buttons": false,
    "font_size": 11.0,
    "highlight_line": true,
    "highlight_modified_tabs": true,
    "indent_to_bracket": true,
    "match_brackets_angle": true,
    "show_encoding": true,
    "show_line_endings": true,
    "translate_tabs_to_spaces": true,
    "update_check": false,
    "word_wrap": "auto"
}
{% endhighlight %}  
*Package*
`
Anaconda(非常强大的Python插件，支持自动完成、代码检查、查找变量或函数定义及使用等众多功能)  
Bracket Highlighter(括号匹配高亮)  
ConvertToUTF8(GBK、UTF-8转换)  
ChineseLocalization(中文汉化)  
Console Exec(Python终端运行)  
IMESupport(输入法随行显示)  
SublimeAStyleFormatter(代码格式化)  
`
*Anaconda Setting*
{% highlight json %}
{
    "python_interpreter": "Python安装路径\\python.exe",
    "auto_complete_triggers": [{"selector": "source.python - string - comment - constant.numeric", "characters": "."}],
    "suppress_word_completions": true,
    "suppress_explicit_completions": true,
}
{% endhighlight %}  