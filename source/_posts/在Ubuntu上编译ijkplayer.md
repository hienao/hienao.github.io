---
title: 在Ubuntu上编译ijkplayer
date: 2018-07-07 13:01:09
categories:
  - Android
tags:
  - Android
  - ijkPlayer
---

## 为什么要编译ijkplayer?

> 因为官方的Ijkplayer库是大多数项目中会用到的格式，如果不需要那么多格式，想要减小apk的体积、或者需要的格式比较多就需要重新编译了。

##编译环境

> 系统：Ubuntu Kylin 16.04 LTS（我是装在win10系统下的虚拟机里的）
>
>
## 先看下官方给出的步骤

> 原文参见：https://github.com/Bilibili/ijkplayer

### Before Build
```
# install homebrew, git, yasm
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install git
brew install yasm

# add these lines to your ~/.bash_profile or ~/.profile
# export ANDROID_SDK=<your sdk path>
# export ANDROID_NDK=<your ndk path>

# on Cygwin (unmaintained)
# install git, make, yasm
```

- If you prefer more codec/format
```
cd config
rm module.sh
ln -s module-default.sh module.sh
cd android/contrib
# cd ios
sh compile-ffmpeg.sh clean
```

- If you prefer less codec/format for smaller binary size (include hevc function)
```
cd config
rm module.sh
ln -s module-lite-hevc.sh module.sh
cd android/contrib
# cd ios
sh compile-ffmpeg.sh clean
```

- If you prefer less codec/format for smaller binary size (by default)
```
cd config
rm module.sh
ln -s module-lite.sh module.sh
cd android/contrib
# cd ios
sh compile-ffmpeg.sh clean
```

- For Ubuntu/Debian users.
```
# choose [No] to use bash
sudo dpkg-reconfigure dash
```

- If you'd like to share your config, pull request is welcome.

### Build Android
```
git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-android
cd ijkplayer-android
git checkout -B latest k0.7.9

./init-android.sh

cd android/contrib
./compile-ffmpeg.sh clean
./compile-ffmpeg.sh all

cd ..
./compile-ijk.sh all

# Android Studio:
#     Open an existing Android Studio project
#     Select android/ijkplayer/ and import
#
#     define ext block in your root build.gradle
#     ext {
#       compileSdkVersion = 23       // depending on your sdk version
#       buildToolsVersion = "23.0.0" // depending on your build tools version
#
#       targetSdkVersion = 23        // depending on your sdk version
#     }
#
# If you want to enable debugging ijkplayer(native modules) on Android Studio 2.2+: (experimental)
#     sh android/patch-debugging-with-lldb.sh armv7a
#     Install Android Studio 2.2(+)
#     Preference -> Android SDK -> SDK Tools
#     Select (LLDB, NDK, Android SDK Build-tools,Cmake) and install
#     Open an existing Android Studio project
#     Select android/ijkplayer
#     Sync Project with Gradle Files
#     Run -> Edit Configurations -> Debugger -> Symbol Directories
#     Add "ijkplayer-armv7a/.externalNativeBuild/ndkBuild/release/obj/local/armeabi-v7a" to Symbol Directories
#     Run -> Debug 'ijkplayer-example'
#     if you want to reverse patches:
#     sh patch-debugging-with-lldb.sh reverse armv7a
#
# Eclipse: (obselete)
#     File -> New -> Project -> Android Project from Existing Code
#     Select android/ and import all project
#     Import appcompat-v7
#     Import preference-v7
#
# Gradle
#     cd ijkplayer
#     gradle

```


### Build iOS
```
git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-ios
cd ijkplayer-ios
git checkout -B latest k0.7.9

./init-ios.sh

cd ios
./compile-ffmpeg.sh clean
./compile-ffmpeg.sh all

# Demo
#     open ios/IJKMediaDemo/IJKMediaDemo.xcodeproj with Xcode
# 
# Import into Your own Application
#     Select your project in Xcode.
#     File -> Add Files to ... -> Select ios/IJKMediaPlayer/IJKMediaPlayer.xcodeproj
#     Select your Application's target.
#     Build Phases -> Target Dependencies -> Select IJKMediaFramework
#     Build Phases -> Link Binary with Libraries -> Add:
#         IJKMediaFramework.framework
#
#         AudioToolbox.framework
#         AVFoundation.framework
#         CoreGraphics.framework
#         CoreMedia.framework
#         CoreVideo.framework
#         libbz2.tbd
#         libz.tbd
#         MediaPlayer.framework
#         MobileCoreServices.framework
#         OpenGLES.framework
#         QuartzCore.framework
#         UIKit.framework
#         VideoToolbox.framework
#
#         ... (Maybe something else, if you get any link error)
# 
```

## 接下来我们开始自己的编译

 ### 1.安装Java环境（已有可跳过）

* 保险起见请先在终端执行一次一下代码

```
 sudo apt-get update
```

* 然后检查是否安装了jdk
```
 java -version
```
* 如果出现了下面的输出结果，意味着你并没有安装过Java:

```
The program 'java' can be found in the following packages:
 * default-jre
 * gcj-4.9-jre-headless
 * gcj-5-jre-headless
 * openjdk-8-jre-headless
 * gcj-4.8-jre-headless
 * openjdk-9-jre-headless
Try: sudo apt install <selected package>

```
* 安装jdk命令如下：

```
  sudo add-apt-repository ppa:webupd8team/java
  sudo apt-get update
  sudo apt-get install oracle-java8-installer
  sudo apt-get install oracle-java8-set-default
```

* 安装成功后执行上面检查jdk的命令会看到如下输出：
```
  java version "1.8.0_121"
  Java(TM) SE Runtime Environment (build 1.8.0_121-b13)
  Java HotSpot(TM) 64-Bit Server VM (build 25.121-b13, mixed mode)
```
* 到这里jdk就安装完成了
###2. 安装android开发SDK和NDK

* 首先新建一个文件夹，用来存放sdk和ndk(这步骤看个人习惯，可以放到其他目录去，我新建的目录名称是“Android”)：

```
  mkdir Android
```

* 然后下载android的sdk:

```
  cd Android/
  wget https://dl.google.com/android/android-sdk_r24.4.1-linux.tgz
  tar -zxvf android-sdk_r24.4.1-linux.tgz
  echo 'export ANDROID_HOME="'$HOME'/Android/android-sdk-linux"' >> ~/.bashrc
  echo 'export PATH="$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools"' >> ~/.bashrc
```

* 然后重新打开终端，让环境变量生效。启动Android SDK Manager

```
  android 
```

* 之后来安装NDK：

```
  cd Android/
  wget https://dl.google.com/android/repository/android-ndk-r14b-linux-x86_64.zip
  unzip android-ndk-r14b-linux-x86_64.zip
  echo 'export ANDROID_NDK="'$HOME'/Android/android-ndk-r14b"' >> ~/.bashrc
  echo 'export PATH="$PATH:$ANDROID_NDK"' >> ~/.bashrc
```

* 重启系统（到这步时不重启就不生效，也许只是我这里的个例）

* 启动终端，执行

```
ndk-build
```
	若出现以下提示（只要没提示命令不存在就应该是安装成功了），这说明环境变量设置成功

```
Android NDK: Could not find application project directory !    
Android NDK: Please define the NDK_PROJECT_PATH variable to point to it.    
/home/swart/Android/android-ndk-r14b/build/core/build-local.mk:151: *** Android NDK: Aborting    .  Stop.
```
###3. 安装git、yasm

```
sudo add-apt-repository ppa:git-core/ppa
sudo apt-get update
sudo apt-get install git  
sudo apt-get install yasm
```

* 安装下载完成后，可以使用下面的命令行，确认git和yasm是否安装成功，如果成功的话不会输出命令不存在的提示

```
git --version 
make
```

###3. 正式开工、开始编译（以Android为例IOS、请参照官方文档即可）

* 从git上拉取ijkplayer的源码：

```
git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-android
cd ijkplayer-android
git checkout -B latest k0.7.9
#调整编译的类型的话需要在这行代码所在的位置做调整，调整方式在下面
./init-android.sh

cd android/contrib
./compile-ffmpeg.sh clean
./compile-ffmpeg.sh all

cd ..
./compile-ijk.sh all
```

* 调整编译类型


   如果你需要支持更多的格式

```
cd config
rm module.sh
ln -s module-default.sh module.sh
cd android/contrib
# cd ios
sh compile-ffmpeg.sh clean
```

如果你需要较少的格式以便减小体积（支持hevc）

```
cd config
rm module.sh
ln -s module-lite-hevc.sh module.sh
cd android/contrib
# cd ios
sh compile-ffmpeg.sh clean
```

如果你需要较少的格式以便减小体积（默认选择）

```
cd config
rm module.sh
ln -s module-lite.sh module.sh
cd android/contrib
# cd ios
sh compile-ffmpeg.sh clean
```

- 对于Ubuntu/Debian的用户
```
# choose [No] to use bash
sudo dpkg-reconfigure dash
```
* 编译完成后在/ijkplayer-android/android/ijkplayer目录下就生成了各个平台上所需的so文件