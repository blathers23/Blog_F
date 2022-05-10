# Python常用内置容器及方法


<!--more-->

{{<admonition type=success title="">}}

用好内置的容器及方法是快速巧妙解题的关键！

{{</admonition>}}

## 容器

---

###  defaultdict 默认字典

`collections.defaultdict` 是一个特殊的字典，在实例化之前可以接受一个 `factory_function`，来当作当查询的键不存在的时候所返回的默认值。`factory_function` 指的是 `list`、`set`、`str` 等等。

举例：

```python
>>> import collections
>>> dic = dict()
>>> dedic = collections.defaultdict(str)
>>> dic[0], dedic[0] = "Deep", "Dark"
>>> dic[0]
'Deep'
>>> dic[1]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 1
>>> dedic[0]
'Dark'
>>> dedic[1]
''
>>> 
```



### deque 双端队列

`collections.deque` 全称为 double-end queue，也就是双端队列。可以通过该容器模拟栈或者队列，从而获得比 `list` 更好的性能。

举例：

```python 
>>> import collections
# 队列
>>> queue = collections.deque()
>>> queue
deque([])
>>> queue.append(1)
>>> queue.append(2)
>>> queue.append(3)
>>> queue
deque([1, 2, 3])
>>> queue.popleft()
1
>>> queue
deque([2, 3])
# 堆栈
>>> stack = collections.deque()
>>> stack
deque([])
>>> stack.append(1)
>>> stack.append(2)
>>> stack.append(3)
>>> stack
deque([1, 2, 3])
>>> stack.pop()
3
>>> stack
deque([1, 2])
>>> 
```



## 方法

---

 ### heapq 堆

`heapq` 方法可以将 `list` 中元素在线性时间内调整为小根堆。

举例：

```python 
>>> import heapq
>>> l = [1, 2, 3, 4, 5, 6, 7, 8, 9, 0]
>>> heap = heapq.heapify(l)
>>> l
[0, 1, 3, 4, 2, 6, 7, 8, 9, 5]
>>> heapq.heappush(l, 10)
>>> l
[0, 1, 3, 4, 2, 6, 7, 8, 9, 5, 10]
>>> heapq.heappop(l)
0
>>> heapq.heappop(l)
1
>>> heapq.heappop(l)
2
>>> l
[3, 4, 6, 8, 5, 9, 7, 10]
>>> 
```



### bisect 二分查找

`bisect` 方法支持对有序列表的二分查找。

举例：

```python
>>> import bisect
>>> l = [3, 5, 5, 8, 10]
>>> bisect.bisect_left(l, 5)	# 若元素存在，返回最左边符合条件的位置
1
>>> bisect.bisect_right(l, 5)	# 若元素存在，返回最右边符合条件的下一个位置
3
>>> bisect.bisect_left(l, 4)	
1
>>> bisect.bisect_right(l, 4)	# 若元素不存在，返回分割点。
1
```

### map 映射

`map` 方法将提供的函数对给定的序列做映射并返回迭代器。

举例：

```python
>>> def A():
...     return
...
>>> A()
>>> A() == None
True
>>> def f(x):
...     return x ** 2
...
>>> ite = map(f, [1,2,3,4,5,6])
>>> while True:
...     print(next(ite))
...
1
4
9
16
25
36
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
StopIteration
>>> 
```




