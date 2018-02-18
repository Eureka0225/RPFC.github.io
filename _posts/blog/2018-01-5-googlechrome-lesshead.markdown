---
layout:     post
title:      "使用谷歌浏览器的无头模式模拟访问"
date:     2018-01-05
category: blog
subtitle:  使用谷歌浏览器的无头模式模拟访问
author:     "bluebird"
categories:
    - 网络安全
thumb: https://avatars2.githubusercontent.com/u/31133259?s=400&v=4
---
### 如何开始
如果你是mac和linux需要版本59以上,window则需要60以上.然后运行
```bash
google-chrome \
  --headless \                   # 运行chrome在无头模式
  --disable-gpu \                #　如果你是window
  --remote-debugging-port=9222   # 调试端口
```

如果没有报错的话 应该会显示
```bash
DevTools listening on ws://127.0.0.1:9222/devtools/browser/a788c70f-e72b-46c4-891a-6acf489ebd9d

```
<!-- more -->
ws:是谷歌的 DevTools协议 可以通过网络通信控制.但是在命令行选项中已经内置了一些常用选项
```
 --dump-dom   #  打印document.body.innerHTML  |
   --print-to-pdf #  创建页面的PDF  
 --screenshot   # 屏幕截图    
   --repl #  可以在命令行执行js代码  
```

你也可以通过访问`http://localhost:9222` 来查看是否运行正常.正常情况下是
```
Inspectable WebContents
about:blank
```

### 通过编程使用
虽然官方推荐使用nodejs,但是作为一名pythoner加上电脑没有nodejs,还是选择了使用python.
在github找到了2个别人写好的轮子 [pychrome](https://github.com/fate0/pychrome) [pyppeteer](https://github.com/miyakogi/pyppeteer)
查看了一下作者更新日期 选择了pychrome.
直接使用`pip install  git+https://github.com/fate0/pychrome.git`
尝试官方例子
```python
import pychrome

# create a browser instance
browser = pychrome.Browser(url="http://127.0.0.1:9222")

# create a tab
tab = browser.new_tab()

# register callback if you want
def request_will_be_sent(**kwargs):
    print("loading: %s" % kwargs.get('request').get('url'))

tab.Network.requestWillBeSent = request_will_be_sent

# start the tab 
tab.start()

# call method
tab.Network.enable()
# call method with timeout
tab.Page.navigate(url="https://github.com/fate0/pychrome", _timeout=5)

# wait for loading
tab.wait(5)

# stop the tab (stop handle events and stop recv message from chrome)
tab.stop()

# close tab
browser.close_tab(tab)
```
如果不报错 输出应该是这样的
```
loading: https://github.com/fate0/pychrome
loading: https://assets-cdn.github.com/assets/frameworks-f27d807afb610bf126cbfb9ce429438a328e012239e5a77fc8152b794553dfc0.css
loading: https://assets-cdn.github.com/assets/github-90814c795d836404981b54444a6ccc23fac2c6b0e1ce875e90784fbd83445bc5.css
loading: https://assets-cdn.github.com/assets/site-784ac435bab892893613aebf8fc79351510fb731a659406e0a2930f7643de45f.css
loading: https://avatars3.githubusercontent.com/u/6829628?s=40&v=4
```

光是官方例子就可以写一个简单的检测网页请求来进行下一步扫描了.
谷歌的官方[api文档](https://chromedevtools.github.io/devtools-protocol/1-2/)
使用pychrome只需要使用tab.call_method('方法名',参数字典).
例如https://chromedevtools.github.io/devtools-protocol/1-2/Page/ 中的 Page.enable
```python
tab.call_method('Page.enable')
```

如果有参数,如Page.reload,则传入参数字典如
```python
tab.call_method('Page.reload',scriptToEvaluateOnLoad
='alert(1)')
```

