---
title: Jenkins Android 开发持续集成配置
date: 2018-02-28 13:11:09
categories:
  - Android
tags:
  - Android
  - Jenkins
---


1. Jenkins Android编译环境配置 ：略
  
   ![](https://raw.githubusercontent.com/hienao/hienao.github.io/blog_img/img/20190116102943.png)

2. 配置Jenkins编译设置

   - 参数化构建过程：

     - 此处为设置android工程中的Gradle参数的，详见project 下的`gradle.properties`文件，如果工程中没有且需要动态指定参数的话可以自己创建一个，例子如下图所示：

       ![](https://raw.githubusercontent.com/hienao/hienao.github.io/blog_img/img/20190116102709.png)

       参数以K-V键值对的方式写入即可。这些参数只是演示，如果工程中不需要，参数化构建过程可以不设置。

   - 源码管理

     - 选择Git或者SVN并输入账号密码即可，略。

   - 构建触发器

     - Poll SCM 用于执行定时任务，每隔一段时间检测一次svn代码是否有变化，如果有变化就重新编译apk，途中设置的是每5分钟检测一次：

       ```shell
       H/5 * * * *
       ```

   - 构建

     - 选择gradle版本，指定编译task，一般而言直接按照自己的需求复制我下面的例子即可：

       正式包：

       ```groovy
       clean 
       assembleRelease
       ```

       测试包：

       ```groovy
       clean 
       assembleDebug
       ```

       正式&测试包：

       ```groovy
       clean 
       build
       
       ```

   - 构建后操作：

     - 用于存档的文件(需要按照自己的工程修改):

       ```
       trunk/TransfarMobileOA/app/build/outputs/apk/release/*.apk,trunk/TransfarMobileOA/app/build/outputs/mapping/**/mapping.txt
       ```

     - 上传到Fir.im或者蒲公英

       - Fir.im  需安装插件

         fir.im Token  填入你在firim的apitoken即可。

       - 蒲公英

         在"构建"步骤中的Execute shell 处填写如下参数，按照自己工程修改即可。

         ```shell
         curl -F "file=@${WORKSPACE}/trunk/TransfarMobileOA/app/build/outputs/apk/debug/transfar_oa_debug.apk" -F "uKey=你的蒲公英UserKey" -F "_api_key=你的蒲公英APItoken" http://www.pgyer.com/apiv1/app/upload
         ```

         其中file部分对照修改为自己工程打包Apk所在位置就好。