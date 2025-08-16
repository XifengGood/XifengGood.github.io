---
title: 树状数组
excerpt: 树状数组
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-05-18 15:16:18
---
<!-- more -->
### 树状数组（支持单点修改+区间查询）

``` C++
class Fenwick{
    vector<int> s;
    int n;
public:
    Fenwick(int n):n(n),s(n+1){}

    int lowbit(int x){
        return x&(-x);
    }
    void add(int i){
        while(i<=n){
            s[i]++;
            i+=lowbit(i);
        }
    }
    int pre(int i){
        int res=0;
        while(i>0){
            res+=s[i];
            i-=lowbit(i);
        }
        return res;
    }
    int query(int l,int r){
        return pre(r)-pre(l-1);
    }
};
```
[^1]:[OI-Wiki-fenwick](https://oi-wiki.org/ds/fenwick/)