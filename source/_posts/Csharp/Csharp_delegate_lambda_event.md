---
title: C# delegate lambda event
date: 2022-05-21 12:41:30
index_img: /img/code.webp
tags:
 - C#
 - code
categories:
 - [code, C#]
---
# 委托(delegate)
## 声明委托（Delegate）
{% blockquote %}
delegate <return type> <delegate-name> <parameter list>
{% endblockquote %}
## 实例化委托（Delegate）
```
public delegate void printString(string s);
...
printString ps1 = new printString(WriteToScreen);
printString ps2 = new printString(WriteToFile);
```
## 委托的多播（Multicasting of a Delegate）
委托对象可使用 "+" 运算符进行合并。一个合并委托调用它所合并的两个委托。只有相同类型的委托可被合并。"-" 运算符可用于从合并的委托中移除组件委托。

使用委托的这个有用的特点，您可以创建一个委托被调用时要调用的方法的调用列表。这被称为委托的 <strong>多播（multicasting）</strong>，也叫组播。

# lambda
{% asset_img lambda.png %}<br>
()=>{}<br>
stats=>stats.kills<br>
[^1]:https://www.runoob.com/csharp/csharp-delegate.html