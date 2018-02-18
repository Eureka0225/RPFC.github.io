---
layout:     post
title:      "蜜罐技术"
date:     2018-1-17
category: blog
subtitle: 蜜罐技术
author:     "bluebird"
categories:
网络安全
thumb: https://avatars2.githubusercontent.com/u/31133259?s=400&v=4
---
## 前言
> 维基百科:蜜罐（英语：honeypot）是一个电脑术语，专指用来侦测或抵御未经授权操作或者是黑客攻击的陷阱，因原理类似诱捕昆虫的蜜罐因而得名。

随着docker和技术的发展的盛行,蜜罐技术也开始普及起来.如[cowrie](https://github.com/micheloosterhof/cowrie),[蜜网](https://github.com/threatstream/mhn),分布式蜜罐 如[DemonHunter](https://github.com/RevengeComing/DemonHunter) 
蜜罐能捕获到各种样本来进行恶意软件分析,甚至可能捕获到0day攻击.从蜜罐或者用户中获得样本 在安全企业已经是相当普及了.如打野小能手360 
**[360捕获0dayCVE-2017-11826](https://zhuanlan.zhihu.com/p/30023530)**
**[360捕获0dayCVE-2018-0802](https://cert.360.cn/report/detail?id=e21bd0f87635c7261d24871c29f28bae)**

## 蜜罐简介
### 发展
最早的蜜罐工具DTK是在系统未使用端口上运行,对任何想要探测端口的攻击源提供欺骗服务.
随着发展,从模拟整个系统 如honeyd 变为 只模拟漏洞部分 发展出了现在ssh蜜罐等,并加入对样本捕获的功能.
现代蜜罐增加了web程序蜜罐,客户端蜜罐(即浏览器模拟等),智能手机蜜罐来捕获新的攻击类型

### 类型
根据交互程度可以分为低,中,高交互蜜罐 根据使用虚拟软件又可以分为docker,VirtualBox,真实系统蜜罐等(现在流行docker)
* 低交互:模拟服务和漏洞以便收集信息和恶意软件，但是攻击者无法和该系统进行交互
* 中等交互:在一个特有的控制环境中模拟一个生产服务，允许攻击者的部分交互；
* 高交互:攻击者可以几乎自由的访问系统资源直至系统重新清除恢复。
<!-- more -->
## 部署蜜罐
由于详细介绍蜜罐安装篇幅过长并且已经有非常多类似文章,下面放几个链接
https://bbs.ichunqiu.com/forum.php?mod=viewthread&tid=18391
http://www.freebuf.com/sectool/134504.html


## 蜜罐与反蜜罐技术
既然有了蜜罐技术,既然也有反蜜罐技术.包括了网络层面和系统层面

### 对低交互蜜罐的检测
低交互蜜罐不能产生实际操作.常用方法是产生一个网络请求.如`curl xxxxxx`  然后检测服务器请求,如果没有说明你已经进入了一个蜜罐.如果不能使用curl,如mysq蜜罐,可以尝试不存在的命令/不常见的命令,蜜罐与实际系统产生的反应是不一样的

### 对中等交互蜜罐的检测
中等交互蜜罐由于已经部分能和系统交互,我们可以尝试在系统层面进行识别.仅以识别docker为例子
我们可以使用`cat /proc/1/cgroup` 来读取控制组 我们知道docker应用了cgroup技术所以 如果是docker,输出应该存在/docker 如下
~~~
root#: docker run -t -i jupyter/notebook /bin/bash 
root@1e96429c1b62: cat /proc/1/cgroup
11:freezer:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
10:devices:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
9:hugetlb:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
8:cpuset:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
7:blkio:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
6:pids:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
5:cpuacct,cpu:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
4:memory:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
3:perf_event:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
2:net_prio,net_cls:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
1:name=systemd:/docker/1e96429c1b62832d0760f37f4a83e41256e402ef1050e79d9e8c7388a0d6906a
~~~
如果是正常系统,显示应该是
~~~
11:freezer:/
10:devices:/
9:hugetlb:/
8:cpuset:/
7:blkio:/
6:pids:/
5:cpuacct,cpu:/
4:memory:/
3:perf_event:/
2:net_prio,net_cls:/
1:name=systemd:/
~~~

另外也可以尝试不允许的命令如 `reboot`,`rm -rf /` 如果是蜜罐,肯定不能成功操作

### 高交互蜜罐的检测
这个情况下蜜罐已经和正常系统操作无异,你可以为所欲为进行`rm -rf /`和`reboot`等操作.
这个时候主要应用的就是反虚拟机技术或网络检测技术了.反虚拟机技术留给接下来的反docker蜜罐等.我们来讨论网络检测技术.
蜜罐和普通系统在与其他计算机交互上存在差异,我们可以通过读取路由表,arp记录,系统日志等来分析它的网络交互.一个蜜罐通常是与其他计算机没有关联或被屏蔽的(现在发展出了蜜网等技术),所以我们如果读取到异常数据,基本可以认定是蜜罐.举个例子.如果是docker蜜罐你尝试读取arp表 实际是空的
~~~
root@36fcdf9b604f:/notebooks# arp
root@36fcdf9b604f:/notebooks# 
~~~

### docker蜜罐的检测
* 如上 `cat /proc/1/cgroup`
* 提取进程uid 使用`cat /proc/1/sched | head -n 1` 如果是正常系统应该是 `init (1, #threads: 1)` 
虚拟环境则可能是类似于`bash (5276, #threads: 1)`
总结起来可以写成

### VirtualBox蜜罐检测
可以尝试枚举VirtualBox的进程如
* "vmsrvc.exe"
*  "vmusrv.exe"
*   "vmtoolsd.exe"
*    "df5serv.exe"
*   "vboxtray.exe"
*   "vboxservice.exe"

## 蜜罐发展
蜜罐也在随着时代的发展不断扩展 如现在的智能手机蜜罐 但是还是不完善 现在工控设备,智能手机蜜罐依然有很大发展空间



## 参考
[awesome-honeypots](https://github.com/paralax/awesome-honeypots)
[蜜罐技术研究与应用进展 诸葛建伟](http://kns.cnki.net/kns/detail/detail.aspx?QueryID=3&CurRec=1&recid=&FileName=RJXB201304012&DbName=CJFD2013&DbCode=CJFQ&yx=&pr=&URLID=)
[VirtualBoxdetect](https://github.com/mstefanowich/VirtualBoxProcessDetection)
[docker detect](https://stackoverflow.com/questions/20010199/determining-if-a-process-runs-inside-lxc-docker)

