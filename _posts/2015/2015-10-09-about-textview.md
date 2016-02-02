---
layout: post
title: TextView source
categories:
- Programming
tags:
- Android
- dependency

---

##TextView


```
android.widget.TextView;
```
*为用户显示文字并支持随意编辑。TextView是一个带有Editor的完整实现，但是本基础类并不暴露编辑功能，子类EditText则开放了编辑。*

*如果希望允许用户复制粘贴文本内容，请设置textIsSelectable属性为true，调用setTextIsSelectable方法也可。*

*这个标志允许用户用手势选取，并触发系统原生的复制粘贴功能。*


```
android.widget.Editor;
```
*一个TextView的帮助类，用来处理可编辑的文字view*

####我们看看在TextView中，哪些地方用到了Editor
```
private Editor mEditor;
```
*EditText的特殊数据，需要使用的情况下才创建。*

```
private void createEditorIfNeeded() {
    if (mEditor == null) {
        mEditor = new Editor(this);
    }
}
```
也就是说普通的不可编辑的TextView应该用不到这个类，我们在onDraw中也能看到Editor的身影，发现在绘制的时候mEditor也不是必须的。


```
@Override
protected void onDraw(Canvas canvas){
	...
	if (mEditor != null) {
		mEditor.onDraw(canvas, layout, highlight, mHighlightPaint, cursorOffsetVertical);
	} else {
		layout.draw(canvas, highlight, mHighlightPaint, cursorOffsetVertical);
    }
	...
}
```
其中Layout对象应该是起到文字绘制作用的类，我们来看一看

```
android.text.Layout;
abstract class
```

*一个在屏幕可见元素中管理文字布局的基础类*

*可编辑的文字，请使用DynamicLayout，这个类会在文字变化时更新*

*不能编辑的文字使用StaticLayout*

---

其中的draw方法：

```
public void draw(Canvas canvas, Path highlight, Paint highlightPaint,
            int cursorOffsetVertical) {
        final long lineRange = getLineRangeForDraw(canvas);
        int firstLine = TextUtils.unpackRangeStartFromLong(lineRange);
        int lastLine = TextUtils.unpackRangeEndFromLong(lineRange);
        if (lastLine < 0) return;
        drawBackground(canvas, highlight, highlightPaint, cursorOffsetVertical,
                firstLine, lastLine);
        drawText(canvas, firstLine, lastLine);
    }

```
没错了，Layout这个类控制TextView的绘制区域和内容，而TextView中也提供了公共的getLayout方法给我们使用。
Layout使用的是TextPaint，一个Paint的子类，允许设置包括下划线在内的一系列参数.
Layout的绘制区域就是文字真实的显示区域了.

我们来看下完整的TextView绘制过程:

**首先是检查了下是否是滚动模式**
我们知道滚动模式的时候并不是所有的内容都显示全的,应该是需要偏移量等特殊处理,暂且跳过.

**绘制背景**
**提取吸附图片的距离,滚动距离,bound坐标,绘制方向,偏移等**
**绘制吸附图片**

**检查Layout**
要注意layout初始化是使用控件本身的宽度和去除吸附图片后的宽度来计算的
很可惜的是assumeLayout本身是个private方法,不想使用反射的话我们只能从makeNewLayout方法入手

**检查是绘制hint还是有内容,设置相应颜色**
**考虑阴影和padding,裁剪矩形画布**
**不是顶部绘制的话还要移动画布便于绘制**
**滚动模式相关移动**

**绘制layout或者editor**

结束

所以在textview中绘制主要还是考虑gravity,吸附图片,间距等内容.文字的绘制都封装在layout里面了


###android.text.Layout

layout的绘制过程:

**获取需要绘制的行范围** 当然范围不能大于绘制范围,下面会被切掉
在DynamicLayout中 getLineCount 返回的是PackIntVector的size-1
PackIntVector 是一个封装的二维int数组

**画背景** 

文字的绘制过程:


#待续...



