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

#### 2014-12-3 ####
针对功能点#1, google 'split voice silent period'得到关键词'speech detection' or 'Voice Activity Detection'. 从而有了研究的方向.

#### 2014-12-18
[Introduction to Computer Music: Volume One](http://www.indiana.edu/~emusic/etext/toc.shtml)发现的不错edu资源,系统地介绍了acoustic方面的内容.   
看完后对Waveform的纵轴采样采的到底是是什么?文件中保存的audio raw data究竟代表的是什么什么值?这些问题能有些直观的了解.

#### 2015-1-11
经过多重尝试,包括ringdroid的cheapWAV/cheapMP3(获取raw data的方式不好),Visualizer api(获取到的bytes与pcm raw data不一样,仅用于可视化显示的目的),sphinx4(使用的是javax.audio包,android中不包含).
最终还是使用MediaExtractor,MediaCodec来获取pcm raw data.目前测试过16bit/stereo/44100的wav文件,以及mp3文件.
ps:没有思路的时候,在google或SO上,搜索宽泛点的关键词,如android pcm,一条目一条目的看过去获取线索.