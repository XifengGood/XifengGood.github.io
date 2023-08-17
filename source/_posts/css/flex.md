---
title: flex
date: 2022-05-20 16:43:09
index_img: /img/css.webp
tags:
 - css
 - flex
categories:
 - - css
   - flex
excerpt: Flex 是 Flexible Box 的缩写，意为"弹性布局"。
---
原文：https://www.ruanyifeng.com/blog/2015/07/flex-grammar.html
## 一、Flex 布局是什么？
Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。<br>

任何一个容器都可以指定为 Flex 布局。

```
.box{
  display: flex;
}
```
行内元素也可以使用 Flex 布局。

```
.box{
  display: inline-flex;
}
```
Webkit 内核的浏览器，必须加上-webkit前缀。

```
.box{
  display: -webkit-flex; /* Safari */
  display: flex;
}
```
注意，设为 Flex 布局以后，子元素的float、clear和vertical-align属性将失效。
## 二、基本概念
采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。<br>
容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross start，结束位置叫做cross end。
<br>
项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。
{% asset_img bg2015071004.png %}