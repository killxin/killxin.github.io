---
layout: post
title:  "Phaser实现GitHub信息展示和坦克大战"
categories: jekyll update
tags: phaser in action
---
# 引子

为了制作一款网页游戏，调研了HTML5游戏引擎Phaser，个人认为学习这类工具的最快的方法就是“案例驱动”，

Phaser吸引我的的地方在于提供了685个example，通过看源码加读文档的方式，可以高效的进行代码复用。

本篇博客主要是用作展示，用例子来体会phaser的功能，想了解具体实现，请直接看代码。

# GitHub信息展示

[Player][player]参考了phaser提供的 <http://phaser.io/examples/v2/text/display-text-word-by-word> 例子，

利用了game.time.events.repeat实现了将文字以打字的方式呈现出来。

# 坦克大战

[Tanks][tanks]参考了<http://phaser.io/tutorials/coding-tips-001>,增加了对坦克移动的控制，实现了两个玩家对战，

核心在于对phaser提供的Phaser.Physics.ARCADE的使用，以及响应用户输入，控制页面展示。

[player]:/src/phaser/player.html
[tanks]:/src/phaser/tanks.html










