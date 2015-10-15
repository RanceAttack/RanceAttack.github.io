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

依赖[BigBadaboom/androidsvg](https://github.com/BigBadaboom/androidsvg)

---

[androidsvg](http://bigbadaboom.github.io/androidsvg/)
同时支持 1.1 和 1.2 版的svg

除了动画和滤镜大部分都能支持

没有直接将命令转换为绘图, 而是先将命令和数值封装起来, 需要图形的时候再去生成 非常方便我们获取这些数据

使用方法

```
SVG svg = SVG.getFromAsset(getContext().getAssets(),svgPath);
svg.registerExternalFileResolver(myResolver);

svg.renderToCanvas(canvas);
```
class Style : 包含了svg的附加属性,fill stroke等

class Box : 记录一个正矩形区域,支持交集操作

class SvgObject : svg中的最小子图形,包含SVG 和父SvgContainer的引用

interface SvgContainer : 管理SvgObject,"获取全部"和"添加一个"

class SvgElementBase : 继承SvgObject, 具有id,style,classname等属性

```
class SvgElement : SvgElementBase的补充 ,增加了一个Box boundingBox属性

interface SvgConditional : 实现的类需增加
 Feastures, Extensions, SystemLanguage, Formats,Fonts属性
 
class SvgConditionanlElement : SvgElement实现SvgConditional的子类  
 
class SvgConditionalContainer : 比SvgConditionanlElement多实现了SvgContainer,现在可以作为一个Group了

class SvgPreserveAspectRatioContainer : 比SvgConditionalContainer 多了一个 PreserveAspectRatio 成员,添加了尺寸适配

class SvgViewBoxContainer : 比上面增加了一个Box viewBox属性,现在有bounding 和 view 两个Box

```
class Svg : 根SvgGroup, 有id,style,classname,boundingBox,viewBox,
Feastures, Extensions, SystemLanguage, Formats,Fonts等属性,有PreserveAspectRatio支持适配,带尺寸和坐标



class Group : 作为Group支持子图形的Matrix变形

class Defs : 标示为不直接转化的Group

class Use : 带坐标和长宽的Group (为什么不用Box?)

class Stop : 一个不关心子Element的Group

class GraphicsElement : 基础的SvgConditionanlElement,多支持一个Matrix变形, 这个类用于各种多边形和圆的数据建模

class Path : GE子类,记录Path的命令和点的个数

class PathDefinition : 把Path的命令和数字值分别用数组存起来,数组长度的扩展是自己做的(没有用ArrayList<>...)

class Rect : GE子类,记录矩形长宽坐标和圆角

class Circle : GE子类,记录圆心和半径

class Ellipse : GE子类,记录椭圆的圆心和两个半径

class Line , PolyLine: 线

class Polygon : GE子类 只存一个点的list,多边形就是一个闭合的折线嘛

其他一些Text和Gradient的class 我自己暂时用不到就不例举了


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
	
	
	