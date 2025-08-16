---
title: gdb command
excerpt: note
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-08-01 21:51:06
---
# gdb

gdb调试前提
```
gcc file.c -g
```

1. 显示源代码
```bash
(gdb) l 							# 下翻源代码
(gdb) l -							# 上翻源代码
(gdb) l 20							# 查看启动程序20行附近的源代码
(gdb) l main						# 查看启动程序main函数附近的源代码
(gdb) l main.c:20					# main.c文件第20行附近的源代码
(gdb) l main.c:main					# main.c文件main函数附近的源代码
```

2. 打断点
```bash
break/b   [文件名:][行号|函数名]			#在某个位置设置一个普通的、持续生效的断点
tbreak/tb [文件名:][行号|函数名]			#在某个位置设置一个只生效一次的一次性断点
```

3. 查看断点信息
```bash
info break/i b						#可以省略到只剩下i b，但空格不能省略，不能使用ib
```

4. 删除断点
```bash
delete/d [n] 			#如果不加断点编号就是删除所有断点，若加上编号则表示删除n号断点
```

## 常用调试指令
1. <span style=color:red;background:yellow>**逐语句/单步调试**</span>

step/s 命令可以用来进行单步调试，即遇到函数调用会进入函数。

```shell
(gdb) step/s
```

2. <span style=color:red;background:yellow>**跳出并执行完函数**</span>

我们可以使用 finish 命令执行完整个函数，并返回到函数调用处：

```shell
(gdb) finish/fin
```

稍微需要注意的是：在main函数当中使用该指令是无效的，因为main一般处在调用栈的最底层。

3. <span style=color:red;background:yellow>**逐过程**</span>

next/n 命令表示逐过程，也就是说遇到函数调用，它不会进入函数，而是把函数调用看作一条语句直接执行完毕。

```shell
(gdb) next/n
```


4. <span style=color:red;background:yellow>**继续，跳过一次断点：**</span>

continue/c 命令可以运行到逻辑上的下一个断点处：

```shell
(gdb) c								#相当于VS当中的继续功能按钮
```

5. <span style=color:red;background:yellow>**忽略断点n次**</span>

我们可以用 ignore 命令来指定忽略某个断点多少次，这在调试循环的时候非常有用。使用格式如下：

```shell
ignore N COUNT
```

常见用法：

```shell
(gdb) ignore 1 10			# 忽略1号断点10次
```

注意：

1. 设定忽略断点次数后，还需要按`c`继续跳过断点才会生效。
2. 设定一次，仅生效一次。
3. <font color=red>**跳过n次断点，那么程序会在(n + 1)次到达这个断点时停下来。**</font>

print/p 命令可以打印表达式的值：

```shell
print/p express				#后面直接跟一个表达式即可，你完全把这个功能当成Linux的计算器来使用
```

<span style=color:red;background:yellow>**如果想要持续的，展示某个表达式的值，使用格式如下：**</span>

```shell
display/disp express					# 每调试一步输出一次express的值
undisplay/undisp [n]					# 删除所有或[n]号自动展示的表达式
info display/i disp						# 显示所有自动展示的表达式信息
```

<span style=color:red;background:yellow>**如果需要查看所有局部变量的值，局部变量窗口，使用格式如下：**</span>

```shell
(gdb) info/i args					# 查看函数的参数
(gdb) info/i locals 				# 查看函数所有局部变量的值
```

<span style=color:red;background:yellow>**查看堆栈信息**</span>

```` shell
bt/backtrace				#查看当前调用堆栈的信息，会一直追溯到程序启动
````

一般我们可以在报错的位置使用该指令，用于查看程序的执行流程以及排查相关的问题。

<span style=color:red;background:yellow>**查看内存，内存窗口**</span>

我们可以用 x 命令查看内存的值，类似VS当中的内存窗口，虽然不如VS那么直观，但在某些场景中会有奇效。

基本使用格式如下：

```` shell
x/(内存单元的个数)(内存数据的输出格式)(一个内存单元的大小) 数组名/指针/地址值...
````

该指令会从`数组名/指针/地址值..`开始，向后以`(内存数据的输出格式)`，展示`(内存单元的个数) * (一个内存单元的大小)`的内存数据。

其中：

内存单元的个数，直接输入一个整数即可。

内存数据的输出格式有：

1. o(octal)，八进制整数
2. x(hex)，十六进制整数
3. d(decimal)，十进制整数
4. u(unsigned decimal)，无符号整数
5. t(binary)，二进制整数
6. f(float)，浮点数
7. c(char)，字符
8. a(address)，地址值
9. c(character)：字符
10. s(string)，字符串

一个内存单元的大小的表示，有以下格式：

1. b(byte)，一个字节
2. h(halfword,  2 bytes)，二个字节
3. w(word, 4 bytes)，四个字节
4. g(giant, 8 bytes)，八个字节


# Coredump文件
通常情况下，程序异常终止时，会产生 Coredump 文件。Coredump 文件类似飞机上的"黑匣子"，它会保留程序"失事"瞬间的一些信息，通常包含寄存器的状态、栈调用情况等。

1. 如果代码压根跑不起来，或者跑起来就会直接崩溃，可以利用<font color=red>**Core文件**</font>进行辅助调试


3. 在实际的生产环境中，可能无法直接复现报错情况(或者很麻烦)。那么程序报错了，只能通过<font color=red>**Core文件**</font>来检测程序的错误信息，进而修正代码。

```bash
gdb hello core_hello_1679196427		# gdb + 可执行文件的名字 + core文件名
```