---
title: 图BFS最小转机问题
date: 2017-8-12 12:32:21
tags: [code,bfs]
categories: [code,bfs]
---

思路：
我们假设所有边的长度都是1，每一个线段表示一个转机，求最少转机就是求最短路径而已   
深度优先和广度优先的方法都可以，但是广度优先更适合所有边的权重一样的情况  
输入：
第一行n,m,p,q分别为站点个数，航线个数，起始点，目的地
后面m行表示m条航线 
输出：
最小转机数量
样例：
5 7 1 5
1 2
1 3
2 3
2 4
3 4
3 5
4 5

2

---
```python 
#coding:utf-8
def change(n,line): #存储图的关系与标记数组
    e=[[99999 for i in range(n+1)] for j in range(n+1)] #初始化所有的线路都为最大值99999
    for i in range(1,n+1):
        e[i][i]=0 #自己和自己之间没有线路
    for i in range(len(line)): #根据m行输入，确定哪一些节点之间是有航线的，
        a,b=line[i]
        a=int(a)
        b=int(b)
        e[a][b]=1 #节点之间有航班即为往返都可通行，即为双向图
        e[b][a]=1
    return e
```

```python 
def bfs(n,m,p,q,e):
    head=1
    tail=1
    que=[[0,0] for i in range(m)]
    que[tail]=[p,0]
    tail +=1
    book=[0 for i in range(m)]
    book.insert(p,1)
    while head<=tail:
        print ('que=',que)
        cur=que[head][0] #que队列中首航班号
        for i in range(1,n+1):
            if e[cur][i]==1 and book[i]==0: #利用方向图，从城市cur到城市i是否有航班并且判断城市i是否在队列中
                que[tail]=[i,que[head][1]+1] #满足条件，cur到城市i有航班并且城市i不在队列中，则i入队，转机次数+1
                tail +=1
                book[i]=1 #改变标记，以防重用 
            if tail==q+1:
                return(que[tail-1][1]) #由于tail是指向队列队尾的下一个位置，所以减1 
        head +=1
```

```python    
def main():
    n,m,p,q=[int(i) for i in raw_input().split()]
    line=[]
    for i in range(int(m)):
        line.append(raw_input().split())
    e=[]
    print (line)
    e=change(n,line)
    print(e)
    ans=bfs(n,m,p,q,e)
    print(ans)

main()
```