---
title: vim+vimplus+ycm
date: 2025-05-23 19:23:07
tags:
 - vim
excerpt: vim安装vimplus和YouCompleteMe
---
<!-- more -->
## 获取最新vim版本

### 从源码编译

进入vim官网 (https://www.vim.org/)

根据官网指示下载vim最新版的源文件进行编译安装

1. 安装python3
``` shell
sudo apt-get install python3-dev 
```

2. 获取vim源码
``` shell
git clone https://github.com/vim/vim.git
cd vim
```

3. 配置vim编译选项（支持python3）
``` shell
./configure --with-features=huge \
            --enable-multibyte \
            --enable-python3interp=yes \
            --with-python3-config-dir=$(python3-config --configdir) \
            --enable-cscope \
            --enable-gui=auto \
            --enable-fontset \
            --enable-largefile \
            --disable-netbeans \
            --enable-fail-if-missing
```
> 编译时需要加上python3支持选项，为了支持后续的ycm。

4. 编译vim
``` shell
make
sudo make install
```

### 安装编译好的vim
``` shell
sudo apt-get install vim-nox
```

## 安装vimplus

vimplus的GitHub仓库 (https://github.com/chxuan/vimplus/)


1. 根据README指示下载vimplus
> vimplus原仓库需要将vimplus里面的.vimrc中的`Plug 'Valloric/YouCompleteMe'` 改为 `Plug 'ycm-core/YouCompleteMe'`

2. 执行安装脚本
vimplus原仓库作者不再维护了，可以使用下面的分仓库(https://github.com/Ro0tk1t/vimplus)

## 安装YouCompleteMe

ycm的Github仓库(https://github.com/ycm-core/YouCompleteMe/)

根据需要添加对应语言的编译选项

### 支持C/C++的代码补全
``` shell
# 彻底删除 YCM 目录
rm -rf ~/.vim/plugged/YouCompleteMe
# 重新打开 Vim 并运行
:PlugInstall
# 然后重新编译
cd ~/.vim/plugged/YouCompleteMe
python3 install.py --clangd-completer
```

# 使用项目数据库进行代码补全
If using CMake, add `-DCMAKE_EXPORT_COMPILE_COMMANDS=ON` when configuring (or add `set( CMAKE_EXPORT_COMPILE_COMMANDS ON )` to `CMakeLists.txt`) and copy or symlink the generated database to the root of your project.