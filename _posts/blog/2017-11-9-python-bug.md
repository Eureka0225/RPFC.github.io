---
layout:     post
title:      "python函数使用可变参数"
categories:
     - 编程技术    
author:     "bluebird"
category: blog
date:     2017-11-9
description: 写一个python项目时一不小心用了可变参数，发生严重bug，
thumb: https://avatars2.githubusercontent.com/u/31133259?s=400&v=4
---
当时写的代码大概是这样的
```python
class foo:
   def __init__(self,name,arg=[]):
       self.name = name
       self.arg = arg
    
   def make(self):
      # 如果没有下一层就返回名字
      if not self.arg:
         return self.name
      # 否则向下进行
      return '{name}[{next}]'.format(name=self.name,next=''.join([ _.make() for _ in self.arg] ))
start = foo('test',arg=[foo('test2')])
start.make()
```

很正常的构造树代码，不过我却得到了一个遍历深度过大的错误

<!-- more -->
查阅了一些资料后发现是我使用可变对象做默认参数的原因
简单例子
```python
def test(arg=[],app='1'):
    arg.append(app)
    print(arg)
test() # [1]
test() # [1,1]
test() # [1,1,1]
```
在python中函数也是一个对象,而且在我们使用在绝大部分情况下都是创建后不会改变．
然后默认参数其实是函数的一个属性．
每次使用都是调用这个函数对象，如果改变会影响到下个函数对象
```
>>> test.__defaults__
    ([1,1,1],1)
>>>test.__defaults__=([],2)
>>> test()
   [2]
```

所以无意中使用可变对象作为参数可以造成严重bug

当然一个特性不可能只有坏处 如果在类的__init__使用，可以当作一种另类的伪全局对象使用
```python
class test:
     def __init__(self,arg=[]):
        arg.append(1)
        self.arg=arg
```
