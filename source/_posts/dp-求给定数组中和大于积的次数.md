---
title: dp-求给定数组中和大于积的次数
date: 2017-9-17 00:08:12
tags: [code,dp]
categories: [code,dp]
---

问题描述
一个袋子里面有n个球，每个球上面都有一个号码(拥有相同号码的球是无区别的)。如果一个袋子是幸运的当且仅当所有球的号码的和大于所有球的号码的积。
例如：如果袋子里面的球的号码是{1, 1, 2, 3}，这个袋子就是幸运的，因为1 + 1 + 2 + 3 > 1 * 1 * 2 * 3
你可以适当从袋子里移除一些球(可以移除0个,但是别移除完)，要使移除后的袋子是幸运的。现在让你编程计算一下你可以获得的多少种不同的幸运的袋子。

输入：
球号数组
输出
种类

---
```python
#-*- coding:utf-8 -*-
def isluck(nums): #幸运袋子判断函数
    sum=0
    mul=1
    for i in nums:
        sum +=i
        mul *=i
    if sum>mul:
        return True
    else:
        return False
    
def composite(array,Sum,multiply): #从小到大的规划过程
    count=0
    for i in xrange(len(array)):
        if i>0 and array[i]==array[i-1]:
            continue
        Sum+=array[i]
        multiply*=array[i]
        if Sum>multiply:
            count+=1+composite(array[i+1:], Sum, multiply) # 当序列中的数值都比较大的时候能够满足要求，那数值小的也更加容易满足
        elif array[i]==1:
            count+=composite(array[i+1:], Sum, multiply) # 1为特殊情况，当为1的时候，和一定一直大于乘积，所以需要累加
        else:
            break
        Sum-=array[i] #如果不满足，回退为上一状态
        multiply/=array[i] 
    return count

def main():
    n=raw_input()
    array=raw_input().split()
    array=[int(i) for i in array]
    array.sort() #首先对其从小到大排列
    print composite(array,0,1)
main()
```