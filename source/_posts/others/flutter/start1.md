---
title: flutter 入门踩坑
date: 2020-10-13 17:28:53
tags: others
---

#### 执行flutter run报错

{% asset_img 1.png %}

<p>这种情况可能是gradle压缩包版本的问题，更换下gradle的压缩包并更改调用路径</p>
<p><b>解决方法:</b></p>

<!-- more -->

<p>打开项目根目录 -> android/gradle/wrapper/gradle-wrapper.properties 文件</p>
<p>修改distributionUrl的值，将gradle-xxx-all.zip替换成gradle-6.4.1-all.zip</p>

```
#Fri Jun 23 08:50:38 CEST 2017
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-6.4.1-all.zip
```

去[官网](https\://services.gradle.org/distributions/gradle-6.4.1-all.zip) 下载 'gradle-6.4.1-all.zip' 压缩包;不需要解压缩，直接放到如图所示目录中:

{% asset_img 2.png %}

<p>就是放到 ${user_home}/.gradle/wrapper/dists/gradle-6.4.1-all 里面的文件夹里</p>

<p>重新执行flutter run,等待编译</p>