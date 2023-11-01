---
title: 为了看Flutter到底有没有人用我竟然
slug: In-order-to-see-if-someone-uses-flutter-I-unexpectedly
description: 所以，整体在知乎上吵「Flutter被抛弃了」、「Flutter要崛起了」，有什么意义呢？所有的争论都抵不过数据来的真实。
date: 2022-05-04 16:59:14
copyright: Reprinted
author:  徐宜生 群英传
originaltitle: 为了看Flutter到底有没有人用我竟然
originallink: https://xuyisheng.top/flutter_app_check/
draft: False
cover: https://img1.dotnet9.com/2022/05/1301.png
categories: Flutter
---

Flutter这个东西出来这么久了，到底市场占有率怎么样呢？为了让大家了解这一真实数据，也为了让大家了解当前Flutter在各大App中的使用情况，我今天下载了几百个App，占了手机将近80G空间，就为了得出一个结论——Flutter，到底有没有人用。

首先，我在vivo应用市场中，下载了4月11日软件排行榜中的所有App，总计230个，再加上平时用的比较多的一些App，总共270个App，作为我们的统计基数。

检测方法，我使用LibChecker来查看App是否有使用Flutter相关的so。

https://github.com/zhaobozhen/LibChecker

除了使用LibChecker之外，还有其它方案也可以，例如使用shell指令——zipinfo。

https://github.com/sugood/apkanalyser

Apk本质上也是一种压缩包，所以，通过zipinfo指令并进行grep，就可以很方便的获取了，同时，如果配合一下爬虫来爬取应X宝的Apk下载地址，就可以成为一个全自动化的脚本分析工具，这里没这么强的需求，所以就不详细做了。

## App列表

我们来看下，我都下载了多少App。

![](https://img1.dotnet9.com/2022/05/1301.png)

![](https://img1.dotnet9.com/2022/05/1302.png)

这些App基本上已经覆盖了应用商店各个排行榜里的Top软件，所以应该还是比较具有代表性和说服力的。

下面我们就用LibChecker来看下，这些App里面到底有多少使用了Flutter。

## 统计结果

![](https://img1.dotnet9.com/2022/05/1303.png)

![](https://img1.dotnet9.com/2022/05/1304.png)

![](https://img1.dotnet9.com/2022/05/1305.png)

![](https://img1.dotnet9.com/2022/05/1306.png)

已经使用Flutter的App共52个，占全体样本的19.2%，作为参考，统计了下RN相关的App，共有45个，占全体样本的16.6%，可以说，Flutter已经超过RN成为跨平台方案的首选。

在52个使用Flutter的App中：

- 腾讯系：QQ邮箱、微信、QQ同步助手、蓝盾、腾讯课堂、QQ浏览器、微视、企业微信、腾讯会议
- 百度系：百度网盘、百度输入法
- 阿里系：优酷视频、哈啰出行、淘特、酷狗直播、阿里1688、学习强国、钉钉、淘宝、闲鱼
- 其它大厂：链家、转转、智联招聘、拍拍贷、哔哩哔哩漫画、网易有道词典、爱奇艺、考拉海购、携程旅行、微博、Soul、艺龙旅行、唯品会、飞猪旅行

从上面的数据来看，各大厂都对Flutter有使用，头条系未列出的原因是，目前好像只有头条系大规模使用了Flutter的动态化加载方案，所以原始包内找不到Flutter相关的so，所以未检出（猜测是这样，具体可以请头条系的朋友指出，根据上次头条的分享，内部有90+App在使用Flutter）。

>不过这里要注意的 ，这里并不是选取的大家常用的一些APP来做测试的，而是直接选取的排行榜，如果直接用常用APP来测试，那比例可能更高，大概统计了下，估计在60%左右。

不过大厂里面，京东没有使用Flutter我还是比较意外的，看了下京东的几个App，目前还是以RN为主作为跨平台的方案。这跟其它很多大厂一样，它们不仅使用了Flutter，RN也还可以检出，这也从侧面说明了，各个厂商，对跨平台的方案探索，从未停止。

所以，总结一下，目前使用Flutter的团队的几个特定：

- 创业公司：快速试错、快速开发，像Blued、夸克这也的
- 大厂：大厂的话题永远是效率，如何利用跨平台技术来提高开发效率，是它们引入Flutter的根本原因
- 创新型业务：例如B漫、淘特、Soul这类没有太多历史包袱的新业务App，可以利用Flutter进行极为高效的开发

所以，整体在知乎上吵「Flutter被抛弃了」、「Flutter要崛起了」，有什么意义呢？所有的争论都抵不过数据来的真实。

嘴上说着不要，身体倒是很诚实。

希望这份数据能给你一些帮助。

向大家推荐下我的网站 https://xuyisheng.top/ 专注 Android-Kotlin-Flutter 欢迎大家访问

![](https://img1.dotnet9.com/2022/05/1307.png)