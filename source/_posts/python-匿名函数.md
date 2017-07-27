---
title: python 匿名函数
date: 2016-07-26 15:34:33
tags: python
---
python 匿名函数
<!--more-->

# lambda
lambda最开始接触的时候是在.net开发的时候，感觉简化了很多的工作里面繁琐，总感觉lambda类似于c的宏定义，只是功能更加强大。

### lambda 参数列表：表达式
下面的代码的作用是对两个数进行相加，通过下面的片段我们可以发现lambda函数的参数列表在左侧并且采用逗号进行分隔

``` python
sum=lambda x,y,z:x+y+z
print(sum(1,2,3))#返回6
```
下面的代码段就是不用匿名函数时的传统方式

``` python
def sum(x,y,z):
	return x+y+z
	
print(sum(1,2,3))
```

###有关map()函数
map()有两个函数
>r = map(func, seq)

func:是一个函数的名称
seq：是一个（如列表)序列
map函数将将seq中的每一个对象进行迭代进行调用 func函数并且返回结果值
下面的代码段是将一个列表里面的所有的值乘以平方

``` python
def square(T):
	return T*T
temperatures = (1, 2, 3, 4, 5)
temperatures_in_Fahrenheit = list(map(square, temperatures))
print(temperatures_in_Fahrenheit) 
#返回的值[1, 4, 9, 16, 25]
```
下面我们用匿名方法来重写上面的功能

``` python
temperatures = (1, 2, 3, 4, 5)
temperatures_in_Fahrenheit = list(map(lambda T:T*T, temperatures))
print(temperatures_in_Fahrenheit)
#返回的值[1, 4, 9, 16, 25]
```
map可以对不同的列表进行计算，但是列表的长度必须一致

``` python
a=[1,2,3,4]
b=[17,12,11,10]
c=[-1,-2,-3,-4]
print(list(map(lambda x,y:x+y,a,b)))#[18, 14, 14, 14]
print(list(map(lambda x,y,z:x+y+z,a,b,c)))#[17, 12, 11, 10]
print(list(map(lambda x,y,z : 2.5*x + 2*y - z, a,b,c)))#[37.5, 31.0, 32.5, 34.0]
```

### Filtering 函数
>filter(function, sequence) 

filter和map的函数一致，但是filter的功能是过滤掉sequence的列表中不符合function的对象

```python
fibonacci = [0,1,1,2,3,5,8,13,21,34,55]
odd_number=list(filter(lambda x:x%2,fibonacci))
print(odd_number)#[1, 1, 3, 5, 13, 21, 55]
even_numbers = list(filter(lambda x: x % 2 == 0, fibonacci))
print(even_numbers)#[0, 2, 8, 34]
```

### 练习
下面的是一个常规的书店的购物车列表

订单号 | 书名和作者|数量|单价
------- | -------|---------|-----|
34587 | C++ Primer,Stanley B. Lippman|2|85.8
2323|Visual C++ 入门，明日科技 |4|88.8
2321|python 学习,岳恩|55|85

1.写一个程序，倘若该订单的单价乘以总价没有大于100元的话就加上10元的运费



### 答案
``` python
orders = [ ["34587","C++ Primer,Stanley B. Lippman", 2, 40.95], 
	   ["2323","PVisual C++ 入门，明日科技", 4, 88.80], 
		   ["2321","python 学习,岳恩",55,85]]

min_order = 100


invoice_totals=list(map(lambda x:x if x[1]>=min_order else (x[0],x[1]+10), map(lambda x:(x[0],x[2]*x[3]),orders)))
print(invoice_totals)
```