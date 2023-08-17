---
title: Hexo在fluid主题下自定义分类导航
date: 2022-05-01 12:06:58
index_img: /img/hexo.webp
excerpt: 利用Hexo自带的categories来分类导航显示
tags:
- Hexo
categories:
- [note, Hexo]
---
## 前言
想在Hexo中根据建立的文件夹显示页面，但是Hexo只会对_post下的文件进行分类和tag。要想在另外的目录下实现分类显示和页面跳转，需要利用Hexo的API在scripts文件夹下写js，或者简单粗暴，在分类文件夹下，利用Markdown可以Html语法来罗列页面。上面的方法不省时，也可能拖累性能。所有想利用Hexo自带的categories来分类导航显示。
## 创建二级目录
修改主题目录下的_config.yml文件中的menu
```
  menu:
    - { key: "home", link: "/", icon: "iconfont icon-home-fill" }
    - { key: "archive", link: "/archives/", icon: "iconfont icon-archive-fill" }
    - { key: "category", link: "/categories/", icon: "iconfont icon-category-fill" }
    - { key: "tag", link: "/tags/", icon: "iconfont icon-tags-fill" }
    - { key: "note", 
        link: "/note/", 
        submenu: [
          {key: 'LeetCode', link: '/categories/note/LeetCode/'},
        ],
        icon: "iconfont book icon-book" }
    - { key: "about", link: "/about/", icon: "iconfont icon-user-fill" }
    - { key: "links", link: "/links/", icon: "iconfont icon-link-fill" }
```
{% blockquote %}
fluid二级菜单，可见 https://hexo.fluid-dev.com/docs/guide/#%E5%AF%BC%E8%88%AA%E8%8F%9C%E5%8D%95
{% endblockquote %}
新增了一个二级菜单note，一个二级菜单LeetCode，为他设置link，这里是利用hexo的分类功能，跳转到它的分类目录下。
## 设置文章categories
```
---
title: LeetCode 4.寻找两个正序数组的中位数
date: 2022-04-30 19:15:38
layout: code
tags: 
- LeetCode
categories: 
- [note, LeetCode]
---
```
点击新建的二级菜单即可跳转。
{% blockquote %}
tips: hexo s要在hexo g之后才能实现跳转
{% endblockquote %}