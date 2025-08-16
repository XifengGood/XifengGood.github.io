---
title: linux 命令
date: 2025-07-08 19:40:22
tags: linux
layout: note
excerpt: linux 命令小记
categories:
  - - note
---

# 实时查看日志文件的方法
有几种方法可以实时查看不断更新的日志文件内容：

## 1. 使用 tail 命令

最常用的方法是使用 tail 命令加上 -f 参数：

```bash
tail -f /path/to/logfile.log
```

- -f 参数表示"follow"，会持续显示文件新增的内容

- 按 Ctrl+C 停止查看

## 2. 使用 less 命令

```bash
less +F /path/to/logfile.log
```
- 进入 less 后按 F 键可以进入实时跟踪模式

- 按 Ctrl+C 停止跟踪，然后可以按 q 退出

## 3. 使用 multitail 工具
如果需要同时监控多个日志文件：

```bash
multitail /path/to/log1.log /path/to/log2.log
```
- 这是一个专门用于查看多个日志的工具

- 支持颜色高亮、分屏等功能

## 4. 使用 watch 命令
```bash
watch -n 1 "tail -n 20 /path/to/logfile.log"
```
- 每1秒刷新一次，显示最后20行

- 不是真正的实时，但适合某些场景

## 高级用法

> 结合 grep 过滤内容：`tail -f /path/to/logfile.log | grep "error"`

> 显示行号：`tail -f /path/to/logfile.log | cat -n`

> 限制显示行数：`tail -f -n 50 /path/to/logfile.log`


# Linux后台运行程序

## 1. 使用 & 符号
在命令末尾加上 &，程序会在后台运行：

``` bash
command &
```

示例：
``` bash
python3 script.py &
```
此时会返回一个进程 ID（PID），但关闭终端后程序可能会终止

## 2. 使用 nohup（忽略挂断信号）
`nohup` 可以让程序在终端关闭后继续运行：

``` bash
nohup command &
```
输出默认会保存到 `nohup.out` 文件中。

示例：

``` bash
nohup python3 script.py &
```
可以指定输出文件：

```bash
nohup python3 script.py > output.log 2>&1 &
```

`> output.log`：重定向标准输出到文件。

`2>&1`：将标准错误（stderr）也重定向到标准输出（stdout）。

# 3.查看磁盘情况
```bash
df -Th
```

# 4.创建软硬链接
```bash
ln [-s]
```

# 5.改变文件权限
```bash
chmod
```

# 6.改变文件所有者
```bash
chown
```

# 7.抓包
在Linux下进行网络抓包有多种工具和方法，以下是常用的几种方式：

## 1. 使用 `tcpdump`（命令行工具）
`tcpdump` 是最常用的命令行抓包工具，支持过滤和保存抓包数据。

基本用法：
```bash
sudo tcpdump -i eth0 -w capture.pcap
```
`-i eth0`：指定网卡（如 `eth0`、`ens33` 或 `any` 抓所有网卡）。

`-w capture.pcap`：将抓包数据保存到文件（可用Wireshark分析）。

`-n`：禁止IP/端口解析（提升速度）。

`port 80`：只抓80端口的流量（过滤示例）。

示例：
```bash
sudo tcpdump -i any port 22 -n -v  # 抓取SSH流量（22端口）
```
## 2. 使用 `tshark`（Wireshark的命令行版）
`tshark` 是Wireshark的命令行工具，功能强大。

基本用法：
```bash
sudo tshark -i eth0 -w output.pcap
```
`-f "tcp port 80"`：设置抓包过滤表达式。

`-Y "http.request"`：显示过滤（类似Wireshark的过滤语法）。

## 3. 使用 Wireshark（图形化工具）
如果系统有桌面环境，可以安装Wireshark：

```bash
sudo apt install wireshark  # Debian/Ubuntu
sudo yum install wireshark  # CentOS/RHEL
```
启动Wireshark后选择网卡开始抓包。

注意：需用sudo wireshark或配置用户权限（推荐将用户加入wireshark组）。

## 4. 使用 `ngrep`（基于正则的抓包）
`ngrep` 可以按正则表达式匹配抓包内容：

```bash
sudo ngrep -d eth0 "GET|POST" port 80
```
匹配HTTP的GET/POST请求。

## 5. 其他工具
`nmap`：端口扫描时抓包（`nmap -p 80 --packet-trace target`）。

`ss/netstat`：查看当前连接（不抓包，但辅助分析）。

## 常见场景
抓HTTP流量：

```bash
sudo tcpdump -i any -A -s0 port 80 | grep "Host:"
```
抓DNS查询：

```bash
sudo tcpdump -i any port 53 -n
```
抓ICMP（Ping）包：

```bash
sudo tcpdump -i any icmp -n
```
常见场景：
```bash
sudo tcpdump -i ens33 'src 192.168.159.1 and tcp' -v -A
```
`-v`：详细信息
`-A`：显示ASCII码
`-X`：十六进制显示
## 注意事项
- 需要root权限（或用户加入wireshark组）。

- 抓包文件（.pcap）可用Wireshark可视化分析。

- 生产环境中避免长时间抓包（文件过大），建议用-c 100限制包数量。

如果需要更复杂的分析，可以结合过滤表达式或脚本处理抓包数据。