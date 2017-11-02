---
title: sort用法
date: 2017-9-17 00:08:12
tags: [笔记,python]
categories: [笔记,python]
comments: false
---

[Python中的sort()方法使用基础文档：](http://www.cnblogs.com/sunny3312/archive/2017/01/07/6260472.html)
格式：
```python
sorted(iterable[, cmp[, key[, reverse]]])
iterable.sort(cmp[, key[, reverse]])
```
参数解释：

* iterable指定要排序的list或者iterable，不用多说；
* cmp为函数，指定排序时进行比较的函数，可以指定一个函数或者lambda函数，如：
students为类对象的list，没个成员有三个域，用sorted进行比较时可以自己定cmp函数，例如这里要通过比较第三个数据成员来排序，代码可以这样写：
```python
students = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
sorted(students, key=lambda student : student[2])
```
* key为函数，指定取待排序元素的哪一项进行排序，函数用上面的例子来说明，代码如下：
```python
sorted(students, key=lambda student : student[2])
```
key指定的lambda函数功能是去元素student的第三个域（即：student[2]），因此sorted排序时，会以students所有元素的第三个域来进行排序。

reverse实现降序排序，需要提供一个布尔值，默认为False（升序排列）。
