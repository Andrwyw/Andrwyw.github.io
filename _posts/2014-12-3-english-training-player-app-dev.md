---
layout: post_w
title: "English Training Player开发记录"
tags: [app] 
categories: [app dev]
---

# English Training Player开发记录 #

## 功能点 ##

1. 后退
	播放时,可以回退到这一句话开始的地方,以便重复播放.
2. A-B段重复
	可用可视化的方式,操作A-B段重复播放.最好能实现以word为单位的A/B端点选择.
2. 记录
	听不清楚的句子/词组, A/B两端之间的word,可以保存起来,用于后期review.

## logs ##

2014-12-3
==========
针对功能点#1, google 'split voice silent period'得到关键词'speech detection' or 'Voice Activity Detection'. 从而有了研究的方向.