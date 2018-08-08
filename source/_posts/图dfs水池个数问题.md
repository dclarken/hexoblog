---
title: 图dfs水池个数问题
date: 2017-8-13 12:31:21
tags: [code,dfs]
categories: [code,dfs]
---

输入：
第一行输入一个整数N，表示共有N组测试数据
每一组数据都是先输入该地图的行数m(0<m<100)与列数n(0<n<100)，然后，输入接下来的m行每行输入n个数，表示此处有水还是没水（1表示此处是水池，0表示此处是地面）
输出：
输出该地图中水池的个数。
要注意，每个水池的旁边（上下左右四个位置）如果还是水池的话的话，它们可以看做是同一个水池。

---
python代码，一直跑不出递归，已经醉了
```python
#coding:utf-8
import sys   
sys.setrecursionlimit(1000000000)
def dfs(i,j):
    if (i<0 or i>=m or j<0 or j>=n or (s[i][j]==0)):
        return 
    s[i][j]==0
    dfs(i,j+1)
    dfs(i,j-1)
    dfs(i-1,j)
    dfs(i+1,j)

if __name__ == '__main__':
    #N=raw_input().split()
    try:
        s=[]
        ans=0
        m,n=[int(i) for i in raw_input().split()]
        for i in range(m):
            s.append([int(i) for i in raw_input().split()])
        for i in range(m):
            for j in range(n):
                if s[i][j]==1:
                    dfs(i,j)
                    ans +=1
        print(ans)
        print s
    except:
        pass
```

---
```cpp
 //C++代码： 
#include<stdio.h>
#include<string.h>
int s[101][101],n,m;
void dfs(int i,int j)
{
    if(i<0||i>m||j<0||j>n||s[i][j]==0)//当所有的点为0时说明这是一个水池
        return;
      s[i][j]=0;//每次搜索一个点后，置为0，避免重复
      //从此点开始往四周扩展
      dfs(i,j+1);
      dfs(i,j-1);
      dfs(i-1,j);
      dfs(i+1,j);
}
int main()
{
    int N;
    scanf("%d",&N);
    while(N--)
    {
        int i,j,ans=0;
        memset(s,0,sizeof(s));//初始化，0表示地面，1表示水池
       scanf("%d%d",&m,&n);
       for(i=0;i<m;i++)
       {
        for(j=0;j<n;j++)
          scanf("%d",&s[i][j]);
       }
       for(i=0;i<m;i++)
       {
            for(j=0;j<n;j++)
            {
                if(s[i][j]==1)//每次从是水池的地方开始深搜
                 {
                    dfs(i,j);
                    ans++; //搜索结束后既为满足条件
                 }
            }
       }
       printf("%d\n",ans);//输出结果
    }
    return 0;
}
```