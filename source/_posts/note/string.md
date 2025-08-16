---
title: 字符串算法
excerpt: 字符串相关算法
layout: note
tags:
  - code
categories:
  - - note
date: 2025-04-27 15:55:02
index_img: /img/code.webp
---

# 字符串匹配

## KMP[^1]

真前缀匹配算法。

数组`pi[i]`表示下标`i`结束的子串能匹配的最大相同前缀。

``` C++
vector<int> prefix_function(const string &s){
	int n=s.length();
	vector<int> pi(n);
	for(int i=1;i<n;i++){
		int j=pi[i-1];
		while(j>0&&s[i]!=s[j]){
			j=pi[j-1];
		}
		if(s[i]==s[j]){
			j++;
		}
		pi[i]=j;
	}
	return pi;
}

vector<int> KMP(const string &s,const string &p) {
	return prefix_function(p+"#"+s);
}

```

## Z函数(拓展KMP)[^2]

该方法可以算KMP的拓展。

数组`z[i]`表示下标`i`开始的子串能匹配的最大相同前缀。

``` C++
vector<int> z_function(const string & s) {
	int n=s.length();
	vector<int> z(n);
	for(int i=1,l=0,r=0;i<n;i++){
		if(i<=r&&z[i-l]<r-i+1){
			z[i]=z[i-l];
		}else{
			z[i]=max(0,r-i+1);
			while(i+z[i]<n&&s[z[i]]==s[i+z[i]]){
				++z[i];
			}
		}
		if(i+z[i]-1>r){
			l=i,r=i+z[i]-1;
		}
	}
	return z;
}

vector<int> Z_Funtion(const string & s,const string & p){
	return z_function(p+"#"+s);
}
```

## Manacher (马拉车)

该方法可以实现$O(n)$的时间复杂度求出字符串所有的回文子串。

主要思想:
 - 数组`d[i]`表示以`i`为中心最大回文子串长度
 - 数组`d1[i]`长度为奇数的数组`d[i]`
 - 数组`d2[i]`长度为偶数的数组`d[i]`
 - 显然`d1[i]`和`d2[i]`就是问题的解


[^1]:[KMP](https://oi-wiki.org/string/kmp/)
[^2]:[Z-Function](https://oi-wiki.org/string/z-func/)