---
title: 升级老的Android项目gradle版本后，出现错误提示
date: 2018-07-07 13:22:09
categories:
  - Android
tags:
  - Android
  - gradle
  - Android Studio
---

> Configuration on demand is not supported by the current version of the Android Gradle plugin since you are using Gradle version 4.6 or above. Suggestion: disable configuration on demand by setting org.gradle.configureondemand=false in your gradle.properties file or use a Gradle version less than 4.6.

按提示配置`org.gradle.configureondemand=false`后并没有什么卵用。。。。

从网上发现了以下设置步骤可以关闭`org.gradle.configureondemand：

1. 在gradle.properties删除org.gradle.configureondemand设置

2. Windows：File > Settings > Build, Execution, Deployment > Compiler，去掉configure on demand的勾选

   Mac：Android Studio>Prefrence> Build, Execution, Deployment > Compiler，去掉configure on demand的勾选