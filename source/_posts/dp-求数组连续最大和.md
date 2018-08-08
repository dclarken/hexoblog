---
title: dp-求数组连续最大和
date: 2017-9-18 00:08:12
tags: [code,dp]
categories: [code,dp]
---

一个数组有 N 个元素，求连续子数组的最大和。 
例如：[-1,2,1]，和最大的连续子数组为[2,1]，其和为 3
属于动态规划问题，从小到大一直找。

---
```python
#coding:utf8
def main():
    nums=[int(i) for i in raw_input().split()]
    #nums=[-1,0,1]
    print(sum_s(nums))
def sum_s(nums):
    s_max=None
    s_sum=0
    for i in range(len(nums)):
        if s_sum>0:
            s_sum=s_sum+nums[i]
        else:
            s_sum=nums[i]
        s_max=max(s_max,s_sum)
    return s_max 
main()
```