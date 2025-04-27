---
title: 动态规划DP
date: 2025-04-14 08:36:38
layout: note
tags:
  - code
categories:
  - - note
excerpt: DP的总结与模板
index_img: /img/code.webp
math: true
---
## 状态压缩DP

只关心已经枚举的集合，不关心具体的排列。

LeetCode 526.优美的排列[^1]
``` C++
int countArrangement(int n) {
    vector memo(1<<n,-1);
    auto dfs=[&n,&memo](this auto&&dfs,uint S)->int{
        if(~memo[S]){
            return memo[S];
        }
        int i=popcount(S);
        if(i==n){
            return 1;
        }
        i++;
        int res=0;
        for(int j=1;j<=n;j++){
            if(S>>(j-1)&1){
                continue;
            }
            if(i%j==0||j%i==0){
                res+=dfs(S|(1<<(j-1)));
            }
        }
        return memo[S]=res;
    };
    return dfs(0);

    // 迭代
    // vector f(1<<n,0);
    // f[0]=1;
    // for(uint S=1;S<(1<<n);S++){
    //     int i=popcount(S);
    //     for(uint j=1;j<=n;j++){
    //         if((S>>(j-1)&1)&&i%j==0||j%i==0){
    //             f[S]+=f[S^(1<<(j-1))];
    //         }
    //     }
    // }

    // return f.back();
}

```

类似题目
 - LeetCode 3530.有向无环图中合法拓扑排序的最大利润[^2]
 - LeetCode 3533.判断连接可除性[^3]


## 数位DP

枚举每一位上可能的数字，因为最终有多个重复的状态，所以可以记忆化搜索结果，比直接枚举要快很多。


时间复杂度=状态数量×状态计算时间。
空间复杂度=状态数量。
时间复杂度:
$$O(nD)$$
n是数字的位数，D为数字的进制数。

### 上界模板
``` C++
int count(int high) {
    string s=to_string(high);

    vector memo(s.length(),-1);
    auto dfs=[&](this auto&&dfs,int i,bool isLimit)->int{
        if(i==s.length()){
            return 1;
        }

        if(!isLimit&&memo[i]>=0){
            return memo[i];
        }

        int top = isLimit?s[i]-'0':9;
        int res=0;
        for(int x=0;x<=top;x++){
            res+=dfs(i+1,isLimit&&x==top);
        }

        if(!isLimit){
            return memo[i]=res;
        }

        return res;

    };

    return dfs(0,true);
}
```
下界含0和不含0，最终结果需要另外判断。

### 上下界模板

``` C++
int count(int high, int low) {
    string high_s=to_string(high);
    string low_s=to_string(low);
    int n=high.length();
    low_s=string(n-low_s.length(),'0').append(low_s);

    vector memo(n+1-1);

    auto dfs=[&](this auto&&dfs,int i,bool limit_low,bool limit_high)->int{

        if(i==n){
            return 1;
        }

        if(!limit_low&&!limit_high&&memo[i]>=0){
            return memo[i];
        }

        int low=limit_low?high_s[i]-'0':0;
        int high=limit_high?low_s[i]-'0':9;

        int res=0;

        for(int x=low;x<=high&&s+x<=max_sum;x++){
            res+=dfs(i+1,limit_low&&x==low,limit_high&&x==high);
        }

        if(!limit_low&&!limit_high){
            return memo[i]=res;
        }

        return res;
    };

    return dfs(0,true,true);
}
```

对应的模板还可以添加isNum和mask参数，记录当前是否填了数字和填过哪些数字。

[^1]:[LeetCode 526.美丽的排列](https://leetcode.cn/problems/beautiful-arrangement/)
[^2]:[LeetCode 3530.有向无环图中合法拓扑排序的最大利润](https://leetcode.cn/problems/maximum-profit-from-valid-topological-order-in-dag)
[^3]:[LeetCode 3533.判断连接可除性](https://leetcode.cn/problems/concatenated-divisibility)