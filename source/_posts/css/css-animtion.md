---
title: CSS 过渡与动画：transition 属性
date: 2022-05-20 23:16:30
index_img: /img/css.webp
tags:
 - css
categories:
 - css
excerpt: css的transition属性的设置
---

{% blockquote %}
知道了CSS3三剑客之一的transform可以用来让原本规规矩矩的元素产生变形，平移，旋转一样的旋转，接下来使用第二个特性，transition属性，增加变化过程中的过渡效果。
{% endblockquote %}

{% blockquote %}
并不能继承给子类型
{% endblockquote %}

Transition允许CSS的属性值在一定的时间区间内平滑地过渡。这种效果可以在鼠标单击、获得焦点、被点击或对元素任何改变中触发，并圆滑地以动画效果改变CSS的属性值，它的语法如下：
```
transition: property duration timing-function delay;
/*
property：执行过渡的属性
duration：执行过渡的持续时间
timing-function：执行过渡的速率模式
delay：延时多久执行
*/
```

### transition-property
可取值：

<li>none<br>
没有属性会获得过渡效果。
<li>all<br>
所有属性都将获得过渡效果。
<li>property<br>
定义应用过渡效果的 CSS 属性名称列表，列表以逗号分隔。

```
div{
transition-property:width;
-moz-transition-property: width;/* Firefox 4 */
-webkit-transition-property:width; /* Safari 和 Chrome */
-o-transition-property:width; 
/* Opera */}
```

### transition-duration
参数为时间，单位为s（秒）或者ms（毫秒），默认就是0，回想一下如果只有transform属性，是不是变换啪地一下完成了。

```
div{
transition-duration: 5s;
-moz-transition-duration: 5s; /* Firefox 4 */
-webkit-transition-duration: 5s; /* Safari 和 Chrome */
-o-transition-duration: 5s; /* Opera */}
```
### transition-timing-function
既然是动画，那么就有动画的运行速率，不同的速度会产生不同的结果，以下是可取值。
<ol>
<li>ease：（逐渐变慢）默认值，ease函数等同于贝塞尔曲线(0.25, 0.1, 0.25, 1.0).
<li>linear：（匀速），linear 函数等同于贝塞尔曲线(0.0, 0.0, 1.0, 1.0).
<li>ease-in：(加速)，ease-in 函数等同于贝塞尔曲线(0.42, 0, 1.0, 1.0).
<li>ease-out：（减速），ease-out 函数等同于贝塞尔曲线(0, 0, 0.58, 1.0).
<li>ease-in-out：（加速然后减速），ease-in-out 函数等同于贝塞尔曲线(0.42, 0, 0.58, 1.0)
</ol>
{% asset_img rate.webp %}

### transition-delay
参数为时间，单位为s（秒）或者ms（毫秒），默认就是0，也就是立即执行，如果在多个动画直接有先后顺序，那么它就会派上用场。

### 把它们写在一起
浏览器内核的前缀不能忘啊，使用emmet神器来帮助你。
emmet使用如图所示：

{% asset_img transition.webp %}

```
-webkit-transition: all 0.5s ease-in-out 0s;
-o-transition: all 0.5s ease-in-out 0s;
transition: all 0.5s ease-in-out 0s;
```
将它们放在一起的效果如下，可以很明显的发现它们之间的不同。<br>
https://www.jianshu.com/p/5354a9042a2a
