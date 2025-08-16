---
title: RMQ
excerpt: note
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-08-04 21:55:08
---
# 莫队

## 滚动莫队

### leetcode 3636.查询超过阈值频率最高元素[^1]
```C++
class Solution {
public:
    vector<int> subarrayMajority(vector<int>& nums,
                                 vector<vector<int>>& queries) {
        uint n = nums.size();
        uint q = queries.size();
        vector<int> ans(q);

        // 离散化
        auto a = nums;
        ranges::sort(a);
        a.erase(unique(a.begin(), a.end()), a.end());
        vector<uint> index_to_value(n);
        for (uint i = 0; i < n; ++i) {
            index_to_value[i] = ranges::lower_bound(a, nums[i]) - a.begin();
        }

        // 莫队
        uint blockSize = ceil(n / sqrt(q));
        uint blockNum = (n - 1) / blockSize + 1;

        // l,r,threshold,i
        vector<vector<tuple<uint, uint, uint, uint>>> blocks(blockNum);
        for (uint i = 0; i < q; ++i) {
            auto& _q = queries[i];
            blocks[_q[0] / blockSize].emplace_back(_q[0], _q[1], _q[2], i);
        }

        vector<uint> cnt(a.size());
        uint minx = -1, maxcnt = 0;

        auto add = [&](uint j) {
            uint x = nums[j];
            uint c = ++cnt[index_to_value[j]];
            if (c > maxcnt) {
                maxcnt = c;
                minx = x;
            } else if (c == maxcnt) {
                minx = min(minx, x);
            }
        };

        for (int blockId = 0; blockId < blockNum; ++blockId) {
            auto& block = blocks[blockId];
            if (block.empty()) {
                continue;
            }

            ranges::sort(block, {}, [](auto& ele) { return get<1>(ele); });

            uint R = (blockId + 1) * blockSize;
            uint _r = R;
            for (auto& [l, r, threshold, i] : block) {
                if (blockId == r / blockSize) {
                    uint prev_minx = minx, prev_maxcnt = maxcnt;
                    for (uint j = l; j <= r; ++j) {
                        add(j);
                    }
                    ans[i] = maxcnt >= threshold ? minx : -1;

                    // 回滚
                    minx = prev_minx, maxcnt = prev_maxcnt;
                    for (uint j = l; j <= r; ++j) {
                        --cnt[index_to_value[j]];
                    }
                } else {
                    while (_r <= r) {
                        add(_r++);
                    }
                    uint prev_minx = minx, prev_maxcnt = maxcnt;
                    for (uint j = l; j < R; ++j) {
                        add(j);
                    }
                    ans[i] = maxcnt >= threshold ? minx : -1;

                    // 回滚
                    minx = prev_minx, maxcnt = prev_maxcnt;
                    for (uint j = l; j < R; ++j) {
                        --cnt[index_to_value[j]];
                    }
                }
            }

            // 回滚
            minx = -1, maxcnt = 0;
            for (uint j = R; j < _r; ++j) {
                --cnt[index_to_value[j]];
            }
        }

        return ans;
    }
};
```
[^1]:[3636.查询超过阈值频率最高元素]("https://leetcode.cn/problems/threshold-majority-queries/description/")