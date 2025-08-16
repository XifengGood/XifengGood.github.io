---
title: 并查集 UnionFind
excerpt: 并查集模板
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-05-18 15:09:58
---
### 并查集UnionFind模板
<!-- more -->
节点值范围: $[0,n-1]$
``` C++
class UnionFind{
    vector<int> pa;
    vector<int> sz;
public:
    int cc;
    UnionFind(int n):pa(n),sz(n,1),cc(n){
        ranges::iota(pa,0);
    }
    int find(int x){
        return pa[x]==x?x:pa[x]=find(pa[x]);
    }
    void merge(int x,int y){
        x=find(x),y=find(y);
        if(x==y){
            return ;
        }
        if(sz[x]>sz[y]){
            swap(x,y);
        }
        pa[x]=y;
        sz[y]+=sz[x];
        sz[x]=sz[y];
        --cc;
    }
    bool isSame(int x,int y){
        return find(x)==find(y);
    }
};
```

[^1]:[OI-Wiki](https://oi-wiki.org/ds/dsu/)