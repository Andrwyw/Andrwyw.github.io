---
layout: post_w
title: "Tips and recommendations for android layouts"
tags: [android] 
categories: [50 android hacks, 读书笔记]
---

# Hack 1: centering views using weights

## ?如何用最简单的方法,让button居中且占parent50%的宽度?
	- 使用androi:weightSum和android:layout_weight实现50%宽度.
	- LineaLayout中用android:gravity:center属性实现居中.


# Hack 2: Using layz loading and avoiding replication

## ?部分布局在很多个xml中重复出现?
	- 使用<include layout="">标签实现布局引入.
	- 在<include>标签中可以override included layout的root view的参数.如果需要override,layout_height/wight属性需要同时override,其他override的layout属性才会起作用.

## ?默认让一个view先invisible,随后根据情况让它visible?
通常情况下,我们动态控制某些布局的隐藏显示,都是直接控制它的visibility属性的,但如果这些布局始终是invisible,没有机会变visible,那就有些浪费了.
因为invisible的view,是有size的,在布局inflate的时候也是会有开销的.为了避免这种情况,可以使用<ViewStub>实现lazy loading.

?为什么不用visibility = View.Gone?
因为visibility为Gone的view在view hierarchy中的开销还是要比ViewStub大.
> A ViewStub is a dumb and lightweight view. It has no dimension, it does not draw anything and does not participate in the layout in any way. This means a ViewStub is very cheap to inflate and very cheap to keep in a view hierarchy.

> ViewStub仅在inflate函数调用时,或被设为visible时,layout resource才会被inflate.并且viewStub会被inflated view替换掉.

ps:如果只是单个的view,可能不会发现性能有所提升,但当这个view的View Hierarchy比较大时,就能发现了.




# Refs
- [Re-using Layouts with <include/>](http://developer.android.com/training/improving-layouts/reusing-layouts.html)
- [ViewStub class](http://developer.android.com/reference/android/view/ViewStub.html)
- [Android Layout Tricks #3: Optimize with stubs](http://android-developers.blogspot.sg/2009/03/android-layout-tricks-3-optimize-with.html)