# LC15 - 三数之和


<!--more-->

> 问题链接：[三数之和](https://leetcode.cn/problems/3sum/)

## 思考

---

从一个列表中选出所有和为零的三个数的组合，并且不重复，最简单的方式即为三重 for 循环，然后排除重复的组合。时间复杂度为 $\mathcal{O}(n^3)$。

## 代码

---

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        length = len(nums)
        if length < 3:
            return []

        res = []

        for i in range(0, length - 2):
            for j in range(i + 1, length - 1):
                for k in range(j + 1, length):
                    if nums[i] + nums[j] + nums[k] == 0:
                        el = [nums[i], nums[j], nums[k]]
                        if el not in res:
                            res.append(el)

        return res
```

三重 for 循环很容易实现，但是问题是如何排除重复项。重复项的三个数可能以不同的顺序排列，这增加了查重的难度。这里的方法是对首先对数组进行排序，这样保证所有可能中的数大小有序，减少排除重复项的难度。

然后简简单单的 TLE。

最容易想到的下一步改进方式就是，如果我们固定住三个数中的一个数，那么问题就转变为了 LC1 两数之和。对于两数字和来说，最佳的解法为 hashmap，时间复杂度为 $\mathcal{O}(n)$，考虑外循环，时间复杂度来到了 $\mathcal{O}(n^2)$。

```python
class Solution:
    def twoSum(self, nums, target):
        res = []
        map = {}
        for i in range(len(nums)):
            need = target - nums[i]
            if need in map:
                res.append([nums[map[need]], nums[i]])
            else:
                map[nums[i]] = i

        return res

    def threeSum(self, nums):
        nums.sort()
        length = len(nums)
        if length < 3:
            return []

        res = []

        for i in range(0, length - 2):
            others = self.twoSum(nums[i + 1:], target=-nums[i])
            if others != []:
                for other in others:
                    ele = [nums[i]] + other
                    if ele not in res:
                        res.append(ele)
            
        return res
```

这里与 LC1 的一个很大的不同是，twoSum 函数有多个可能的返回结果。这要求我们一定要遍历完所有的可能，导致虽然时间复杂度为 $\mathcal{O}(n^2)$，运行速度依旧很慢，勉强为通过的结果。

> 执行用时: **4824 ms**，超过了 **5%** 的 **Python** 提交记录。
>
> 内存消耗: **17.8 MB**，超过了 **67%** 的 **Python** 提交记录。

## 双指针解法

---

在上述代码中，另一个时间消耗大户为排查重复项。有 hashmap 不便分析，回到最初的三重循环的想法：可以注意到，只要三重循环中的每一重循环都**跳过重复项**，就可以不需要最后额外的排查重复项。如果最初进行一次数组的排序，那么我们只需要检查当前值和上一步的值是否相同，就可以跳过重复项。这是因为排序后所有值相同的数全部相邻。

然而这就为 hashmap 解法引入了新的问题。注意到 hashmap 仅有一根指针，我们不能轻易排除重复项，这是因为 $target$ 很有可能等于相同的两项之和。例如 $target = 0$，$nums = [0, 0]$，此时如果我们跳过相同的数，那么将不会得到正确的返回结果 $[0, 0]$。为了正确的返回结果，还必须引入一个为每个元素进行计数的计数器。

那么有没有什么方式可以在引入双指针的同时将 twoSum 的时间复杂度降为 $\mathcal{O}(n)$ 呢？这里，我们注意到数组已经有序。参考 LC11 的思路，对于任意的双指针 $j$ 和 $k$，假设 $j < k$ 。那么此时有三种情况：

1. $nums[j] + nums[k] < target$：

   此时可以注意到，如果我们继续减小 $k$，直到$k = j + 1$，也不会遇到符合条件的结果。换句话说，对于当前 $j$ 而言，$k \in [j+1, k]$ 不可能存在符合条件的结果。

2. $nums[j] + nums[k] > target$：

   此时可以注意到，如果我们继续增加 $j$，直到$j = k - 1$，也不会遇到符合条件的结果。换句话说，对于当前 $k$ 而言，$j \in [j, k-1]$ 不可能存在符合条件的结果。

3. $nums[j] + nums[k] = target$：

   此时符合条件，可以返回值。

因此我们可以得知，在区间 $[j, k]$ 内，对于情况 1，我们只需要继续搜索区间 $[j+1, k]$，而对于情况 2，我们也只需要继续搜索区间 $[j,k-1]$ 才可能会出现符合条件的结果。而对于情况 3，我们只需要继续搜索区间 $[j+1, k-1]$。无论怎样，经过一次计算，我们需要考虑的列表长度都会减少 1 或 2，这意味着 twoSum 的时间复杂度来到了 $\mathcal{O}(n)$。

## 双指针代码

---

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        length = len(nums)
        nums.sort()
        res = []

        for i in range(0, length - 2):
            if nums[i] > 0:
                break

            if i > 0 and nums[i] == nums[i - 1]:
                continue

            target = -nums[i]
            k = length - 1
            j = i + 1
            while k > j:
                if nums[j] + nums[k] > target:
                    k -= 1
                elif nums[j] + nums[k] < target:
                    j += 1
                else:
                    res.append([nums[i], nums[j], nums[k]])
                    k -= 1
                    j += 1
                    while k > j and nums[k] == nums[k + 1]:
                        k -= 1
                    while k > j and nums[j] == nums[j - 1]:
                        j += 1

        return res
```

此处依旧有一些细节需要注意。对于 twoSum 部分，使用 while 循环来代替 for 循环可以极大的减少运行的时间，对于最外层循环，当 $nums[i] > 0$ 时，无论 $j$ 和 $k$ 取何值，都无法得到 0，因此可以直接结束全部计算，这是因为数组已经经过了从大到小的排序。

总体来说，twoSum 部分时间复杂度为 $\mathcal{O}(n)$，嵌套在外层循环中，整体时间复杂度为 $\mathcal{O}(n^2)$，考虑到排序的时间复杂度为  $\mathcal{O}(nlogn)$，因此整体时间复杂度依旧为 $\mathcal{O}(n^2)$。与 hashmap 的解法相比，虽然时间复杂度相同，但是没有了排除重复项及 hashmap 的影响，最终结果得到了很好的提升。

>执行用时: **436 ms**，超过了 **99%** 的 **Python** 提交记录。
>
>内存消耗: **17.8 MB**，超过了 **72%** 的 **Python** 提交记录。

## 最后 1%

---

注意到 $j$ 和 $k$ 的初值设置问题。由于列表有序，我们完全可以使用二分查找减少双指针最初无用的查找。Python 内置了 `bisect` 库，可以使用二分查找方便的生成列表中目标元素的索引。增加二分查找后，代码如下：

```python
# ······
            k = length - 1
            j = i + 1
            if nums[j] + nums[k] > target:
                k = bisect.bisect_right(nums, target - nums[j], j + 1, k)
            else:
                j = bisect.bisect_left(nums, target - nums[k], j, k - 1)
# ······
```

最终结果：

> 执行用时: **300 ms**，超过了 **100%** 的 **Python** 提交记录。
>
> 内存消耗: **17.9 MB**，超过了 **53%** 的 **Python** 提交记录。

同样的思路，双指针 + 二分查找可以用在 LC16 最接近的三数之和上。

> 问题链接：[最接近的三数之和](https://leetcode.cn/problems/3sum-closest/)

附代码：

```python
class Solution:
    def threeSumClosest(self, nums: List[int], target: int) -> int:
        res = 10 ** 4
        sum = 0
        need_ = 0
        length = len(nums)
        if length == 3:
            return nums[0] + nums[1] + nums[2]

        nums.sort()
        for i in range(length - 2):
            need = target - nums[i]

            if i > 0 and nums[i] == nums[i - 1]:
                continue

            if target > 0 and need <= 0:
                if abs(res - target) > abs(nums[i] + nums[i + 1] + nums[i + 2] - target):
                    res = nums[i] + nums[i + 1] + nums[i + 2]
                break

            j = i + 1
            k = length - 1

            if nums[j] + nums[k] > need:
                k = bisect.bisect_right(nums, need - nums[j], i + 2, k)
            else:
                j = bisect.bisect_left(nums, need - nums[k], i + 1, k - 1) - 1
            
            while j < k:
                sum = nums[i] + nums[j] + nums[k]
                if abs(res - target) > abs(sum - target):
                    res = sum

                need_ = nums[j] + nums[k]
                if need_ > need:
                    k -= 1
                    while j < k and nums[k] == nums[k + 1]:
                        k -= 1
                elif need_ < need:
                    j += 1
                    while j < k and nums[j] == nums[j - 1]:
                        j += 1
                else:
                    return target

        return res
```

>执行用时: **32 ms**，超过了 **100%** 的 **Python** 提交记录。
>
>内存消耗: **15.2 MB**，超过了 **14%** 的 **Python** 提交记录。

最后，100%  真好看。😎

---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/lc15/  

