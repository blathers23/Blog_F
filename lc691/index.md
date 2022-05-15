# LC691 - 贴纸拼词


<!--more-->

> 问题链接：[贴纸拼词](https://leetcode.cn/problems/stickers-to-spell-word/)

## 思考

---

本题被 LeetCode 官方归为了一道 hard 题，但实际上仅仅使用深度优先搜索或广度优先搜索就能解决。与之相比，官方的题解（动态规划 + 记忆化搜索 + 状态压缩）才是实打实的 hard，能力有限，日后再解。

对于搜索来说，每个节点保存的信息包含两个部分：一是当前的 target。在搜索的每一步上，选择一个贴纸，并将贴纸中符合的字母贴在 target 上，这样 target 需要的字母将会越来越少。该搜索树的叶节点有两种可能：一是贴纸匹配，刚好可以拼凑出 target；二是贴纸无法匹配，无法拼凑出 target。而我们要搜索的，就是深度最低的匹配叶节点的深度。所以节点要保存的第二个信息为当前节点的深度，以便返回目标值。

## 代码

---

这里首先给出 DFS 的实现：

```python
class Solution:
    def minStickers(self, stickers: List[str], target: str) -> int:
        @lru_cache(None)
        def dfs(target: str) -> int:
            if not target:
                return 0
            res = float('inf')
            for sticker in stickers:
                replacedWord = addSticker(sticker, target)
                res = min(res, dfs(replacedWord) + 1)
            return res

        def addSticker(sticker: Counter, word: str) -> str:
            for char in sticker:
                word = word.replace(char, '', sticker[char])
            return word

        stickers = [Counter(s) for s in stickers]
        res = dfs(target)
        return res if res != float('inf') else -1
```

 事实上很好理解，从所有贴纸中去除一张，利用贴纸中的字母替换 target 后，将新的 target 传入到下一个节点，最后通过比较选出最短路径。想法非常美好，但是运行将会报错：

```python
RecursionError: maximum recursion depth exceeded while calling a Python object
```

Python 默认递归深度最高只能为 1000，这是因为过深的递归可能会导致堆栈溢出。尽管可以使用 `sys.setrecursionlimit` 修改 Python 递归的深度限制，但这明显是治标不治本的方法：递归深度如此之深，程序运行效率堪忧。

那么如何限制递归的次数呢？有一个很明显的优化方式存在。注意程序中，我们不断使用贴纸替换掉部分 target，那么肯定存在这样两条路径：第一条路经，我们先使用贴纸 A、后使用贴纸 B 替换了 target；第二条路径：我们先使用贴纸 B、后使用贴纸 A 替换了 target。换句话说，我们的搜索中存在着大量的无意义重复。

如何解决这个问题呢？有一个相当简单的方式。且看代码：

```python
class Solution:
    def minStickers(self, stickers: List[str], target: str) -> int:
        @lru_cache(None)
        def dfs(target: str) -> int:
            if not target:
                return 0
            res = float('inf')
            for sticker in stickers:
            	if target[0] not in sticker:	# 注意此行
                    continue
                replacedWord = addSticker(sticker, target)
                res = min(res, dfs(replacedWord) + 1)
            return res

        def addSticker(sticker: Counter, word: str) -> str:
            for char in sticker:
                word = word.replace(char, '', sticker[char])
            return word

        stickers = [Counter(s) for s in stickers]
        res = dfs(target)
        return res if res != float('inf') else -1
```

通过添加这一行，我们对搜索树展开做了一个巨大的限制：只有包含 target 中第一个字母的贴纸才可以展开搜索树。这会带来一个显而易见的影响：子树的数量大大降低了。那么这样做会导致结果的错误吗？答案是不会的。因为我们知道，先使用贴纸 A 后使用贴纸 B 和先使用贴纸 B 而后使用贴纸 A 的效果是相同的，这个加入的限制，仅仅会限制搜索树的展开，而不会对原先树里存在的节点的深度造成任何影响。

这样，我们就可以快速求得最少的贴纸数量，或者说搜索树的深度。结果如下：

>执行用时: **112 ms**，超过了 **88%** 的 **Python3** 提交记录。
>
>内存消耗: **15.4 MB**，超过了 **81%** 的 **Python3** 提交记录。

## 优化

---

在写 DFS 代码之前，我们是不是讨论过如何构建搜索树？本题可以使用 DFS 来搜索，那么 BFS 可以完成这项任务吗？答案肯定是可以的。并且 BFS 有着一项 DFS 不具备的优势：**BFS 搜索到的第一个可行节点，一定就是目标节点**。这是因为 BFS 是按深度进行展开的，那么搜索到的第一个可行节点之前，不会有别的可行节点比其深度更浅。

## BFS 代码

---

```python
class Solution:
    def minStickers(self, stickers: List[str], target: str) -> int:
        availables = [Counter(st) for st in stickers]
        queue = deque([(target, 0)])
        explored = {target}
        while queue:
            cur, step = queue.popleft()
            if not cur:
                return step
            for avl in availables:
                if cur[0] in avl:
                    nxt = cur
                    for k, v in avl.items():
                        nxt = nxt.replace(k, '', v)
                    if nxt not in explored:
                        explored.add(nxt)
                        queue.append((nxt, step + 1))

        return -1
```

最终效果：

>执行用时: **76 ms**，超过了 **98%** 的 **Python3** 提交记录。
>
>内存消耗: **15 MB**，超过了 **97%** 的 **Python3** 提交记录。

{{<admonition bug " 溜大了">}}

芜湖 芜湖 真的好满足！

{{</admonition>}}
