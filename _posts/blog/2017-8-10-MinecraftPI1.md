---
layout: post
title: "探索Minecraft Pi的虚拟世界"
date: 2017-8-10
author: GloomyGhost
categories:
    - blog
    - RaspberryPi
permalink: /blog/2017/MinecraftPI1/
thumb: http://ouav818sk.bkt.clouddn.com/mcpi-start.png
---


> 探索Minecraft Pi的虚拟世界

## 1：介绍

### 你会做什么

您将探索Minecraft Pi的虚拟世界，这是Minecraft为Raspberry Pi制作的特别版本。

您将学习如何控制游戏，手动构建方块，并使用Python界面来操纵您周围的世界。

### 你会学到什么

<!-- more -->

通过使用您的Raspberry Pi，您将学习以下内容：

+ 如何访问Minecraft Pi并创建一个新的世界
+ 如何使用Python编程环境IDLE连接到Minecraft Pi
+ 如何使用Minecraft Python API将文本发布到聊天窗口，查找坐标，传送和构建结构
+ 如何使用变量来存储不同类型方块的ID
+ 尝试放置具有特殊属性的不同类型的方块
+ 使用基本的编程结构来创建简单的程序


## 2.你会需要什么

### 软件

软件安装:

默认情况下，Minecraft已于2014年9月起在Raspbian中安装。

![Minecraft Pi桌面图标](http://ouav818sk.bkt.clouddn.com/minecraft-pi-shortcut.png)

如果您使用的是旧版本的Raspbian，请打开一个终端窗口并输入以下命令（您必须有网络连接）：
```batch
sudo apt-get update
sudo apt-get install minecraft-pi
```

一旦完成，就会安装Minecraft Pi和Python库。

### 测试Minecraft

要运行Minecraftpi，双击桌面图标或在终端输入```minecraft-pi```

![menu.png](http://ouav818sk.bkt.clouddn.com/menu.png)

当Minecraft Pi加载时，单击StartGame，然后单击New。你会注意到包含的窗口稍微偏移。这意味着拖动窗口，你必须抓住Minecraft窗口后面的标题栏。


![你现在正在玩Minecraft！](http://ouav818sk.bkt.clouddn.com/mcpi-start.png)

### 测试Python

当游戏运行时，按下Tab钥匙，释放你的鼠标。在桌面上打开IDLE（不是IDLE3），并移动窗口，使其并排。

您可以直接在Python窗口中键入命令或创建一个文件，以便您可以保存代码并再次运行它。

如果你想创建一个文件，去File > New window，File > Save。您可以将其保存在主文件夹或新项目文件夹中。

从导入Minecraft库开始，创建与游戏的连接，并通过将消息“Hello world”发送到屏幕进行测试：

```python
from mcpi import minecraft
mc = minecraft.Minecraft.create()
mc.postToChat("Hello world")
```

如果您直接输入命令到Python窗口，在每行后面时Enter。如果它是一个文件，保存：Ctrl + S并运行：F5。当您的代码运行时，您应该在游戏中的屏幕上看到您的消息。

![Hello World](http://ouav818sk.bkt.clouddn.com/mcpi-game.png)

如果您在Minecraft窗口中看到“Hello world”，那么您可以继续阅读该文章。


## Minecraft Pi入门

Minecraft是一个受欢迎的沙盒开放世界的建筑游戏。免费版的Minecraft适用于Raspberry Pi; 它还附带一个编程接口。这意味着您可以在Python代码中编写命令和脚本来自动构建游戏中的东西。这是学习Python的好 法！

![Minecraft Pi横幅](http://ouav818sk.bkt.clouddn.com/minecraft-pi-banner.png)

## 运行Minecraft

要运行Minecraft Pi，请从桌面菜单打开或打开终端输入`minecraft-pi`

![menu.png](http://ouav818sk.bkt.clouddn.com/menu.png)

当Minecraft Pi加载时，单击StartGame，然后单击New。你会注意到包含的窗口稍微偏移。这意味着拖动窗口，你必须抓住Minecraft窗口后面的标题栏。

![mcpi-game.png](http://ouav818sk.bkt.clouddn.com/mcpi-game.png)

你现在正在玩Minecraft！去走走，劈东西，撸树致富！

使用鼠标环顾四周，并使用键盘上的以下按键：

键位	       命令<br>
W: 	       前进<br>
A: 	       左<br>
S: 	       后退<br>
D: 	       右<br>
E: 	       物品栏<br>
Space: 	   跳<br>
双击Space: 飞 / 掉下来<br>
Esc: 	  暂停 / 游戏主菜单<br>


您可以使用鼠标的滚轮从物品栏中选择一个方块（或使用键盘上的数字），或者E从库存中选择一些东西。

![mcpi-inventory.png](http://ouav818sk.bkt.clouddn.com/mcpi-inventory.png)

你也可以双击空格键进入空中。当你释放空格键时，你会停止飞行，如果你再次点击它，你会回到地面。

![mcpi-flying.png](http://ouav818sk.bkt.clouddn.com/mcpi-flying.png)

用手中的剑，你可以点击你面前的方块去除它们（亦挖掘）。手中有一个方块，您可以使用右键单击将该   方块放在您的前面，或者单击左键以删除一个方块。

## 使用Python编程

当游戏运行时，按下Tab钥匙，释放你的鼠标。从应用程序菜单中打开Python 3并移动窗口，使它们并排。

您可以直接在Python窗口中键入命令或创建一个文件，以便您可以保存代码并再次运行它。

如果你想创建一个文件，去File > New window，File > Save。您可以将其保存在主文件夹或新项目文件夹中。

从导入Minecraft库开始，创建与游戏的连接，并通过将消息“Hello world”发送到屏幕进行测试：
```python
from mcpi.minecraft  import  Minecraft
mc = Minecraft.create（）
mc.postToChat（“ Hello world ”）
```
如果您直接输入命令到Python窗口，在每行后面时Enter。如果它是一个文件，保存：Ctrl + S并运行：F5。当您的代码运行时，您应该在游戏中的屏幕上看到您的消息。

![helloworld.gif](http://ouav818sk.bkt.clouddn.com/helloworld.gif)

### 找到您的位置

要查找您的位置，请输入：

```pos = mc.player.getPos()```

pos现在包含你的位置; 访问坐标系的每一部分pos.x，pos.y和pos.z。

或者，将坐标设置为单独变量的好方法是使用Python的拆包技术：

```x, y, z = mc.player.getPos()```

现在x，y并且z包含你的位置坐标的每个部分。x并且z是步行方向（前进/后退和左/右），并且y是向上/向下。

请注意，getPos()返回当时玩家的位置，如果您移动位置，您必须再次调用该功能或​​使用存储的位置。

### 瞬移

除了找出您当前的位置，您可以指定要传送到的特定位置。
```python
x, y, z = mc.player.getPos()
mc.player.setPos(x, y+100, z)
```
这将把您的玩家运送到空中的100个空间。这意味着你会传送到天空的中间，直接回到你开始的地方。

尝试传送到其他地方！

### 设置方块

您可以在给定的一组坐标位置放置单个   方块,使用mc.setBlock()：
```python
x, y, z = mc.player.getPos()
mc.setBlock(x+1, y, z, 1)
```
现在一块方块石方块应该出现在你站立的旁边。如果它不是立即在你面前，它可能在你身后。返回到Minecraft窗口，并使用鼠标旋转，直到您在前面看到一个灰色方块。

![mcpi-setblock.png](http://ouav818sk.bkt.clouddn.com/mcpi-setblock.png)

函数set block是x，y，z与id。(x, y, z)是指在游戏上位置（我们指定一个方块，位置是离玩家x+ 1的地方），id指的是我们希望把方块的类型。1是石头

你可以试试的其他方块：

空气:   0
草地:   2
泥土:   3

试着把它改成别的：

```mc.setBlock(x+1, y, z, 2)```

你应该看到你眼前的灰石方块改变了！

![mcpi-setblock2.png](http://ouav818sk.bkt.clouddn.com/mcpi-setblock2.png)

您可以使用内置的方块常量来设置方块，如果您知道它们的名称。你会需要另import一行。

```from mcpi import block```

现在你可以写下面的一个方块：

```mc.setBlock(x+3, y, z, block.STONE.id)```

方块ID很容易猜到，只需使用所有CAPS，但这里有几个例子让您习惯了他们的命名方式。

WOOD_PLANKS

WATER_STATIONARY

GOLD_ORE

GOLD_BLOCK

DIAMOND_BLOCK

NETHER_REACTOR_CORE

###  方块作为变量

如果你知道一个   方块的id，将它设置为一个变量是有用的。您可以使用名称或整数ID。

```
dirt = 3
mc.setBlock(x, y, z, dirt)
```
或者

```
dirt = block.DIRT.id
mc.setBlock(x, y, z, dirt)
```

### 特别的方块

有一些具有额外属性的方块，例如可以指定颜色的额外设置的羊毛。要设置此使用可选的第四个参数setBlock：

```
wool = 35
mc.setBlock(x, y, z, wool, 1)
```

这里第四个参数1将羊毛颜色设置为橙色。没有第四个参数，它被设置为default（0），它是白色的。更多的颜色是：

2: 品红</br>
3: 亮蓝</br>
4: 骚黄

尝试一些更多的数字，看看方块改变！

具有额外特性的方块是木材（17）：橡木，云杉，桦木等; 高草（31）：灌木，草，蕨; 火炬（50）：指向东，西，北，南; 等等。有关详细信息，请参阅[API参考](http://www.stuffaboutcode.com/p/minecraft-api-reference.html)。

### 设置多个方块

除了设置单个方块，setBlock您可以一次性填充一个空间setBlocks：

```python
stone = 1
x, y, z = mc.player.getPos()
mc.setBlocks(x+1, y+1, z+1, x+11, y+11, z+11, stone)
```

这将填充一个10 x 10 x 10立 体的实心石头。

![mcpi-setblocks.png](http://ouav818sk.bkt.clouddn.com/mcpi-setblocks.png)

您可以使用该setBlocks功能创建更大的，但可能需要更长时间才能生成！

## 当你走路时掉落的方块

现在你知道如何放放置方块，当我们走路时，让我们使用移动的位置来放置方块。

以下代码将在您身后随身携带一朵花：

```python
from mcpi.minecraft import Minecraft
from time import sleep

mc = Minecraft.create()

flower = 38

while True:
    x, y, z = mc.player.getPos()
    mc.setBlock(x, y, z, flower)
    sleep(0.1)
```

现在向前走一会儿，转过身去看看你留下的鲜花。

![mcpi-flowers.png](http://ouav818sk.bkt.clouddn.com/mcpi-flowers.png)

由于我们使用while True循环，这将永远持续下去。要停止，关闭python脚本。

尝试飞过空中，看看你在天空中离开的花朵：

![mcpi-flowers-sky.png](http://ouav818sk.bkt.clouddn.com/mcpi-flowers-sky.png)

如果玩家在草地上散步时只想放花，该怎么办？我们可以getBlock用来找出一个方块的类型：

```python
x, y, z = mc.player.getPos()  # player position (x, y, z)
this_block = mc.getBlock(x, y, z)  # block ID
print(this_block)
```

这告诉你，你站在方块的位置在（这将是0-空气方块）。我们想知道，我们是站在什么类型的方块上。为此，我们从y值中减去1，并用于getBlock()确定我们所在的方块的类型：

```python
x, y, z = mc.player.getPos()  # player position (x, y, z)
block_beneath = mc.getBlock(x, y-1, z)  # block ID
print(block_beneath)
```

这告诉我们玩家所在的区域的方块的ID。

通过运行一个循环来测试它，以打印您当前所处的任何方块的ID：

```python
while True:
    x, y, z = mc.player.getPos()
    block_beneath = mc.getBlock(x, y-1, z)
    print(block_beneath)
```

我们可以使用一个if陈述来选择我们是否种植花：

```python
grass = 2
flower = 38

while True:
    x, y, z = mc.player.getPos()  # player position (x, y, z)
    block_beneath = mc.getBlock(x, y-1, z)  # block ID

    if block_beneath == grass:
        mc.setBlock(x, y, z, flower)
    sleep(0.1)
```

接下来我们可以把我们站在草地上的玻璃替换成草地。

```python
if block_beneath == grass:
    mc.setBlock(x, y, z, flower)
else:
    mc.setBlock(x, y-1, z, grass)
```

现在我们可以向前走，如果我们走在草地上，我们会留下一朵花。如果下一个方块不是草地，就变成草地。当我们转身走回来，在我们后面留下一朵花。

## 玩TNT

另一个有趣的方块是TNT！使用正常的TNT方块：

````
tnt = 46
mc.setBlock(x, y, z, tnt)
```

![mcpi-tnt.png](http://ouav818sk.bkt.clouddn.com/mcpi-tnt.png)

然而，这个TNT是相当无聊的，你无法点燃它。尝试应用data为1：

```
tnt = 46
mc.setBlock(x, y, z, tnt, 1)
```

现在用你的剑，左击TNT：它将被激活，并将在几秒钟内爆炸！

### 现在尝试制作一个大方块的TNT方块！

```
tnt = 46
mc.setBlocks(x+1, y+1, z+1, x+11, y+11, z+11, tnt, 1)
```

![mcpi-tnt-blocks.png](http://ouav818sk.bkt.clouddn.com/mcpi-tnt-blocks.png)

现在你会看到一个大   方块的TNT   方块。去激活一个TNT，然后逃跑看看节目！渲染图形真的很慢，因为很多事情一下子改变了。

## 快乐流动的熔岩。

一个玩得很开心的方块是流动的熔岩。

```python
from mcpi.minecraft import Minecraft

mc = Minecraft.create()

x, y, z = mc.player.getPos()

lava = 10

mc.setBlock(x+3, y+3, z, lava)
```

找到你刚刚放置的方块，你应该看到熔岩从那里流出。

熔岩的有趣之处在于，当它冷却下来时，它将变成岩石。移动到您的世界的另一个位置，并尝试：

```python
from mcpi.minecraft import Minecraft
from time import sleep

mc = Minecraft.create()

x, y, z = mc.player.getPos()

lava = 10
water = 8
air = 0

mc.setBlock(x+3, y+3, z, lava)
sleep(20)
mc.setBlock(x+3,y+5, z, water)
sleep(4)
mc.setBlock(x+3, y+5, z, air)
```

您可以调整sleep参数以允许更多或更少的熔岩流动。


## 接下来是什么？

现在有很多你可以做的，比如做一些小东西在Minecraft世界并使用Python。

### 网络游戏

如果多个人将Raspberry Pis连接到本地网络，他们可以加入同一个Minecraft世界并一起玩。玩家可以在Minecraft世界中看到对方。

### API参考

有关功能的更广泛的文档和方块ID的完整列表，请参阅[stuffaboutcode.com](http://www.stuffaboutcode.com/p/minecraft-api-reference.html)上的API参考。