---
title: Android 9.0 sdk导致的gradle依赖问题解决
date: 2018-07-07 13:22:09
categories:
  - Android
tags:
  - Android
  - gradle
---


##### Bug表现

* 今天在项目开发中，编译工程时，突然出现了一个莫名的错误提示，即:


    > ERROR: In <declare-styleable> FontFamilyFont, unable to find attribute android:fontVariationSettings
    > ERROR: In <declare-styleable> FontFamilyFont, unable to find attribute android:ttcIndex



##### 可能原因

* 通过StackOverFlow上查询得知，出现这种情况的原因共有两种：

  1. 可能是compileSdkVersion与targetSdkVersion的版本不一致。
  2. 可能是工程中依赖的com.android.support:support-v4 (或support-v7) 版本不一致。

* 解决方案：第一种将compileSdkVersion与targetSdkVersion的版本改成一致即可，这个原因跟我工程里面的状况不符合，我的工程里都是26，所以略过。

  第二种检查方法比较简单，在AndroidStudio的终端Terminal里输入`gradle app:dependencies`来打印gradle来检查gradle依赖，发现corelib module里，有一个控件依赖如下：

  ```
  +--- cn.qqtheme.framework:WheelPicker:1.1.3
  |  |    +--- cn.qqtheme.framework:Common:1.1.3
  |  |    |    +--- com.android.support:support-v4:+ -> 26.1.0 (*)
  |  |    |    \--- com.android.support:support-annotations:+ -> 28.0.0-alpha1
  |  |    +--- com.android.support:support-v4:+ -> 26.1.0 (*)
  |  |    \--- com.android.support:support-annotations:+ -> 28.0.0-alpha1
  ```

  可以看到com.android.support:support-annotations的版本依赖被调整为了28.0.0-alpha1版本，应该就是这个问题了，那么需要到cn.qqtheme.framework:WheelPicker的引入部分将

  ```groovy
  api ('cn.qqtheme.framework:WheelPicker')
  ```

  改为：

  ```groovy
  api ('cn.qqtheme.framework:WheelPicker'){ exclude group: 'com.android.support' }
  ```

  之后重新编译，问题即可解决。