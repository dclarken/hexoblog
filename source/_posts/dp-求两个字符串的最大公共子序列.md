---
title: dp-求两个字符串的最大公共子序列
date: 2017-9-16 00:08:12
tags: [code,dp]
categories: [code,dp]
---

```python
from __future__ import print_function 
#将python3中的print函数调用，可以实现end=‘’不换行输出的功能
import math
```
问题描述：
求出两个字符串最大公共子序列以及输出他的长度，基础动态规划题目

输入：
2017 11 02
02 11 2017
输出：
最大公共子序列

---
```python
#_*_coding: utf_8 _*_ 
def lcs(a,b):
    lena=len(a)
    lenb=len(b)
    c=[[0 for i in range(lenb+1)] for j in range(lena+1)]
    flag=[[0 for i in range(lenb+1)] for j in range(lena+1)]
    for i in range(lena):
        for j in range(lenb):
            if a[i]==b[j]:
                c[i+1][j+1]=c[i][j]+1
                flag[i+1][j+1]='ok'
            elif c[i+1][j]>c[i][j+1]:
                c[i+1][j+1]=c[i+1][j]
                flag[i+1][j+1]='le'
            else:
                c[i+1][j+1]=c[i][j+1]
                flag[i+1][j+1]='up'
    maxnum=0
    for i in range(1,lena):
        for j in range(lenb):
            if flag[-i][-j]=='ok':
                count=c[-i][-j]
                maxnum=max(maxnum,int(count))
                break
    return c,flag,maxnum
def printLcs(flag,a,i,j):
    if i==0 or j==0:
        return
    if flag[i][j]=='ok':
        printLcs(flag,a,i-1,j-1)
        
        print(a[i-1],end='')
    elif flag[i][j]=='le':
        printLcs(flag,a,i,j-1)
    else:
        printLcs(flag,a,i-1,j) 
a='2017 11 02'
b='02 11 2017'
c,flag,count=lcs(a,b)
for i in c:
    print(i)
print('')
for j in flag:
    print(j)
print('')
printLcs(flag,a,len(a),len(b))
print('')
print(count)
```