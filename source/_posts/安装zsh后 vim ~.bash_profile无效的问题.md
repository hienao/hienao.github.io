---
title: 安装zsh后 vim ~/.bash_profile无效的问题
date: 2018-07-14 10:33:00
categories:
  - Mac
tags:
  - Mac
  - 环境配置
---

> - 装了 zsh 之后，修改终端配置就变成了：`vim ~/.zshrc`，而不是：`vim ~/.bash_profile`，所以以后看到别人的文章中需要：`vim ~/.bash_profile`，那你自己要变通思想过来。
> - 同时更新修改后的配置文件也从：`source ~/.bash_profile`，变成了：`source ~/.zshrc`。