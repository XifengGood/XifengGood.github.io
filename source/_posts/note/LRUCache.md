---
title: LRUCache
excerpt: note
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-08-01 22:20:25
---
### 
<!-- more -->
``` C++
#pragma once
#include <list>
#include <unordered_map>
#include <mutex>
#include <memory>

template <typename K, typename V>
class LRUCache
{
private:
    using Cache = std::list<std::pair<K, V>>;
    Cache cache;
    std::unordered_map<K, typename Cache::iterator> um;
    const size_t capacity;
    std::unique_ptr<std::mutex> mtx;

public:
    LRUCache(size_t capacity) : capacity(capacity),mtx{new std::mutex()} {}

    template <typename K1>
    V *get(K1 &&key)
    {
        std::unique_lock<std::mutex> ul{*mtx};
        auto it = um.find(std::forward<K1>(key));
        if (it == um.end())
        {
            return nullptr;
        }
        cache.splice(cache.end(), cache, it->second);
        return &it->second->second;
    }

    template <typename K1, typename V1>
    void put(K1 &&key, V1 &&value)
    {
        if (capacity == 0)
        {
            return;
        }

        std::unique_lock<std::mutex> ul{*mtx};
        auto it = um.find(std::forward<K1>(key));
        if (it == um.end())
        {
            if (cache.size() == capacity)
            {
                um.erase(cache.front().first);
                cache.pop_front();
            }

            auto cache_it = cache.emplace(cache.end(), std::forward<K1>(key), std::forward<V1>(value));
            um.emplace(cache_it->first, cache_it);
        }
        else
        {
            it->second->second = std::forward<V1>(value);
            cache.splice(cache.end(), cache, it->second);
        }
    }
};
```
[^1]: