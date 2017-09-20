---
title: margin合并(collapsing margins) 
date: 2017-05-01 15:04:24
tags:
---
# Collapsing margins
>  CSS中，两个或者多个盒（可能但不一定是兄弟）的相邻的margin会被结合成一个margin。Margin按这种方式结合叫合并（collapse），产生的结合的margin叫做合并margin。



> 水平margin不会合并;相邻的竖直margin会合并，除了：

* 根元素的盒的margin不合并
* 如果一个有空隙（clearance）的元素的top和bottom margin是相邻的，它的margin会与紧跟着的兄弟的相邻margin合并，但产生的margin不会与父级块的bottom margin合并

