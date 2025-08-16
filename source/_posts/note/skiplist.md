---
title: 跳表
excerpt: 跳表的设计与实现
layout: note
tags:
  - code
categories:
  - - note
date: 2025-04-14 09:11:52
index_img: https://pic.leetcode.cn/1702370216-mKQcTt-1506_skiplist.gif
---
## 介绍
跳表是一种有序的数据结构，支持 $log(n)$ 时间复杂度的查询、插入和删除。在节点增加跳跃长度信息，还可以支持 $log(n)$ 时间复杂度的查询第 $k$ 小/大元素（这一点C++中 STL 的 `set` 和 `map` 并不支持）。

跳表与其他有序数据结构（例如AVL树，红黑树）相比，实现原理简单，随机化情况下的时间复杂度优秀，redis底层的存储结构就是跳表。

## 概要
跳表是在普通的单链表的基础上进行改进而来的，跳表的操作和普通单链表十分相似。在普通单链表中查询元素需要遍历所有元素，因此时间复杂度的$O(n)$。跳表查询元素时不需要遍历所有的元素，跳表会跳过大量不可能的元素。

跳表空间复杂度为O(n);

详细证明参考OI-wiki[^2]和leetcode的官方解[^1];

跳表节点结构定义如下:
``` C++
template <typename K, typename V>
struct SkiplistNode
{
    K key;
    V value;
    vector<SkiplistNode *> forward;
    SkiplistNode(const K &key = K(), const V &value = V(), uint MAX_LEVEL = 0) : key(key), value(value), forward(MAX_LEVEL, nullptr) {}
};
```

## 查询
假设升序排列
- 从最顶层的单链表进行遍历，找到最后一个小于target的节点，然后跳到下一层。
- 到第0层时，最后得到的节点就是最后一个小于target的节点。
- 判断得到的节点的下一个节点是否为所找的节点。

{% raw %}
<div class="responsive-iframe-container">
  <iframe src="https://docs.google.com/presentation/d/1NZnOopBchSyxx2PbfQ15di2SWgyYniPWGPV1dFkzR3Q/embed?start=false&loop=false&delayms=3000" frameborder="0" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>
</div>

<style>
.responsive-iframe-container {
  position: relative;
  overflow: hidden;
  width: 100%;
  padding-top: 56.25%; /* 16:9 宽高比 (计算方式：9 / 16 = 0.5625) */
}
.responsive-iframe-container iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: none;
}
</style>

## 插入
假设升序排列
- 随机得到所插入的元素最大的层数。
- 从最顶层的单链表进行遍历，找到每一层最后一个小于target的节点，记录小于等于最大层数的每一层的第一个大于等于target的节点，然后跳到下一层。
- 从下往上连接插入节点的前驱和后继节点。

{% raw %}
<div class="responsive-iframe-container">
  <iframe src="https://docs.google.com/presentation/d/1FvIzF3ORmNUltmljvYkN_lTOO1rULOhIFagPiPiaWBs/embed?start=false&loop=false&delayms=3000" frameborder="0" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>
</div>

<style>
.responsive-iframe-container {
  position: relative;
  overflow: hidden;
  width: 100%;
  padding-top: 56.25%; /* 16:9 宽高比 (计算方式：9 / 16 = 0.5625) */
}
.responsive-iframe-container iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: none;
}
</style>

## 删除
假设升序排列
- 从最顶层的单链表进行遍历，找到每一层最后一个小于target的节点，然后跳到下一层。
- 从下往上连接被删除节点的前驱和后继节点。

{% raw %}
<div class="responsive-iframe-container">
  <iframe src="https://docs.google.com/presentation/d/19opBzXC-Zzmk6BU2P7Oo2ef7-LdAMadS02fXyNFceZ4/embed?start=false&loop=false&delayms=3000" frameborder="0" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>
</div>

<style>
.responsive-iframe-container {
  position: relative;
  overflow: hidden;
  width: 100%;
  padding-top: 56.25%; /* 16:9 宽高比 (计算方式：9 / 16 = 0.5625) */
}
.responsive-iframe-container iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: none;
}
</style>

## 实现
<!-- more -->
``` C++
#include <vector>
#include <random>

using std::max;
using std::min;
using std::mt19937;
using std::random_device;
using std::uniform_int_distribution;
using std::vector;
using uint = unsigned int;

template <typename K, typename V>
struct SkiplistNode
{
    K key;
    V value;
    vector<SkiplistNode *> forward;
    SkiplistNode(const K &key = K(), const V &value = V(), uint MAX_LEVEL = 0) : key(key), value(value), forward(MAX_LEVEL, nullptr) {}
};

template <typename K, typename V, typename Compare = std::less<K>>
class Skiplist
{
private:
    using Node = SkiplistNode<K, V>;
    static constexpr const uint MAX_LEVEL = 32;
    static constexpr const uint P = 4; // p=0.25
    static constexpr const uint S = 0xFFFFFFFF;
    static constexpr const uint PS = S / P;
    mt19937 mt;
    uniform_int_distribution<uint> rd;
    Node *dummy_head;
    uint level;
    Compare cmp;

public:
    Skiplist() : level(1), mt(random_device()()), rd(0, S)
    {
        dummy_head = new Node(K(), V(), MAX_LEVEL);
    }

    bool equal(const K &k1, const K &k2) const
    {
        return cmp(k1, k2) == cmp(k2, k1);
    }

    Node *find(const K &key)
    {
        Node *curr = dummy_head;
        for (uint i = level - 1; ~i; --i)
        {
            while (curr->forward[i] && cmp(curr->forward[i]->key, key))
            {
                curr = curr->forward[i];
            }
        }
        curr = curr->forward[0];
        if (curr && equal(curr->key, key))
        {
            return curr;
        }
        return nullptr;
    }

    V &insert(const K &key, const V &value)
    {
        uint lv = randomLevel();
        vector<Node *> update(lv, nullptr);
        Node *curr = dummy_head;
        for (uint i = level - 1; ~i; --i)
        {
            while (curr->forward[i] && cmp(curr->forward[i]->key, key))
            {
                curr = curr->forward[i];
            }
            if (i < lv)
            {
                update[i] = curr;
            }
        }

        Node *node = new Node(key, value, lv);

        for (uint i = 0; i < min(level, lv); ++i)
        {
            node->forward[i] = update[i]->forward[i];
            update[i]->forward[i] = node;
        }

        level = max(level, lv);
        return node->value;
    }

    bool erase(const K &key)
    {
        vector<Node *> update(MAX_LEVEL, nullptr);
        Node *curr = dummy_head;
        for (uint i = level - 1; ~i; --i)
        {
            while (curr->forward[i] && cmp(curr->forward[i]->key, key))
            {
                curr = curr->forward[i];
            }
            update[i] = curr;
        }

        if (curr->forward[0] == nullptr || !equal(curr->forward[0]->key, key))
        {
            return false;
        }

        curr = curr->forward[0];
        for (uint i = 0; i < curr->forward.size(); ++i)
        {
            update[i]->forward[i] = curr->forward[i];
        }

        delete curr;

        return true;
    }

    uint randomLevel()
    {
        uint lv = 1;
        while (rd(mt) <= PS)
        {
            ++lv;
        }
        return min(lv, MAX_LEVEL);
    }
};
```
[^1]: [leetcode-设计跳表](https://leetcode.cn/problems/design-skiplist/)
[^2]: [OI-wiki](https://oi-wiki.org/ds/skiplist/)