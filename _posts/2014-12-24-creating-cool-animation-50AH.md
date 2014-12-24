---
layout: post_w
title: "Creating cool animation <50 Android Hacks>"
tags: [android] 
categories: [50 android hacks, 读书笔记]
---

# Hack 5: transitions with TextSwitcher and ImageSwitcher

## ?切换TextView内容时如何显示动画?
使用TextSwitcher,步骤:
- textSwitcher.setFactory
- textSwticher.setInAnimation/setoutAnimation

# Hack 6: Adding eye candy to your ViewGroups's children

## ?添加child view到viewgroup时,显示动画?
	LayoutAnimationController controller = new LayoutAnimationController(animationSet, 0.5f);
	mListView.setLayoutAnimation(controller);


# Refs
