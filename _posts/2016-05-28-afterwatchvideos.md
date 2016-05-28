---
layout: post
title: "afterWatchVideos"
description: ""
category: 
tags: []
---
{% include JB/setup %}
## 简单总结一下看了AppleDeveloper网站上wwdc2015的视频学到的

因为项目弄玩了，老板觉得看视频就是在玩，所以只看了少部分，先说看了的几个这几个吧


###1.Protocol Oriented Programming In Swift（用swift面向协议编程）

视频链接[点击此处](https://developer.apple.com/videos/play/wwdc2015/408/)

这个视频总的来说讲了用POP比用传统的OOP的优势。以及带来的新特性和展望可以用在哪些方面。
一开始作者讲了传统OOP在资源共享方面带来的缺陷：
1.为了不出现可变版本相互影响，往往不得已把资源进行Copy操作。
2.Copy后会降低允许相率。
3.会出现资源的相互竞争。
4.防止互相竞争就加锁。
5.加锁后就更加效率低。
6.还会可能造成死锁。
7.越来越复杂。
8.Bug一堆。

然后作者就引申出用Protocol和Value可以代替class进行愉快的编程。并说swift有很多东西都是用的Protocol。

然后又用Protocol进行Extension，又可以玩出花来，作者说就像magic一样。

###2.Building Better Apps with Value Types in Swift 

视频链接[点击此处](https://developer.apple.com/videos/play/wwdc2015/414/)

一开始，作者也是说了用Reference带来的复杂。还是举例说资源共享时候mutable版本带来的复杂。
然后就说了用Value是多么的安全，多么的方便。
然后又说了要怎么应用ValueTpye。作者就说了其实swift里面的基本数据类型都是ValueType，连集合都是。虽然底层集合是引用类型，但是苹果做了优化，使我们用的时候就按照ValueType用就行了。然后还讲了如何把一个引用类型的对象引入我们的值类型中。
然后又说了ValueType天生就非常适合于Undo操作，因为每一步操作都是值类型都是独立的，切换非常方便。
