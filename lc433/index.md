# LC433 - 最小基因变化


<!--more-->

> 问题链接：[最小基因变化](https://leetcode.cn/problems/minimum-genetic-mutation/)

## 思考

---

假设没有 bank 的存在，所有的变化都是可行的时候，本题最小基因变化，其实就是在求 start 基因到 end 基因之间的距离。这里的距离定义为，两个基因之间字符不同的数量。因为当所有的基因变化都是有效的时候，我们只需要让每次基因变化，都将 start 中一个与 end 中不同的字符变为相同即可。

与自由变化（没有 bank）不同的是，在自由变化的搜索过程，我们知道一定存在最短的路径使得 start 经过最小次数的变换之后变成 end。但是当 bank 存在的时候，这样一条最短路径可能并不存在，例如：

```python
start = "AACCGGTT"
end = "AAACGGTA"
bank = ["AACCGATT","AACCGATA","AAACGATA","AAACGGTA"]
```

可以看到，在这种情况中，start 和 end 之间的距离为 2，但是 bank 中不能提供这种最短路径，而是必须借助一个跳板，最终路径为：`"AACCGGTT"` -> `"AACCGATT"` -> `"AACCGATA"` -> `"AAACGATA"` -> `"AAACGGTA"`。也就是说，虽然在变异过程中出现了 start 与 end 之间 距离增加的情况，但这是唯一的可行解。

为了解决这种问题，最简单无脑的方式就是对所有的可行解都进行搜索。

对一个树进行搜索的方式有深度优先搜索和广度优先搜索。因为本题需要返回的是**变异的最短路径**，假如我们选择深度优先搜索，这会导致我们搜到一个可行解的时候，必须考虑这个可行解是不是最优解，从而不得不搜索所有的情况。而当我们选择广度优先搜索时，我们搜到的**第一个可行解一定是最优解**，这是因为 BFS 是在搜索的过程中，下一个节点距起点的距离不可能小于当前节点，所以本题采用 **BFS**。

## 代码

---

新建一个队列，将 start 加入到队列当中。每次从队列中取出一个节点，从 bank 中搜索未访问过的并且当前节点可以到达的下一个节点 ，并将其加入对列。当下一个节点为 end 时直接返回最终结果；当队列为空时，返回 end 不可达。

```python
class Solution:
    def distance(self, start, end):
        dis = 0
        for i, j in zip(start, end):
            if i != j:
                dis += 1

        return dis

    def minMutation(self, start: str, end: str, bank: List[str]) -> int:
        queue = deque()
        queue.append((start, 0))
        bank = set(bank)
        visit = []
        res = []

        while queue:
            curgen, curdis = queue.popleft()
            if curgen == end:
                return curdis

            visit = []
            for gen in bank:
                if self.distance(gen, curgen) == 1:
                    queue.append((gen, curdis + 1))
                    visit.append(gen)
                    
            for gen in visit:
                bank.remove(gen)
```

> 执行用时: **40 ms**，超过了 **41%** 的 **Python3** 提交记录。
>
> 内存消耗: **15 MB**，超过了 **37%** 的 **Python3** 提交记录。

## 小改进

---

```python
class Solution:
    def distance(self, start, end):
        return sum(i != j for i, j in zip(start, end))

    def minMutation(self, start: str, end: str, bank: List[str]) -> int:
        graph = defaultdict(list)
        for geni, genj in combinations([start] + bank, 2):
            if self.distance(geni, genj) == 1:
                graph[geni].append(genj)
                graph[genj].append(geni)

        queue = deque()
        queue.append((start, 0))
        visit = set()
        while queue:
            curgen, curdis = queue.popleft()
            for nexgen in graph[curgen]:
                if nexgen == end:
                    return curdis + 1
                
                if nexgen in visit:
                    continue
                else:
                    visit.add(nexgen)
                    queue.append((nexgen, curdis + 1))

        return -1
```

与初始代码主要的区别是，这里采用了先计算全部可行的走法组合，将每个节点可行的走法存储在字典中，再进行 BFS 的做法。由于字典的增时间复杂度小于删和查，所以时间消耗有了进一步的减少：

> 执行用时: **32 ms**，超过了 **87%** 的 **Python3** 提交记录。
>
> 内存消耗: **15.1 MB**，超过了 **9%** 的 **Python3** 提交记录。

## 伪 · 结尾碎碎念

---

为什么没啥可讲的，还要记录这道题呢？这是因为这道题：

1. 今天没能完全独立做出来。我一开始以为这题用纯纯的 BFS 会超时，就想了很多花里胡哨的办法，最后写了俩小时给自己写不会了，实在头痛点开题解一看，好嘛，纯纯 BFS。只能说，以后做题要先把最简单的方法实现了再去想如何优化，眼高手低的后果是等于个零😀。
2. 这题还有很多解法可以扩展。这里先占个位置，等整明白了把双向 BFS、A* 等算法也补上。

另外：现在是2022/5/8 2:36，刚才 LeetCode CN 好像崩了，提交没反应一直转圈。
