---
title: ros2
excerpt: note
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-08-06 21:21:53
---
# Core Concept

## colcon

### 1. 基本介绍
colcon（COllection of Linux COmmand Ninjas）是 ROS 2 的官方 构建工具，用于编译、测试和安装 ROS 2 工作空间中的软件包。它替代了 ROS 1 中的 catkin_make 和 catkin build，并支持更灵活的构建流程。

### 2. 基本使用方法
#### (1) 创建 ROS 2 工作空间
```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
```
#### (2) 下载 ROS 2 包（如示例包）
```bash
git clone https://github.com/ros2/examples src/examples
```
#### (3) 编译
```bash
colcon build
--symlink-install（开发时推荐，避免重复安装）：
```

```bash
colcon build --symlink-install
```
- 指定包编译（只编译某个包）：

```bash
colcon build --packages-select <package_name>
```
- 并行编译（加快速度）：

```bash
colcon build --parallel-workers 4
```
#### (4) 加载环境
```bash
source install/setup.bash  # 或 .zsh/.fish
```

### 常用指令
``` shell
colcon build	编译所有包
colcon build --packages-select <pkg>	只编译指定包
colcon test	运行测试
colcon list	列出所有包
colcon clean	清理构建文件
colcon graph	查看包依赖关系
```

## node
``` shell
ros2 node info <node_name>
ros2 node list <node_name>
```
## topic
``` shell
ros2 topic bw <topic_name>
ros2 topic delay <topic_name>
ros2 topic echo <topic_name>
ros2 topic find <topic_type>
ros2 topic hz <topic_name>
ros2 topic info <topic_name>
ros2 topic list
ros2 topic pub <topic_name> <message_type>
ros2 topic type <topic_name>
```
## service
``` shell
ros2 service list -t
ros2 service info <service_name>
ros2 service type <service_name>
ros2 service find <service_type>
ros2 service call <service_name> <service_type> <arguments>
ros2 service echo <service_name | service_type> <arguments>
ros2 interface show <type_name>
```
## parameter

## action
``` shell
ros2 action list -t
```

# Client libraries
## workspace

## package
创建一个package
``` shell
ros2 pkg create --build-type ament_cmake --license Apache-2.0 --node-name my_node my_package
```

使用colcon构建package
``` shell
colcon build --packages-select my_package
```

## service

# ycm补全ros2

## 1. 确保 YouCompleteMe 已正确安装
YCM 需要基于 `clangd`（或 `libclang`）提供 C++ 补全功能。
👉 安装方式：
``` bash
# 如果还没有安装 YCM，先安装（Vim-Plug 示例）
git clone https://github.com/ycm-core/YouCompleteMe.git ~/.vim/plugged/YouCompleteMe
cd ~/.vim/plugged/YouCompleteMe
git submodule update --init --recursive
python3 install.py --clangd-completer  # 确保启用 clangd 支持
```

## 2. 为 ROS 2 生成编译数据库（compile_commands.json）
YCM 需要 `compile_commands.json` 来解析代码结构。ROS 2 使用 `colcon` 构建，可以自动生成该文件。

1. 安装 `colcon` 并启用编译数据库
``` bash
sudo apt install python3-colcon-common-extensions
```

2. 在 ROS 2 工作空间启用 `compile_commands.json`
``` bash
cd ~/ros2_ws  # 进入你的 ROS 2 工作空间
colcon build --packages-select <package_name> --symlink-install --cmake-args -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```
`--symlink-install` 可避免重复编译
`-DCMAKE_EXPORT_COMPILE_COMMANDS=ON` 会生成 `compile_commands.json`

3. 合并所有包的 `compile_commands.json`（可选）
ROS 2 每个包单独生成 `compile_commands.json`，可以用 `compdb` 合并：
``` bash
sudo apt install python3-pip
pip install compdb
compdb -p build list > compile_commands.json  # 合并所有包的编译数据库
```

> If using `CMake`, add `-DCMAKE_EXPORT_COMPILE_COMMANDS=ON` when configuring (or add `set( CMAKE_EXPORT_COMPILE_COMMANDS ON )` to `CMakeLists.txt`) and copy

## 3. 配置 YCM 识别 ROS 2 头文件
YCM 默认可能找不到 ROS 2 的头文件（如 `rclcpp.hpp`），需要配置 `.ycm_extra_conf.py`。

1. 创建或修改 `~/.vim/.ycm_extra_conf.py`
``` python
import os
import ycm_core

def DirectoryOfThisScript():
    return os.path.dirname(os.path.abspath(__file__))

def Settings(**kwargs):
    return {
        'flags': [
            '-x', 'c++',
            '-std=c++17',  # ROS 2 默认使用 C++17
            '-I', '/opt/ros/<ros2_distro>/include',  # 替换 <ros2_distro>，如 "humble"
            '-I', os.path.join(DirectoryOfThisScript(), 'src'),
            '-I', '/usr/include',
            '-I', '/usr/include/eigen3',
            '-Wall',
            '-Wextra',
            '-Werror',
        ],
        'override_filename': None,
    }
```
👉 替换 `<ros2_distro>` 为你的 ROS 2 版本（如 `humble`、`foxy`）。

2. 让 YCM 自动加载配置
在 `~/.vimrc` 中添加：
``` vim
let g:ycm_global_ycm_extra_conf = '~/.vim/.ycm_extra_conf.py'
let g:ycm_confirm_extra_conf = 0  " 避免每次询问是否加载配置
```

<!-- more -->