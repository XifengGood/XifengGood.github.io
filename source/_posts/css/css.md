---
title: css 集合
date: 2022-05-19 21:03:55
index_img: /img/css.webp
tags: 
 - css
categories:
 - css
excerpt: css的各种相关知识
---
### overflow-wrap说明
设置或检索当内容超过指定容器的边界时是否断行。\
CSS3中将 word-wrap 改名为 overflow-wrap；\
由于历史原因，当你使用 overflow-wrap 时，最好同时使用 word-wrap 作为备选，作向前兼容。\
对应的脚本特性为overflowWrap。\
http://caibaojian.com/css3/properties/text/overflow-wrap.htm

### CSS3自定义滚动条样式 -webkit-scrollbar
::-webkit-scrollbar 滚动条整体部分\
::-webkit-scrollbar-thumb  滚动条里面的小方块，能向上向下移动（或往左往右移动，取决于是垂直滚动条还是水平滚动条）\
::-webkit-scrollbar-track  滚动条的轨道（里面装有Thumb）\
::-webkit-scrollbar-button 滚动条的轨道的两端按钮，允许通过点击微调小方块的位置。\
::-webkit-scrollbar-track-piece 内层轨道，滚动条中间部分（除去）\
::-webkit-scrollbar-corner 边角，即两个滚动条的交汇处\
::-webkit-resizer 两个滚动条的交汇处上用于通过拖动调整元素大小的小控件\
https://blog.csdn.net/hanshileiai/article/details/40398177

### *:before,*:after{box-sixzing:border-box}

这里的原代码应该是：
```
*, *:before, *:after {
-webkit-box-sizing: border-box;
-moz-box-sizing: border-box;
box-sizing: border-box;
}
```
是让所有的元素都使用 border-box ，并且包括 伪类 before, after 。并不是特殊的定义它们俩。before,after 默认的display 是 inline， 但难免有人会把它变成 block 来用，这时候统一box-sizing 会好操作一些。
https://www.zhihu.com/question/23143701

### Style transform 属性

### css3属性-webkit-font-smoothing
对字体进行抗锯齿渲染可以使字体看起来会更清晰舒服。在图标字体成为一种趋势的今天，抗锯齿渲染使用也越来越多。
\
font-smoothing是非标准的CSS定义。它被列入标准规范的草案中，后由于某些原因从web标准中被移除了。
\
但是，我们可以用以下两种定义进行抗锯齿渲染
1）Webkit在自己的引擎中支持了这一效果。

-webkit-font-smoothing

它有三个属性值：

none ------ 对低像素的文本比较好

subpixel-antialiased------默认值

antialiased ------抗锯齿很好 

<strsong>例子：</strsong>
```
body{
 
-webkit-font-smoothing: antialiased;
 
}
```
这个属性可以使页面上的字体抗锯齿,使用后字体看起来会更清晰。

加上之后就顿时感觉页面小清晰了。
https://blog.csdn.net/pupilxiaoming/article/details/78221934

### CSS3 @media 查询
<strong>实例</strong>\
如果文档宽度小于 300 像素则修改背景颜色(background-color):
```
@media screen and (max-width: 300px) {
    body {
        background-color:lightblue;
    }
}
```
https://www.runoob.com/cssref/css3-pr-mediaquery.html

### @media的用法
```

@media screen and (max-width: 600px) { 
/*当屏幕尺寸小于600px时，应用下面的CSS样式*/
  .class {
    background: #ccc;
  }
}
/*当屏幕尺寸大于900px时，应用下面的CSS样式*/
@media screen and (min-width: 900px) {
  .class {
    background: #fff;
  }
}
```
注意下顺序，如果你把@media (min-width: 768px)写在了下面那么很悲剧，\
@media (min-width: 1200){ //>=1200的设备 }\
@media (min-width: 992px){ //>=992的设备 }\
@media (min-width: 768px){ //>=768的设备 }\
因为如果是1440,由于1440>768那么你的1200就会失效。\
\
所以我们用min-width时，小的放上面大的在下面，同理如果是用max-width那么就是大的在上面，小的在下面\
@media (max-width: 1199){ //<=1199的设备 }\
@media (max-width: 991px){ //<=991的设备 }\
@media (max-width: 767px){ //<=767的设备 }

https://www.jianshu.com/p/3e12c41ed773

### css选择器
https://www.w3school.com.cn/cssref/css_selectors.asp

### white-space
<strong>实例</strong>
规定段落中的文本不进行换行：
```
p
  {
  white-space: nowrap;
  }
```
可能的值\
值	描述
<pre>
normal	默认。空白会被浏览器忽略
pre	空白会被浏览器保留。其行为方式类似 HTML 中的 &lt;pre&gt; 标签。
nowrap	文本不会换行，文本会在在同一行上继续，直到遇到 &lt;br&gt; 标签为止。
pre-wrap	保留空白符序列，但是正常地进行换行。
pre-line	合并空白符序列，但是保留换行符。
inherit	规定应该从父元素继承 white-space 属性的值。
</pre>
https://www.w3school.com.cn/cssref/pr_text_white-space.asp
https://www.w3school.com.cn/tags/tag_pre.asp

### 图标库
```
.icon-home-fill:before {
    content: "\f017";
}
```
:before伪元素选择器？
https://www.cnblogs.com/zccst/p/3655069.html

### HTML 5 深色模式适配 (prefers-color-scheme)
为了实现 HTML5 的深色模式，我们需要利用 prefers-color-scheme 媒体特性。它能够帮助检测设备的深色模式开启情况。不过，这一方法并不能完美解决所有情况，你还需要注意下面这一点。
```
目前，若结果为 no-preference，无法通过此媒体特性获知宿主系统是否支持设置主题色，或者用户是否主动将其设置为无偏好。出于隐私保护等方面的考虑，用户或用户代理也可能在一些情况下在浏览器内部将其设置为 no-preference。——prefers-color-scheme – CSS（层叠样式表） | MDN
```
&lt;html lang="zh-CN" data-default-color-scheme="auto" data-user-color-scheme="dark" class=""&gt;<br/>
https://zhuanlan.zhihu.com/p/248810469 <br/>
https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media/prefers-color-scheme

### margin-left: auto元素右对齐以及margin: 0 auto水平居中的原理
#### 一、margin-left: auto;元素右对齐
```
flex设置justify-content: flex-end
absolute定位设置rigth: 0
float: right
当父节点和子节点宽度固定时，设置margin-left: auto
…
```
#### 二、margin: 0 auto;水平居中

#### 三、原理
##### 块级元素中auto属性的计算特性
<li>节点元素满足等式：'margin-left' + 'border-left-width' + 'padding-left' + 'width' + 'padding-right' + 'border-right-width' + 'margin-right' = width of containing block，除width以外，左侧属性的默认值为0。
<li>当且仅当其中某一个属性为auto（计算属性）时，这个auto值就是令等式成立的值。
<li>最后一条，当margin-left和margin-right都为auto时，两者就会一样，从而使元素居中。

https://blog.csdn.net/sinat_36521655/article/details/103394464

### position absolute relative
css将每个html元素看作一个box<br>
内联元素被周围元素环绕<br>
块级元素另起一行<br>
这样的布局称为正常流<br>
postion属性可以覆盖定位<br>

https://www.jianshu.com/p/6d481e9d7e2d

### :not(.show)