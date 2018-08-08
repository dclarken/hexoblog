---
title: Dijkstra算法
date: 2017-8-23 20:46:44
tags: [笔记,网络协议]
categories: [笔记,网络协议]
---

文档资料：
[代码详解](http://www.wutianqi.com/?p=1890)
[算法详解](http://www.cnblogs.com/biyeymyhjob/archive/2012/07/31/2615833.html)

**`freopen函数`**
[freopen妙用文档资料](http://blog.chinaunix.net/uid-11600035-id-2866019.html)
因为文件指针使用的是标准流文件，因此我们可以不定义文件指针。
接下来我们使用freopen()函数以只读方式r(read)打开输入文件slyar.in
```cpp
freopen("slyar.in", "r", stdin);
```
然后使用freopen()函数以写入方式w(write)打开输出文件slyar.out
```cpp
freopen("slyar.out", "w", stdout);
```
单独使用stdin方法时候，系统有的时候会不读取我们自己创建的文件，这个时候的解决办法可以是写利用stdout输出一个txt的文件，然后将该文件改名并且填入需要输入的内容，作为stdio的文件。

**`算法步骤：`**

* a.初始时，S只包含源点，即S＝{v}，v的距离为0。U包含除v外的其他顶点，即:U={其余顶点}，若v与U中顶点u有边，则<u,v>正常有权值，若u不是v的出边邻接点，则<u,v>权值为∞。
* b.从U中选取一个距离v最小的顶点k，把k，加入S中（该选定的距离就是v到k的最短路径长度）。
* c.以k为新考虑的中间点，修改U中各顶点的距离；若从源点v到顶点u的距离（经过顶点k）比原来距离（不经过顶点k）短，则修改顶点u的距离值，修改后的距离值的顶点k的距离加上边上的权。
* d.重复步骤b和c直到所有顶点都包含在S中
![jiexi1](https://i.loli.net/2017/10/20/59e99cc231939.png)
![jiexi2](https://i.loli.net/2017/10/20/59e99cc2a104e.png)

**`代码`**
```cpp
#include <iostream>
#include <stdio.h>
#include <fstream>
#include <stdlib.h>
using namespace std;

const int maxnum = 100;
const int maxint = 999999;

// 各数组都从下标1开始
int dist[maxnum];     // 表示当前点到源点的最短路径长度
int prev[maxnum];     // 记录当前点的前一个结点
int c[maxnum][maxnum];   // 记录图的两点间路径长度
int n, line;             // 图的结点数和路径数

// n -- n nodes
// v -- the source node
// dist[] -- the distance from the ith node to the source node
// prev[] -- the previous node of the ith node
// c[][] -- every two nodes' distance
void Dijkstra(int n, int v, int *dist, int *prev, int c[maxnum][maxnum])
{
	bool s[maxnum];    // 判断是否已存入该点到S集合中
	for(int i=1; i<=n; ++i)
	{
		dist[i] = c[v][i];
		s[i] = 0;     // 初始都未用过该点
		if(dist[i] == maxint)
			prev[i] = 0;
		else
			prev[i] = v;
	}
	dist[v] = 0;
	s[v] = 1;

	// 依次将未放入S集合的结点中，取dist[]最小值的结点，放入结合S中
	// 一旦S包含了所有V中顶点，dist就记录了从源点到所有其他顶点之间的最短路径长度
         // 注意是从第二个节点开始，第一个为源点
	for(int i=2; i<=n; ++i)
	{
		int tmp = maxint;
		int u = v;
		// 找出当前未使用的点j的dist[j]最小值
		for(int j=1; j<=n; ++j)
			if((!s[j]) && dist[j]<tmp)
			{
				u = j;              // u保存当前邻接点中距离最小的点的号码
				tmp = dist[j];
			}
		s[u] = 1;    // 表示u点已存入S集合中

		// 更新dist
		for(int j=1; j<=n; ++j)
			if((!s[j]) && c[u][j]<maxint)
			{
				int newdist = dist[u] + c[u][j];
				if(newdist < dist[j])
				{
					dist[j] = newdist;
					prev[j] = u;
				}
			}
	}
}

// 查找从源点v到终点u的路径，并输出
void searchPath(int *prev,int v, int u)
{
	int que[maxnum];
	int tot = 1;
	que[tot] = u;
	tot++;
	int tmp = prev[u];
	while(tmp != v)
	{
		que[tot] = tmp;
		tot++;
		tmp = prev[tmp];
	}
	que[tot] = v;
	for(int i=tot; i>=1; --i)
		if(i != 1)
			cout << que[i] << " -> ";
		else
			cout << que[i] << endl;
}

int main()
{
	freopen("input.txt", "r", stdin);
	if( freopen("input.txt","r",stdin)== NULL )
        cout<< "false"<<endl;
    else
        cout<< "open"<<endl;
	// 各数组都从下标1开始

	// 输入结点数
	cin >> n;
	// 输入路径数
	cin >> line;
	int p, q, len;          // 输入p, q两点及其路径长度

	// 初始化c[][]为maxint
	for(int i=1; i<=n; ++i)
		for(int j=1; j<=n; ++j)
			c[i][j] = maxint;

	for(int i=1; i<=line; ++i)
	{
		cin >> p >> q >> len;
		if(len < c[p][q])       // 有重边
		{
			c[p][q] = len;      // p指向q
			c[q][p] = len;      // q指向p，这样表示无向图
		}
	}

	for(int i=1; i<=n; ++i)
		dist[i] = maxint;
	for(int i=1; i<=n; ++i)
	{
		for(int j=1; j<=n; ++j)
			printf("%8d", c[i][j]);
		printf("\n");
	}

	Dijkstra(n, 1, dist, prev, c);

	// 最短路径长度
	cout << "源点到最后一个顶点的最短路径长度: " << dist[n] << endl;

	// 路径
	cout << "源点到最后一个顶点的路径为: ";
	searchPath(prev, 1, n);
}

```
![结果1](https://i.loli.net/2017/10/20/59e99c41573f8.png)
![结果2](https://i.loli.net/2017/10/20/59e99c4179e4f.png)