---
title: vector用法
date: 2017-9-12 00:08:12
tags: [笔记,数据结构,c++]
categories: [笔记,c++]
comments: false
---

[文档来自](http://blog.csdn.net/hancunai0017/article/details/7032383)
**`vector(向量):`**
 
C++中的一种数据结构,确切的说是一个类.它相当于一个动态的数组,当程序员无法知道自己需要的数组的规模多大时,用其来解决问题可以达到最大节约空间的目的.
`文件包含:`  
首先在程序开头处加上#include<vector>以包含所需要的类文件vector还有一定要加上using namespace std;
`变量声明:`
声明一个int向量以替代一维的数组:vector <int> a;用vector代替二维数组.其实只要声明一个一维数组向量即可,而一个数组的名字其实代表的是它的首地址,所以只要声明一个地址的向量即可,即:vector <int *> a.同理想用向量代替三维数组也是一样,vector <int**>a;再往上面依此类推.int可以改成其他的如char、string甚至是strcut。
`具体的用法以及函数调用:`

* 1.push_back   在数组的最后添加一个数据
* 2.pop_back    去掉数组的最后一个数据 
* 3.at                得到编号位置的数据
* 4.begin           得到数组头的指针
* 5.end             得到数组的最后一个单元+1的指针
* 6．front        得到数组头的引用
* 7.back            得到数组的最后一个单元的引用
* 8.max_size     得到vector最大可以是多大
* 9.capacity       当前vector分配的大小
* 10.size           当前使用数据的大小
* 11.resize         改变当前使用数据的大小，如果它比当前使用的大者填充默认值
* 12.reserve      改变当前vecotr所分配空间的大小
* 13.erase         删除指针指向的数据项
* 14.clear          清空当前的vector
* 15.rbegin        将vector反转后的开始指针返回(其实就是原来的end-1)
* 16.rend          将vector反转构的结束指针返回(其实就是原来的begin-1)
* 17.empty        判断vector是否为空
* 18.swap         与另一个vector交换数据
`详细的函数实现功能:`

* c.clear()         移除容器中所有数据。
* c.empty()         判断容器是否为空。
* c.erase(pos)        删除pos位置的数据
* c.erase(beg,end) 删除[beg,end]区间的数据 如： vec.erase(vec.begin()+2);删除第3个元素
* c.front()         传回第一个数据。
* c.insert(pos,elem)  在pos位置插入一个elem拷贝
* c.pop_back()     删除最后一个数据。
* c.push_back(elem) 在尾部加入一个数据。
* c.resize(num)     重新设置该容器的大小
* c.size()         回容器中实际数据的个数。
* c.begin()           返回指向容器第一个元素的迭代器
* c.end()             返回指向容器最后一个元素的迭代器                                  
* c1.swap(c2)==swap(c1,c2)         将c1和c2元素互换。两操作相同。
 
`#include<algorithm>中的泛函算法`
搜索算法：find() 、search() 、count() 、find_if() 、search_if() 、count_if() 
分类排序：sort() 、merge() 
删除算法：unique() 、remove() 
生成和变异：generate() 、fill() 、transformation() 、copy() 
关系算法：equal() 、min() 、max() 
```cpp
sort(v1.begin(),vi.begin()+v1.size/2）; 
```
对v1的前半段元素排序，默认是从小到大排序，即默认为冒泡排序。
```cpp
sort(v1.begin(),v1.end());
```
对整个vector做排序
定义排序比较函数：
```cpp
bool cmp(const int &a,const int &b)
{
    return a>b;
}
```
调用时:sort(vec.begin(),vec.end(),cmp)，这样就降序排序。
`其他功能:`

* c.at(idx)  传回索引idx所指的数据，如果idx越界，抛出out_of_range。 
* c.back()  传回最后一个数据，不检查这个数据是否存在。
* c.front()  传回地一个数据。 
* get_allocator  使用构造函数返回一个拷贝。 
* c.rbegin()  传回一个逆向队列的第一个数据。 
* c.rend()  传回一个逆向队列的最后一个数据的下一个位置。 