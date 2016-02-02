title: Android 学习日记 Day 1
tags: memo, env, android
date: 2016-1-14

## 前言

人生不如意，事十常八九。最嫌弃 Java 的我正在维护一套 legacy 的 Java Web 系统，还在为了帮人做论文而学习 Android 开发。

这种大型工具链最难学，知识细节十分琐碎，而且主要集中在工具的使用和针对性框架知识的积累，主要靠背。而且环境搭建更是难中之难……

把学习中的一些知识点记录积累一下，方便复习吧。

## 学习资料

[Develop Apps](http://developer.android.com/develop/index.html)
[《第一行代码-Android》](http://www.amazon.cn/%E7%AC%AC%E4%B8%80%E8%A1%8C%E4%BB%A3%E7%A0%81-Android-%E9%83%AD%E9%9C%96/dp/B00LVHTI9U/ref=pd_bxgy_14_img_2?ie=UTF8&refRID=0G5C7P8T3HFKK0APVNV5)

## 环境配置

今天赶巧家里网不稳定，下载东西断断续续，花了一上午才把环境搭建起来。

1. 下载&安装 Android Studio，推荐使用最新稳定版。
2. OS X 需要单独下载 SDK Tools，放到指定目录。
3. 随便建个工程，进入 AS 后打开 SDK Manager，下载如下项目：
	4. Android SDK Tools
	5. Android SDK Platform-tools
	6. Android SDK Build-tools
	7. SDK Platform
	8. ARM EABI v7a System Image
	9. Intel x86 Atom 64 System Image
	10. Sources for Android SDK
	11. Android Support Repo
	12. Android Support Lib
	13. Intel x86 Emulator Accelerator (HAXM Installer)
14. 在 SDK Manager 的 Settings 中可以设置国内源[AndroidDevTools](www.androiddevtools.cn)
15. 在 AS 中的 System Settings 中也可以设置代理，可以将本地的shadowsocks配置上去。
16. 打开 AVD Manager，安装 HAXM
17. 新建 Emulator，推荐使用x86镜像，据说速度快一些，有兼容性问题时再使用ARM版。
18. 编译运行Hello World。

第一天配置环境，感觉还有很多疑问没有解决，比如每个 Android 版本都有可用的 SDK ，是只需要安装最新的，还是所有要兼容的目标版本都需要呢？SDK 那里有很多项目都是每个 Android 版本都有一个，官网推荐下载最新的，那么如果需要开发针对低版本的 app，需不需要低版本的 SDK Tools 呢？编译的时候发生了些什么，这个 Gradle 构建的流程是什么？这么多的资源文件如何组织起来的？

希望进一步的学习可以解决这些疑问。
