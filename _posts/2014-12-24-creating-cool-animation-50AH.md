---
layout: post_w
title: "Creating cool animation <50 Android Hacks>"
tags: [android] 
categories: [50 android hacks, 读书笔记]
---

# Creating cool animation <50 Android Hacks>

## Hack 5: transitions with TextSwitcher and ImageSwitcher

### 切换TextView内容时如何显示动画?
使用TextSwitcher,步骤:
- textSwitcher.setFactory
- textSwticher.setInAnimation/setoutAnimation

## Hack 6: Adding eye candy to your ViewGroups's children

### 添加child view到viewgroup时,显示动画?
	LayoutAnimationController controller = new LayoutAnimationController(animationSet, 0.5f);
	mListView.setLayoutAnimation(controller);

## Hack 7: Doing animation over the canvas
A canvas work for you as a interface to the actual surface upon which your graphics will be drawn.
Canvas holds all draw calls,这些calls在underlying Bitmap上执行.

## Hack 8: Slideshow using the Ken Burns effect

> Ken Burns effect is nothing more than a type of panning and zooming effect used in video production from still imagery.   

通过放大.以及translate动画实现Ken Burns effect. 为了使用新的animation API并兼容3.0以下使用了NineOld库.

ps: idea很好,把rss feed列表改为image+title展示,因为静态图片展示太无聊,又添加Ken Burns效果.

## Refs
