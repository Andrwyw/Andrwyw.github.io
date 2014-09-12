---
layout: post
title: "CircularProgressButton Project Analysis"
tags: [open source project][android]
categories: [Other]
---

# setProgress调用流程

setProgress - morphToProgress - animation.start

**********************************

#C. CircularProgressButton

- onDraw

调用drawIndeterminateProgress或drawProgress

- drawIndeterminateProgress    

CircularAnimatedDrawable.start 、 draw  //无穷进度条

- drawProgress

CircularProgressDrawable.start 、 draw  //进度条

- setProgress

通过poregress的值确定btnState，分别调用morph函数，如morphToProgress

- morphToProgress

调用createProgressMorphing创建MorphingAnimation.
MorphingAnimation.start();

- createProgressMorphing
设置了MorphingAnimation的From、To Aspects
fromWidth:getWidth -> toWidth:getHeight,由wh长方形变为直径为h的圆.

**********************************

#C. MorphingAnimation

- start

* 使用valueAnimator计算left、right边新坐标，gradientDrawable.setBounds
* 使用objectAnimator animate strokeColor和color
* 使用objectAnimator animate cornerRadius
     从idle->progress时，cornerRadius从mCornerRadius->getHeight()，变圆

**********************************

## get

* field定义按 type分组
* enum

{% highlight ruby %}
private enum State {
     PROGRESS, IDLE, COMPLETE, ERROR
}
{% endhighlight %}

* 使用状态机.      // StateManager


## tips

* sample的示例用法中用到的函数。
     如此project中的setProgress函数，wiki中说明Button state depends on progress.

* 自定义view先瞅瞅onDraw、onLayout之类的，or搜索@override.这些函数着看下，入口之二.
* 构造函数、public函数，次级阅读入口.
