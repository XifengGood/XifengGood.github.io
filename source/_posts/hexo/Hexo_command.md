---
title: Hexo 指令
date: 2025-07-08 20:00:46
tags:
excerpt: Hexo 指令小记
---
# 创建新页面

``` bash
hexo new [layout] <title>
```

|Option |Description|
|---    |---        |
|-p, --path     |   Post path. Customize the path of the post.  |
|-r, --replace  |   Replace the current post if existed.       |
|-s, --slug     |   Post slug. Customize the URL of the post.   |

``` bash
hexo new page --path about/me "About me"
```
