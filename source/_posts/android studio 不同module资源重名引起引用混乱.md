---
title: android studio 不同module资源重名引起引用混乱的解决方法
date: 2018-07-14 10:59:09
categories:
  - Android
tags:
  - Android
---

> 解决方案：
>
> 1. 通过gradle配置

``` groovy 
 dependencies { provided fileTree(dir: ‘libs’, include: [‘*.jar’])}
```

> 2.右键module选择open module setting,选择要修改的module名，切换到dependencies页面，将要修改的jar的scope修改provided模式。
