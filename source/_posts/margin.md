---
title: margin
date: 2017-01-05 16:48:55
tags: css margin
---

# Intro
margin作为盒模型的五大部分之一,它独树一帜,在众多css属性中脱颖而出.
* 背景透明
* 可以设置负值
* margin重叠
* 可以改变元素宽度


> 盒模型

{% image http://img.mukewang.com/5365d7b10001e8d506350529.jpg 300 240 "盒模型" %}

从图中可以看出padding属性和content属性层叠background-image属性，层叠background-color属性，这个是存在的，它们四者之间构成了Ｚ轴（垂直屏幕的坐标）多重层叠关系。但是border属性与margin属性、padding属性三者之间应该是平面上的并级关系，并不能构成Ｚ轴的层叠关系。



# 背景透明
margin不会显示背景

## 例子:等高布局

```html+
<div class="box">
  <div class="a">sdf<br/>sdf</div>
  <div class="b">sdfa</div>
</div>
```
```css+
.box{
  overflow:hidden;
}
.a,.b{
  float:left;
  margin-bottom:-600px;
  padding-bottom:600px;
}

.a{
  background:red;
  width:50%;
}
.b{
  background:blue;
  width:50%;
}
```



{% tabbed_codeblock %}
  <!-- tab html -->
<div class="box">
  <div class="a">sdf<br/>sdf</div>
  <div class="b">sdfa</div>
</div>
  <!-- endtab -->
  <!-- tab css -->
.box{
  overflow:hidden;
}
.a,.b{
  float:left;
  margin-bottom:-600px;
  padding-bottom:600px;
}

.a{
  background:red;
  width:50%;
}
.b{
  background:blue;
  width:50%;
}
  <!-- endtab -->
{% endtabbed_codeblock %}
# 负值
## 例子:布局
```html
<div class='container'>
  <div class="content">content width:100%,将sidebar挤到下一行.siderbar margin-left取负值,将移动到上一行</div>
</div>
<div class='sidebar'>siderbar</div>
```
```css
.container{
  float:left;
  width:100%;
}
.content{
  margin-right:200px;
}
.sidebar{
  width:190px;
  height:100px;
  margin-left:-190px;
  float:left;
  background:lightblue;
}

```
# margin重叠
## 

# 改变元素宽度
## 例子:宽度变大
```html
<div id="test-width">No2父元素
    <p id="test-block">我是No2子元素 </p>
</div>
<hr />
<div id="test-width2">No2父元素
    <p id="test-block2">我是No2子元素 </p>
</div>
```
```css
#test-width, #test-width2 {
    text-align: center;
    margin-right: auto;
    margin-left: auto;
    width: 400px;
    height: 80px;
    background-color: purple;
}
#test-block, #test-block2 {
    background-color: blue;
}
#test-block2 {
    margin-right: -100px;
    margin-left: -100px;
}

```
因为P为block元素且没有指定width，则p的width+padding+border+margin=父级div的width（这里都是说的水平方向,垂直方向会发生重叠）。
这里假定padding,border为0。父级div width为400px。p的width为inherit。不设置margin，则p的width为400px。正如text-block。

当margin-left:-100px;，p.width + (-100px) = 400px。So, p的width就变成了500px。

这里说的p都是假定为inherit width。如果指定了p的width，则margin-left,margin-top为负值会引起p元素位置的变化。