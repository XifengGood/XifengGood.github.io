---
title: SegmentTree 线段树
excerpt: note
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-06-17 20:29:01
---
### 
<!-- more -->
## 单点修改，区间查询
``` C++
#include <vector>
#include <bit>

using std::vector;
using std::bit_width;

template <typename T>
class SegmentTree
{
    int n;

    vector<T> tree;

    T merge_val(T a, T b) const
    {
        return a + b;
    }

    void maintain(int i)
    {
        tree[i] = merge_val(tree[i << 1], tree[(i << 1) + 1]);
    }

    void build(vector<T> &a, int l, int r, int i)
    {
        if (l == r)
        {
            tree[i] = a[l];
            return;
        }
        int m = l + ((r - l) >> 1);
        build(a, l, m, i << 1);
        build(a, m + 1, r, (i << 1) + 1);
        maintain(i);
    }

    T query(int l, int r, int i, int ql, int qr)
    {
        if (ql <= l && r <= qr)
        {
            return tree[i];
        }
        int m = l + ((r - l) >> 1);
        if (ql > m)
        {
            return query(m + 1, r, (i << 1) + 1, ql, qr);
        }
        else if (qr < m + 1)
        {
            return query(l, m, i << 1, ql, qr);
        }
        T l_res = query(l, m, i << 1, ql, qr);
        T r_res = query(m + 1, r, (i << 1) + 1, ql, qr);
        return merge_val(l_res, r_res);
    }

    void update(int l, int r, int i, int index, T v)
    {
        if (l == r)
        {
            tree[i] = v;
            return;
        }
        int m = l + ((r - l) >> 1);
        if (index <= m)
        {
            update(l, m, i << 1, index, v);
        }
        else
        {
            update(m + 1, r, (i << 1) + 1, index, v);
        }
        maintain(i);
    }

public:
    SegmentTree(vector<T> &a) : n(a.size()), tree(2 << bit_width(a.size() - 1))
    {
        build(a, 0, n - 1, 1);
    }

    T querySum(int l, int r)
    {
        return query(0, n - 1, 1, l, r);
    }

    void update(int index, T v)
    {
        update(0, n - 1, 1, index, v);
    }
};
```

## 区间修改+区间查询（懒修改）

``` C++
#include <vector>
#include <bit>

using std::bit_width;
using std::vector;

template <typename T, typename F>
class SegmentTreeLazy
{
    int n;

    const int TODO_INIT = 0;

    struct Node
    {
        T val;
        F todo;
    };

    vector<Node> tree;

    T merge_val(T a, T b) const
    {
        return a + b;
    }

    F merge_todo(F todo_a, F todo_b) const
    {
        return todo_a + todo_b;
    }

    void maintain(int i)
    {
        tree[i].val = merge_val(tree[i << 1].val, tree[(i << 1) + 1].val);
    }

    void apply(int l, int r, int i, F todo)
    {
        auto &node = tree[i];
        node.val += (r - l + 1) * todo;
        node.todo = merge_todo(node.todo, todo);
    }

    void spread(int l, int r, int i)
    {
        auto &node = tree[i];
        if (node.todo == TODO_INIT)
        {
            return;
        }
        int m = l + ((r - l) >> 1);
        apply(l, m, i << 1, node.todo);
        apply(m + 1, r, (i << 1) + 1, node.todo);
        node.todo = TODO_INIT;
    }

    void build(vector<T> &a, int l, int r, int i)
    {
        if (l == r)
        {
            tree[i].val = a[l];
            return;
        }
        int m = l + ((r - l) >> 1);
        build(a, l, m, i << 1);
        build(a, m + 1, r, (i << 1) + 1);
        maintain(i);
    }

    T query(int l, int r, int i, int ql, int qr)
    {
        if (ql <= l && r <= qr)
        {
            return tree[i].val;
        }
        spread(l, r, i);
        int m = l + ((r - l) >> 1);
        if (ql > m)
        {
            return query(m + 1, r, (i << 1) + 1, ql, qr);
        }
        else if (qr < m + 1)
        {
            return query(l, m, i << 1, ql, qr);
        }
        T l_res = query(l, m, i << 1, ql, qr);
        T r_res = query(m + 1, r, (i << 1) + 1, ql, qr);
        return merge_val(l_res, r_res);
    }

    void update(int l, int r, int i, int ql, int qr, F v)
    {
        if (ql <= l && r <= qr)
        {
            apply(l, r, i, v);
            return;
        }
        spread(l, r, i);
        int m = l + ((r - l) >> 1);
        if (ql <= m)
        {
            update(l, m, i << 1, ql, qr, v);
        }
        if (qr >= m + 1)
        {
            update(m + 1, r, (i << 1) + 1, ql, qr, v);
        }
        maintain(i);
    }

public:
    SegmentTreeLazy(vector<T> &a) : n(a.size()), tree(2 << bit_width(a.size() - 1))
    {
        build(a, 0, n - 1, 1);
    }

    T querySum(int l, int r)
    {
        return query(0, n - 1, 1, l, r);
    }

    void add(int l, int r, F v)
    {
        update(0, n - 1, 1, l, r, v);
    }
};
```
[^1]:[OI-线段树基础](https://oi-wiki.org/ds/seg/)