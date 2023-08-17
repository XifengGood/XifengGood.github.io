---
title: GLSL的LUT实现
date: 2022-05-04 10:57:36
index_img: /img/colorGrading.webp
excerpt: Lut将一种颜色映射到另外一种颜色
tags:
- GLSL
- Lut
categories: 
- [GLSL, Lut]
---
### LUT
color lut全名Color Lookup Table，即颜色查询表，是一种简单的后期处理。255 * 255* 255为无质量降低，而一旦小于就有可能导致色块和色带，不过通过一些插值算法能减少色块和色带。
###  实现思路
根据color的蓝色分量选取到对应的lut图的格子，小数的话选取向下和向上取整的格子进行混合。
根据color的r,g确定uv的坐标位置。
获取两个坐标上的颜色根据之前蓝色格子小数部分进行混合。
将原来的颜色和处理的颜色根据强度进行混合。

[^1]: https://www.jianshu.com/p/96a61110a5ae