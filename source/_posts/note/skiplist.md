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
# 跳表
![img](https://pic.leetcode.cn/1702370216-mKQcTt-1506_skiplist.gif "跳表示意图")
## 跳表实现
<!-- more -->
``` C++
struct SkiplistNode{
    int val;
    vector<SkiplistNode*> forward;
    SkiplistNode(int val,int MAX_LEVEL):val(val),forward(MAX_LEVEL,nullptr){}

};

class Skiplist {
private:
    static constexpr const int MAX_LEVEL = 32;
    static constexpr const int P = 4; // p=0.25
    static constexpr const int S = 0xFFFF;
    static constexpr const int PS = S/P;
    static constexpr const int INVALID = INT_MAX;
    SkiplistNode *dummy_head;
    int level;
public:
    Skiplist():level(1) {
        dummy_head = new SkiplistNode(INVALID,MAX_LEVEL);

    }
    
    bool search(int target) {
        SkiplistNode * curr = dummy_head;
        for(int i=level-1;i>=0;i--){
            while(curr->forward[i]&&curr->forward[i]->val<target){
                curr=curr->forward[i];
            }
        }
        curr=curr->forward[0];
        if(curr&&curr->val==target){
            return true;
        }
        return false;
    }
    
    void add(int num) {
        int lv=randomLevel();
        vector<SkiplistNode*> update(lv,nullptr);
        SkiplistNode *curr = dummy_head;
        for(int i=level-1;i>=0;i--){
            while(curr->forward[i]&&curr->forward[i]->val<num){
                curr=curr->forward[i];
            }
            if(i<lv){
                update[i]=curr;
            }
        }

        // i表示层数
        SkiplistNode *node = new SkiplistNode(num,lv);

        for(int i=0;i<min(level,lv);i++){
            node->forward[i] = update[i]->forward[i];
            update[i]->forward[i]=node;
        }

        level=max(level,lv);
    }
    
    bool erase(int num) {
        vector<SkiplistNode*> update(MAX_LEVEL,nullptr);
        SkiplistNode *curr = dummy_head;
        for(int i=level-1;i>=0;i--){
            while(curr->forward[i]&&curr->forward[i]->val<num){
                curr=curr->forward[i];
            }
            update[i]=curr;
        }

        if(curr->forward[0]==nullptr||curr->forward[0]->val!=num){
            return false;
        }

        curr=curr->forward[0];
        // i表示层数
        for(int i=0;i<curr->forward.size();i++){
            update[i]->forward[i]=curr->forward[i];
        }

        delete curr;

        return true;
    }

    int randomLevel(){
        int lv=1;
        while(rand()%S<PS){++lv;}

        return min(lv,MAX_LEVEL);
    }
};
```
[^1]: https://leetcode.cn/problems/design-skiplist/