---
layout: post
title: Using supportv7 with 23.0.1
categories:
- Programming
tags:
- Android
- dependency
---

##exit value 2
运行的时候抛出了这样的错误，肯定又是依赖出现了问题
昨天至今并没有添加依赖，后来想起升级了support的版本
于是使用./gradlew app:dependencies查看了一下
发现
```
+--- com.android.support:appcompat-v7:23.0.1
|    \--- com.android.support:support-v4:23.0.1
|         \--- com.android.support:support-annotations:23.0.1
+--- com.android.support:recyclerview-v7:23.0.1
|    +--- com.android.support:support-v4:23.0.1 (*)
|    \--- com.android.support:support-annotations:23.0.1
```
recyclerview 的依赖关系不是很对劲。。。不知道为什么要这样做

另外https://jcenter.bintray.com也得翻墙国内还真是。。。