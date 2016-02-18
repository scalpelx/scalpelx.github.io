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
```
Anaconda(非常强大的Python插件，支持自动完成、代码检查、查找变量或函数定义及使用等众多功能)  
Bracket Highlighter(括号匹配高亮)  
ConvertToUTF8(GBK、UTF-8转换)  
ChineseLocalization(中文汉化)  
Console Exec(Python终端运行)  
IMESupport(输入法随行显示)  
SublimeAStyleFormatter(代码格式化)  
```

*Anaconda Setting*
{% highlight json %}
{
    "python_interpreter": "Python安装路径\\python.exe",
    "auto_complete_triggers": [{"selector": "source.python - string - comment - constant.numeric", "characters": "."}],
    "suppress_word_completions": true,
    "suppress_explicit_completions": true,
}
{% endhighlight %}  
*Bracket Highlighter Setting*
{% highlight json %}
{
    "bracket_styles": {
        "default": {
            "icon": "dot",
            "color": "entity.name.class",
            "color": "brackethighlighter.default",
            "style": "highlight"
        },
        "unmatched": {
            "icon": "question",
            "color": "brackethighlighter.unmatched",
            "style": "highlight"
        },
        "curly": {
            "icon": "curly_bracket",
            "color": "brackethighlighter.curly",
            "style": "underline"
        },
        "round": {
            "icon": "round_bracket",
            "color": "brackethighlighter.round",
            "style": "underline"
        },
        "square": {
            "icon": "square_bracket",
            "color": "brackethighlighter.square",
            "style": "underline"
        },
        "angle": {
            "icon": "angle_bracket",
            "color": "brackethighlighter.angle",
            "style": "underline"
        },
        "tag": {
            "icon": "tag",
            "color": "brackethighlighter.tag",
            "style": "highlight"
        },
        "single_quote": {
            "icon": "single_quote",
            "color": "brackethighlighter.quote",
            "style": "underline"
        },
        "double_quote": {
            "icon": "double_quote",
            "color": "brackethighlighter.quote",
            "style": "underline"
        },
        "regex": {
            "icon": "regex",
            "color": "brackethighlighter.quote",
            "style": "outline"
        }
    },
}
{% endhighlight %}  
*C++ Build System*
Windows(可自由更改g++选项，可调出命令行，解决了默认不支持输入的问题):  
{% highlight json %}
{
    "encoding": "utf-8",
    "working_dir": "$file_path",
    "shell_cmd": "g++ -std=c++14 -Wall -Wextra \"$file_name\" -o \"$file_base_name\"",
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "selector": "source.c++",
    "variants": 
    [
        {   
        "name": "Run",
            "shell_cmd": "start cmd /c \"${file_path}/${file_base_name} & pause\""
        }
    ]
}
Linux(同上):  
{% endhighlight %}