---
layout: post
title: research of svg project
categories:
- Programming
tags:
- Android
- svg

---

##各种svg库
---

[pents90/svg-android](https://github.com/pents90/svg-android/tree/master/svgandroid)

将basic 1.1 版本的 SVG文件 解析成 android.graphics.Picture 对象

Picture 是一个记录绘制过程的类,比较适合svg

其实比起绘制对象我更倾向于获得相应的path

- ParserHeloper
	
		从svg文本中解析数字
- SVG
		
		用来描述矢量图,附带绘制范围

- SVGParser

		解析文件的入口
		
		
---
[geftimov/android-pathview](https://github.com/geftimov/android-pathview)

一个可以动画绘制path和svg的绘图view,

依赖androidsvg-1.2.1.jar(com.caverock.androidsvg)

---
[JorgeCastilloPrz/AndroidFillableLoaders](https://github.com/JorgeCastilloPrz/AndroidFillableLoaders)

一个使用灌注动画的进度条,自己实现的svg绘制图形.

这个项目只关注于获取svg的path并将之转换成android.graphics.Path, 支持 M C L H V Z 命令的识别

缺点是不支持polygon circle等的解析,逻辑比较简单 适合学习

- SvgPathParser

		将svg文件的path解析为 android.graphics.Path
	advanceToNextToken()

	解析字符 从全局变量mIndex开始遍历直到发现命令字母和数字,返回1(绝对坐标命令),2(相对坐标命令),3(数字相关)

	consumeCommand()

	返回mIndex处的命令字符
	
	consumeValue()
	
	返回mIndex后面的一个float数字
	
	parsePath()
	
	首先创建path对象,重置mIndex,并遍历查找M命令
	之后则根据逻辑来生成path
	
	
	