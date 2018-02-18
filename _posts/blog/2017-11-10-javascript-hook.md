---
layout:     post
title:     "javascript hook 技术"
category: blog
author:     "bluebird"
date:     2017-11-10
description: javascript hook 技术
categories:
    - 网络安全
thumb: https://avatars2.githubusercontent.com/u/31133259?s=400&v=4
---
###javascript hook 利用

#### 简述
hook的中文翻译是钩子，hook了xxx在网络安全的用语中是指在调用xxx的时候转为调用我们定义的过程

#### 最简单的javascript hook
javascript hook非常简单。因为在javascript中函数也是变量，可以赋值的。 只需要把另一个函数赋值给要hook的函数即可，如
~~~
alert = eval
~~~
我们就成功将alert函数hook，让它去执行我们的eval函数

#### javascript hook利用场景
常用于前端防御，开发调试，javascript加解密等等

比如以下这段加密代码
```
eval(function(p,a,c,k,e,r){e=String;if(!''.replace(/^/,String)){while(c--)r[c]=k[c]||c;k=[function(e){return r[e]}];e=function(){return'\\w+'};c=1};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p}('0(1)',2,2,'alert|'.split('|'),0,{}))
```
<!-- more -->
我们可以hook eval函数使得将执行改为alert
```
eval = alert
```

这样就相当于不管如何字符串加密解密 最后都会将要执行的代码弹窗


前端防御使用更有意思
我们测试xss的时候都挺喜欢alert函数，如果前端不怀好意的加了一句
```
alert = null
```
当我们以为没有xss的时候 其实只是alert被删除了 白白错过了一个xss漏洞

#### 表哥们造过的轮子
简单粗暴的替换 可能在生产环境中不太好用 会影响其他代码 这时我们就需要保存原有函数
当然表哥都造过了轮子，也懒得写了
[hookjs](https://github.com/pnigos/hookjs) 代码很少很简单 建议阅读
[javascirpt-hooker](https://github.com/cowboy/javascript-hooker) 这个支持nodejs 

#### hook攻防简述
如果前端防御使用了hook技术 自然要破解hook防御
hook攻防往往是hook方布下防线 反hook者则需要破解hook方的重重封锁

防御方可以将不会用到的函数统统删除，或者做警报（hook 进行ajax提交） 结合waf可以造成很高的利用难度

防御方主要要面临的问题就是不能影响生产代码，往往是要保存原有函数，
攻击方查看hook代码 将原有函数直接调用 防御均变为无用功



