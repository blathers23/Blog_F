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

### collections.Counter 计数器

`Counter` 是 `dict` 的一个子类，用于计数可哈希对象。

举例：

```python
>>> import collections
>>> c = collections.Counter("ABSBSBSBSBSBSBSB")
>>> c
Counter({'B': 8, 'S': 7, 'A': 1})
>>> c.update("ABABA")
>>> c
Counter({'B': 10, 'S': 7, 'A': 4})
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

### itertools.pairwise 叠对

`pairwise` 方法返回从迭代器中获取的连续叠对。

这是 Python 3.10 新增的方法。

举例：

```python
>>> import itertools
>>> list(itertools.pairwise("ABCDEF"))
[('A', 'B'), ('B', 'C'), ('C', 'D'), ('D', 'E'), ('E', 'F')]
>>> 
```

### sort 排序

`sort` 方法是列表所提供的方法，用于排序，其排序是稳定的，并且可以通过传入参数来自定义排序。可以参见 [LC937 从新排列日志文件](https://leetcode.cn/problems/reorder-data-in-log-files/)。

举例：

```python
>>> l = [1, 2, 3, -4, 5, -6]
>>> l.sort()
>>> l
[-6, -4, 1, 2, 3, 5]
>>> l.sort(reverse=True)
>>> l
[5, 3, 2, 1, -4, -6]
>>> l.sort(key=lambda x: abs(x))
>>> l
[1, 2, 3, -4, 5, -6]
>>>
```

### itertools.combinations 组合

`combinations` 方法返回一个迭代器，迭代内容为可迭代对象的全部非重复组合。这里的非重复指的是位置非重复。

举例：

```python
>>> import itertools
>>> list(itertools.combinations('ABCD', 2))
[('A', 'B'), ('A', 'C'), ('A', 'D'), ('B', 'C'), ('B', 'D'), ('C', 'D')]
>>> list(itertools.combinations('ABCD', 3))
[('A', 'B', 'C'), ('A', 'B', 'D'), ('A', 'C', 'D'), ('B', 'C', 'D')]
>>> 
```

