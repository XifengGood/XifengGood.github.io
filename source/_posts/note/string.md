---
title: 字符串算法
excerpt: 字符串的模板
layout: note
tags:
  - code
categories:
  - - note
date: 2025-04-27 15:55:02
index_img: /img/code.webp
---

## 字符串匹配

### KMP[^1]
``` C++
vector<int> prefix_function(string s){
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

vector<int> KMP(string s, string p) {
	return pi=prefix_function(p+"#"+s);
}

```

### Z函数(拓展KMP)[^1]
``` C++
vector<int> z_function(string s) {
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

vector<int> Z_Funtion(string s,string p){
	return z_function(p+"#"+s);
}
```
[^1]:[KMP](https://oi-wiki.org/string/kmp/)
[^2]:[Z-Function](https://oi-wiki.org/string/z-func/)