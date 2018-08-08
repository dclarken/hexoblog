---
title: 图dfs输出全排序
date: 2017-8-13 20:45:44
tags: [code,dfs]
categories: [code,dfs]
---
问题: 生成1~n的排列 
思路: 穷举所有可能 在生成结果数组前把重复的去掉 
探索到某一步发现原先选择达不到目标, 就退回一步重新选择. 
效率比普通DFS高. 可以优化排列数和素数环的程序
两种代码一种没有加入回溯，一种有-------分隔

```python
#coding：utf-8
A = [None for i in range(10)]
N = 3

def dfs(cur):
    if cur == N:
        print(A[:N])
    else:
        for i in range(1, N+1):
            if i not in A[:cur]:
                A[cur] = i
                dfs(cur+1)

dfs(0)
```

```python
A = [None for i in range(0, 10)]
V = [False for i in range(0, 10)]
N = 4

def dfs(cur):
    if cur == N: #边界条件到N的时候需要输出结果
        print(A[:N])
    else:
        for i in range(1, N+1): #遍历所有的情况
            if not V[i]: #V为标志，到排序的这个位置上没有数据时候，进入下一步
                V[i] = True #首先改变标志位为true，输入需要排入的数值
                A[cur] = i
                dfs(cur+1) #继续往下搜索
                V[i] = False #跳出了循环，需要回溯到进入循环之前的状态

dfs(0)
```

---
问题: 数字1~n围成一个n个节点的环, 不允许数字重复, 任意2个相邻数字相加, 结果均为素数, 打印所有素数环的组合. 
思路: 同排列数, 多了素数判断. 

```python
A = [None for i in range(0, 10)]
N = 6

def is_prime(n):
    for i in range(2, n//2+1):
        if n%i == 0:
            return False
    return True

def dfs(cur):
    if cur == N:
        if is_prime(A[0]+A[N-1]):
            print(A[:N])
    else:
        for i in range(1, N+1):
            if i not in A[:cur]:
                if cur == 0 or is_prime(i+A[cur-1]):
                    A[cur] = i
                    dfs(cur+1)

dfs(0)
```

```python
A = [None for i in range(0, 10)]
V = [False for i in range(0, 10)]
N = 6
def is_prime(n):
    for i in range(2, n//2+1):
        if n%i == 0:
            return False
    return True

def dfs(cur):
    if cur == N:
        if is_prime(A[0]+A[N-1]):
            print(A[:N])
    else:
        for i in range(1, N+1):
            if not V[i]:
                if cur == 0 or is_prime(i+A[cur-1]):
                    V[i] = True
                    A[cur] = i
                    dfs(cur+1)
                    V[i] = False

dfs(0)
```