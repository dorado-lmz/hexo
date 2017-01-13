---
title: flex布局
date: 2017-01-05 14:50:00
tags: css3 flex
---

# Intro

# 术语
## 伸缩容器和伸缩项目  
* 伸缩容器  
`display: flex | inline-flex`
> 如果一个伸缩容器设置了inline-flex,并且是float或绝对定位，那display为flex.

* 伸缩项目  
伸缩容器中的每个子元素是伸缩项目。  
> 容器内的文字会包裹成匿名伸缩项目。

## 主轴和侧轴
* 主轴  
伸缩容器在主轴上配置伸缩项目。它不一定是水平，取决于flex-direction。
* 主轴起点,主轴终点  
伸缩项目的配置从容器的主轴起点开始，在主轴终点结束。
* 主轴长度  
伸缩项目在主轴方向上的width或height就是主轴的长度。
* 侧轴  
侧轴与主轴垂直，测轴方向取决于主轴。
* 侧轴起点，测轴终点  
与主轴起点类似
* 侧轴长度  
与主轴长度类似

## 伸缩容器的属性
### flex-direction
flex-direction: row | row-reverse | column | column-reverse;  

* row（默认值）：主轴为水平方向，起点在左端。
* row-reverse：主轴为水平方向，起点在右端。
* column：主轴为垂直方向，起点在上沿。
* column-reverse：主轴为垂直方向，起点在下沿。

### flex-wrap
 flex-wrap: nowrap | wrap | wrap-reverse; 
 
 * nowrap（默认）：不换行。
 * wrap：换行，第一行在上方。
 * wrap-reverse：换行，第一行在下方。
 
### flex-flow

flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。   
`flex-flow: <flex-direction> || <flex-wrap>;`

### justify-content(主轴对齐方式)

justify-content: flex-start | flex-end | center | space-between | space-around;
}   

* flex-start（默认值）：左对齐
* flex-end：右对齐
* center： 居中
* space-between：两端对齐，项目之间的间隔都相等。
* space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

### align-items(侧轴对齐方式)

align-items: flex-start | flex-end | center | baseline | stretch;
}

* flex-start：交叉轴的起点对齐。
* flex-end：交叉轴的终点对齐。
* center：交叉轴的中点对齐。
* baseline: 项目的第一行文字的基线对齐。
* stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

### align-content(伸缩轴对齐方式)
align-content属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

* flex-start：与交叉轴的起点对齐。
* flex-end：与交叉轴的终点对齐。
* center：与交叉轴的中点对齐。
* space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
* space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
* stretch（默认值）：轴线占满整个交叉轴。

## 伸缩项目的属性
> 一个伸缩项目就是容器中的一个子元素。  
> 伸缩项目中的布局是普通流。  
> 伸缩项目有一个主轴长度和侧轴长度。


### order
order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
可以为负值。
### flex-grow
flex-grow属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。
### flex-shrink
flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。   
**负值对该属性无效**
### flex-basis
flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。
### flex
flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。
`  flex: auto | none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]`
该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
### align-self
align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。  

 `align-self: auto | flex-start | flex-end | center | baseline | stretch;`
 该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

## 伸缩行
flex-wrap绝对伸缩容器是否换行。   

* flex-wrap: nowrap . 单行的伸缩容器中所有的子元素在一行显示，可能导致内容溢出容器
* flex-wrap: wrap. 当伸缩容器一行无法容纳所有子元素时，用户代理自动创建新的伸缩行。