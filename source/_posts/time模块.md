---
title: time模块
date: 2017-10-12 00:08:12
tags: [笔记,python,time]
categories: [笔记,python]
comments: false
---
![mahua](https://i.loli.net/2017/11/01/59f9890e94a1e.jpg)
<!--more-->
Python时间模块之Time模块解析
[文章转载来源](http://blog.csdn.net/SeeTheWorld518/article/details/48314501)
time模块。 
在开始前，先说明几点：

* 在Python中，通常有这几种方式表示时间：时间戳、格式化的时间字符串、元组（struct_time 共九种元素）。由于Python的time模块主要是调用C库实现的，所以在不同的平台可能会有所不同。
* 时间戳（timestamp）的方式：时间戳表示是从1970年1月1号 00:00:00开始到现在按秒计算的偏移量。查看一下type(time.time())的返回值类型，可以看出是float类型。返回时间戳的函数主要有time()、clock()等。
* UTC（世界协调时），就是格林威治天文时间，也是世界标准时间。在中国为UTC+8。DST夏令时。
* 元组方式：struct_time元组共有9个元素，返回struct_time的函数主要有gmtime()，localtime()，strptime()。

## time.localtime()获取当前的时间

```python
import time
ls = time.localtime() 获取当前的时间，其生成的ls的数据结构为具有9个元素的元组
time.struct_time(tm_year=2015, tm_mon=8, tm_mday=24, tm_hour=9, tm_min=39, tm_sec=38, tm_wday=0, tm_yday=236, tm_isdst=0)
tm_year ：年
tm_mon ：月（1-12）
tm_mday ：日（1-31）
tm_hour ：时（0-23）
tm_min ：分（0-59）
tm_sec ：秒（0-59）
tm_wday ：星期几（0-6,0表示周日）
tm_yday ：一年中的第几天（1-366）
tm_isdst ：是否是夏令时（默认为-1）
```

使用元组索引获取对应项的值，或者是使用成员符号调用。
调用年份的方法：

```python
>>> ls[0]
2015
>>> ls.tm_year
2015
time.time()：返回当前时间的时间戳
>>> time.time()
1440337405.85
#对时间戳取整
>>> int(time.time())
1440746424
```

## time.mktime(t)：将一个struct_time转化为时间戳 
time.mktime() 函数执行与gmtime(), localtime()相反的操作，

```python
>>> time.mktime(time.localtime())
1440338541.0
```

## time.sleep(secs)：线程推迟指定的时间运行
secs表示线程睡眠指定时间，单位为妙。
time.clock()
函数以浮点数计算的秒数返回当前的CPU时间。用来衡量不同程序的耗时，比time.time()更有用。在不同的系统上含义不同。在NUix系统上，它返回的是“进程时间”，它是用妙表示的浮点数（时间戳）。而在Windows中，第一次调用，返回的是进程运行时实际时间。而第二次之后的调用是自第一次调用以后到现在的运行时间。 
返回值 
该函数有两个功能：

（1）在第一次调用的时候，返回的是程序运行的实际时间；
（2）第二次之后的调用，返回的是自第一次调用后,到这次调用的时间间隔在win32系统下，这个函数返回的是真实时间（wall time），而在Unix/Linux下返回的是CPU时间。

## time.asctime( [t] ) 
把一个表示时间的元组或者struct_time表示为 ‘Sun Aug 23 14:31:59 2015’ 这种形式。如果没有给参数，会将time.localtime()作为参数传入。

```python
>>> time.asctime(time.gmtime())
'Sun Aug 23 14:31:59 2015'
```

## time.ctime([secs]) 
把一个时间戳（按秒计算的浮点数）转化为time.asctime()的形式。如果为指定参数，将会默认使用time.time()作为参数。它的作用相当于time.asctime(time.localtime(secs)) 

```python
>>> time.ctime(1440338541.0)
'Sun Aug 23 22:02:21 2015'
```

## time.strftime( format [, t] ) 
返回字符串表示的当地时间。 
把一个代表时间的元组或者struct_time（如由time.localtime()和time.gmtime()返回）转化为格式化的时间字符串，格式由参数format决定。如果未指定，将传入time.localtime()。如果元组中任何一个元素越界，就会抛出ValueError的异常。函数返回的是一个可读表示的本地时间的字符串。 
参数：
format：格式化字符串
t ：可选的参数是一个struct_time对象
时间字符串支持的格式符号：（区分大小写）

```python
%a  本地星期名称的简写（如星期四为Thu）      
%A  本地星期名称的全称（如星期四为Thursday）      
%b  本地月份名称的简写（如八月份为agu）    
%B  本地月份名称的全称（如八月份为august）       
%c  本地相应的日期和时间的字符串表示（如：15/08/27 10:20:06）       
%d  一个月中的第几天（01 - 31）  
%f  微妙（范围0.999999）    
%H  一天中的第几个小时（24小时制，00 - 23）       
%I  第几个小时（12小时制，0 - 11）       
%j  一年中的第几天（001 - 366）     
%m  月份（01 - 12）    
%M  分钟数（00 - 59）       
%p  本地am或者pm的相应符      
%S  秒（00 - 61）    
%U  一年中的星期数。（00 - 53星期天是一个星期的开始。）第一个星期天之前的所有天数都放在第0周。     
%w  一个星期中的第几天（0 - 6，0是星期天）    
%W  和%U基本相同，不同的是%W以星期一为一个星期的开始。    
%x  本地相应日期字符串（如15/08/01）     
%X  本地相应时间字符串（如08:08:10）     
%y  去掉世纪的年份（00 - 99）两个数字表示的年份       
%Y  完整的年份（4个数字表示年份）
%z  与UTC时间的间隔（如果是本地时间，返回空字符串）
%Z  时区的名字（如果是本地时间，返回空字符串）       
%%  ‘%’字符  
```

```python
>>> time.strftime("%Y-%m-%d %H:%M:%S",  formattime)
'2015-08-24 13:01:30'
```

## time.strptime(string[,format]) 
将格式字符串转化成struct_time. 
该函数是time.strftime()函数的逆操作。time strptime() 函数根据指定的格式把一个时间字符串解析为时间元组。所以函数返回的是struct_time对象。
参数：
string ：时间字符串
format：格式化字符串

```python
>>> stime = "2015-08-24 13:01:30"
>>> formattime = time.strptime(stime,"%Y-%m-%d %H:%M:%S")
>>> print formattime
time.struct_time(tm_year=2015, tm_mon=8, tm_mday=24, tm_hour=13, tm_min=1, tm_sec=30, tm_wday=0, tm_yday=236, tm_isdst=-1)
```
