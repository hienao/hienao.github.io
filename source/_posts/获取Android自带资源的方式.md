---
title: 获取Android自带资源的方式
date: 2018-07-7 13:24:09
categories:
  - Android
tags:
  - Android
---


资源位置在源代码base/core/res/文件夹中

* 获取string

```java
int lebId = Resources.getSystem()
            .getIdentifier("permlab_accessNetworkState", 
                "string", "android");
String lab = getString(lebId);
```
name：资源名称。

defType：资源类型。
defPackage：资源所在包。

* 资源类型：

  > R.anim、R.animator、R.array、R.attr、R.bool、R.color、R.dimen、R.drawable、R.fraction、R.id、R.integer、R.interpolator、R.layout、R.menu、R.mipmap、R.plurals、R.raw、R.string、R.style、R.styleable、R.transition、R.xml