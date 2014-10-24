---
layout: post_w
title: "Property animation and view animation"
tags:[animation,android] 
categories:[android,viewSystem,animation]
---

# Property Animation

## 工作原理

ValueAnimator中包含：TimeInterpolator.以及TypeEvaluator

动画计算过程：

- 根据elapsed的时间和动画duration，计算百分比fraction.
- interpolator使用fraction计算出一个新的fraction.
- TypeEvaluator使用新的fraction计算，return animated value.比如浮点值或颜色argb.

ObjectAnimator
A subclass of ValueAnimator.
可以设置目标object及object property.
当Animation计算了一个新值,objectAnimator会自动更新该object的property值.不用手动使用计算过的animated value更新UI.

{% highlight Java %}
ObjectAnimator.ofFloat(targetObject, "propName", 1f)
{% endhighlight %}

## xml实现中的实现 

xml位于res/animator下
根元素`set` or `animator` `objectAnimator`

{% highlight Java %}
AnimationSet set = AnimatorInflater.loadAnimator(context,R.anim.name);
set.setTarget(myObject);
set.start();
{% endhighlight %}


# View Animation

分为tweened animation 和 frame-by-frame animation

res/anim目录下新建xml文件.
根元素`set` or `alpha` `translate` `scale` `rotate`

{% highlight Java %}
Animation anim = AnimationUtils.loadAnimation(this,R.anim.animname);
view.startAnimation(anim);
{% endhighlight %}


*********************************<br/>

# Diff：

- view animation只能用于animate view object,而且只有有限的几个方面可以animate, i.e. rotate,translate,alpha,scale.
- view animation相关位于android.view.animation包下.Property animation位于android.animation包.
- xml实现,view animation xml需放置于res/anim下,property animation xml在res/animator下.java中加载xml动画的方式也不同.
