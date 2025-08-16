---
title: LCA
excerpt: 最近公共祖先节点和树上权值路径和
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-05-18 14:55:45
---
<!-- more -->
# LCA-树上倍增（求最近公共祖先和路径权值和）[^1]
>树上节点值范围为$[0,n-1]$

## 求最近公共祖先节点

`u`和`v`的最近公共祖先求法:
1. 将`u`和`v`调整到统一高度
2. `u`和`v`同时向上跳
3. `u`和`v`相遇的第一个节点即为最近公共祖先节点

使用倍增的方法加快上述过程2和3。

## 利用前缀和求路径权值和

要求`u`到`v`的树上路径权值和，先求`u`和`v`的最近公共祖先，那么$$u到v的路径权值和=u到根节点的路径权值和+v到根节点的路径权值和-2*最近公共祖先节点到根节点的路径权值和$$

- `fa[i][k]`表示节点`i`的$2^k$级祖先
- `cost[i]`表示节点`i`到根节点的权值和
- `depth[i]`表示节点`i`的深度


``` C++
    int n = edges.size() + 1;
    vector neibor(n, vector<pair<int, int>>());
    for (auto &e : edges)
    {
        int i = e[0], j = e[1], w = e[2];
        neibor[i].emplace_back(j, w);
        neibor[j].emplace_back(i, w);
    }

    const int MX = bit_width(n - 1);
    vector fa(n, vector(MX + 1, 0));
    vector cost(n, 0);
    vector depth(n, 0);

    [&](this auto &&dfs, int u, int father) -> void
    {
        for (auto &[v, w] : neibor[u])
        {
            if (v == father)
            {
                continue;
            }
            depth[v] = depth[u] + 1;
            fa[v][0] = u;
            cost[v] = cost[u] + w;
            for (int k = 1; k <= MX; k++)
            {
                fa[v][k] = fa[fa[v][k - 1]][k - 1];
            }
            dfs(v, u);
        }
    }(1, 0);

    auto lca = [&](int u, int v) -> int
    {
        if (depth[u] > depth[v])
        {
            swap(u, v);
        };
        int dist = depth[v] - depth[u];
        for (int k = 0; dist; ++k, dist >>= 1)
        {
            if (dist & 1)
            {
                v = fa[v][k];
            }
        }
        if (u == v)
        {
            return u;
        }
        for (int k = MX; ~k; k--)
        {
            if (fa[u][k] != fa[v][k])
            {
                u = fa[u][k];
                v = fa[v][k];
            }
        }
        return fa[u][0];
    };

    auto cal_cost = [&](int u, int v) -> int
    {
        int x=lca(u,v);
        return cost[u]+cost[v]-2*cost[x];
    };

```

## 直接求路径权值和

- `fa[i][k]`表示节点`i`的$2^k$级祖先
- `cost[i][k]`表示节点`i`到$2^k$级祖先的权值和
- `depth[i]`表示节点`i`的深度

该方法比前一种空间复杂度更高。
``` C++
    int n = edges.size() + 1;
    vector neibor(n, vector<pair<int, int>>());
    for (auto &e : edges)
    {
        int i = e[0], j = e[1], w = e[2];
        neibor[i].emplace_back(j, w);
        neibor[j].emplace_back(i, w);
    }

    const int MX = bit_width(n - 1);
    vector fa(n, vector(MX + 1, 0));
    vector cost(n, vector(MX + 1, 0));
    vector depth(n, 0);

    [&](this auto &&dfs, int u, int father) -> void
    {
        for (auto &[v, w] : neibor[u])
        {
            if (v == father)
            {
                continue;
            }
            depth[v] = depth[u] + 1;
            fa[v][0] = u;
            cost[v][0] = w;
            for (int k = 1; k <= MX; k++)
            {
                fa[v][k] = fa[fa[v][k - 1]][k - 1];
                cost[v][k] = cost[fa[v][k - 1]][k - 1] + cost[v][k - 1];
            }
            dfs(v, u);
        }
    }(1, 0);

    auto lca = [&](int u, int v) -> int
    {
        if (depth[u] > depth[v])
        {
            swap(u, v);
        };
        int dist = depth[v] - depth[u];
        for (int k = 0; dist; ++k, dist >>= 1)
        {
            if (dist & 1)
            {
                v = fa[v][k];
            }
        }
        if (u == v)
        {
            return u;
        }
        for (int k = MX; ~k; k--)
        {
            if (fa[u][k] != fa[v][k])
            {
                u = fa[u][k];
                v = fa[v][k];
            }
        }
        return fa[u][0];
    };

    auto cal_cost = [&](int u, int v) -> int
    {
        if (depth[u] > depth[v])
        {
            swap(u, v);
        };
        int dist = depth[v] - depth[u], ans = 0;
        for (int k = 0; dist; ++k, dist >>= 1)
        {
            if (dist & 1)
            {
                ans += cost[v][k];
                v = fa[v][k];
            }
        }
        if (u == v)
        {
            return ans;
        }
        for (int k = MX; ~k; k--)
        {
            if (fa[u][k] != fa[v][k])
            {
                ans += cost[u][k] + cost[v][k];
                u = fa[u][k];
                v = fa[v][k];
            }
        }
        ans += cost[u][0] + cost[v][0];
        return ans;
    };
```

[^1]:[OI-LCA](https://oi-wiki.org/graph/lca/)