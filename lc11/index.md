# LC11 - 盛水最多的容器


<!--more-->

> 问题链接：[盛水最多的容器](https://leetcode.cn/problems/container-with-most-water/)

## 思考

---

易得：面积公式为 $S = (r - l) \times min(height[r], height[l])$，$r$ 和 $l$ 分别为所选择两条线的序号。最容易想到的解决方式即为双循环，遍历所有可能的 $r$ 和 $l$ 的组合。

## 代码

---

```python
class Solution:
	def maxArea(self, height: List[int]) -> int:
	maxS = 0
	for r in range(1, len(height)):
		for l in range(0, len(height)):
			maxS = max(maxS, (r - l) * min(height[r], height[l]))
        
	return maxS
```

>  简单双循环代码，没有什么可解释的。

然而对于一道中等难度的题来说，这种解法实在是有些不能令人满意。实际上这段代码运行后会超时，Time Limit Exceeded，喜闻乐见的 TLE。

当然，我不会这么简单就放弃（这么简单的算法还是早点放弃得了）。将语言换成 C 后，代码如下：

```c
int maxArea(int* height, int heightSize){
    int maxS = 0;
    for (int r = 1; r < heightSize; r++) {
        bool Higher = false;
        int Highest = height[0];
        for (int l = 0; l < heightSize; l++) {
            if (height[l] <= height[r]) {
                if (height[l] >= Highest) {
                    maxS = fmax(maxS, (r - l) * fmin(height[l], height[r]));
                    Highest = height[l];
                }
            } else {
                if (!Higher) {
                    maxS = fmax(maxS, (r - l) * fmin(height[l], height[r]));
                    Higher = true;
                }
            }
        }
    }   
    return maxS;
}
```

> 代码优化。

相较于 Python 的代码，这里又进行了一些优化。注意到在内循环（也即 $l$ 循环）的过程中，$l$ 值不断变大，意味着公式中 $(r - l)$ 的部分会不断变小。这里分为两个情况：

1. $height[l]$ 比 $height[r]$ 更低：

   此时容器的盛水量由 $height[l]$ 决定而不是 $height[r]$ 。当 $(r - l)$ 不断变小，这意味着我们需要找到更大的 $height[l]$ 才有可能计算处更大的盛水量 $S$。因此在内循环过程中，记录所碰到的 $height[l]$ 的最大值，仅当当前 $height[l]$ 值比历史最大值大时，才进行盛水量的计算，否则直接跳过这次循环。

2. $height[l]$ 比 $height[r]$ 高：

   此时容器的盛水量由 $height[r]$ 决定，在内循环过程中，当 $l$ 不断变大，即 $(r - l)$ 不断变小，盛水量 $S$ 必定为不断减少。因此当 $height[l] > height[r]$ 时，一次内循环仅需要计算第一次的盛水量，因为第二次、第三次等以后次数的盛水量必定小于第一次的盛水量。

在这样的优化条件下，代码依旧碰上了喜闻乐见的 TLE。事实证明 $\mathcal{O}(n^2)$ 的算法在这种拙劣的优化下没有什么用处，该 $\mathcal{O}(n^2)$ 还是 $\mathcal{O}(n^2)$。

## 双指针解法

---

换个思路。双重循环实际上就是双指针，右指针每次向右移动一位，而左指针每次从开始位置移动到右指针所在位置。这带来一个问题，注意到 $S = (r - l) \times min(height[r], height[l])$ ，在内循环过程中，第一项始终不断变小，而当外循环每移动一次，第一项又会变大，这就导致我们需要重新将 $r$ 放置到初始位置，重新计算所有的可能结果。有没有一种方法能够让 $(r - l)$ 不断变小呢？这样我们只需要考虑 $min(height[r], height[l])$ 带来的变化。

考虑一个通常的情况：$l$ 和 $r$ 处在不同的位置并且相距一定的距离（注意到 $l$ 一定是小于 $r$ 的）。我们假设 $height[l]$ 比 $height[r]$ 更高。那么此时，我们的盛水量由 $height[r]$ 决定的。为了寻求更大的盛水量，我们有四种做法：

1. 向右移动 $r$: 

   这样我们的 $(r - l)$ 会增大，并且有可能遇到更大的 $height[r]$ ，但若是碰到更小的$height[r]$ ，也需要一些计算，**有利有弊**。

2. 向左移动 $r$: 

   这样我们的 $(r - l)$ 会减小，但可以去寻求更大的 $height[r]$，**有利有弊**。

3. 向右移动 $l$: 

   这样我们的 $(r - l)$ 会减少，就算碰到更大的 $height[l]$ 也会因为 $height[r]$ 的限制在，**不管怎么样都是亏**。

4. 向左移动 $l$: 

   这样我们的 $(r - l)$ 会增大，但是可能会碰到更小的 $height[l]$ ，**有利有弊**。

到这一步，应该考虑那种走法？注意到以上四种走法中，有一种走法比较特殊，即**向右移动 $l$**，也可以称为没有前途。

如果我们将 $l$ 值减1（即向右移动 $l$），那么 $(r - l)$ 的值会减小，$min(height[r], height[l])$ 的值也只会降低或不变，而不会增加（我们假设了 $height[l]$ 比 $height[r]$ 高）。

**有趣的地方来了：如果我们在 $l$ 和 $r$ 相遇以前，不断地重复这个步骤，那么计算得到的 $S$ 肯定不会高于没有移动 $l$ 时的 $S$ ！**

事实上，这个结果就是我们之前导出结论的强化：参见本文**代码章节优化部分，第二个情况， $height[l]$ 比 $height[r]$ 高**。这样一来，意味着我们可以跳过本轮内循环，开始下一轮内循环。这是因为就算我们继续计算下去，$S$ 也只会变低而不会变高。

如果按照往常（双循环）的思路，接下来我们应该向右移动 $r$ ，当然这又会导致上述的问题。这里我们换一个思路：这里我们已经计算出在 $[l, r]$ 区间内固定住 $r$ 后，$l$ 在所有不同位置处时 $S$ 的最大值，也就是我们已经考虑完了 $r$ 对 $l \in [l, r - 1]$ 所有的可能性，进一步的，当 $r$ 和 $l$ 分别是**列表的两个端点**时，我们就**不需要再考虑更多的问题**。换句话说，为了穷举所有的可能性，我们现在只需要计算 $[l, r - 1]$ 这个区间中所有的可能 $S$。也就是说，我们**仅仅通过一次比较和计算，就让输入列表的长度从 $n$ 下降到了 $n-1$**。 当然你可能会问，这样做的前提是我们的假设 **$height[l]$ 比 $height[r]$ 更高**啊？事实上，当 $height[l]$ 比 $height[r]$ 低时，我们得到的新列表为$[l + 1, r]$ 。

那么聪明如你肯定就已经明白了问题解决的办法，只要我们不断重复上面的操作，我们就可以在 $\mathcal{O}(n)$ 列举出**所有的可能的 $S$**，并且通过比较得出最终结果了。

> 本段描写更加偏向于对双指针思考的思路和证明，比较抽象。更为具体的描述相信网上随处可见。

> 事实上我认为双指针从中间往两边扩散也是可行的？那么扩散的初试条件是什么？这个有待进一步思考。

## 双指针解法代码

---

```c
int maxArea(int* height, int heightSize){
    int minH = 0;
    int maxS = 0;
    int maxS_ = 0;
    int l = 0;
    int r = heightSize - 1;
    int maxL = height[l];
    int maxR = height[r];

    while (r > l) {
        if (maxL > maxR) {		// 两数之中取其小
            minH = maxR;
        } else {
            minH = maxL;
        }
        maxS_ = (r - l) * minH;	// 临时盛水量
        if (maxS_ > maxS) {		// 两数之中取其大
            maxS = maxS_;
        }
        while (r > l){
            if (height[r] >= height[l]) {
                l++;
                if (height[l] > maxL) {
                    maxL = height[l];
                    break;
                } 
            } else {
                r--;
                if (height[r] > maxR) {
                    maxR = height[r];
                    break;
                }
            }
        }
    }
    return maxS;
}
```

这里同样引入了一个小小改进。在 $(r-l)$ 不断减小的情况下，只有更大的 $height$ 才可能计算出更大的 $S$。因此在运行过程中记录碰到的最大的 $height[l]$ 和 $height[r]$，通过引入比较，来进一步减少计算的次数。同样的，这是本文**代码章节优化部分，第一个情况， $height[l]$ 比 $height[r]$ 低**部分思路的引用。

在代码中，我最开始采用了内置函数 `fmax` 和 `fmin` ，无论执行用时和内存消耗都很高。自定义 `max` 函数可以减少一部分内存消耗，但是最佳的做法还是简单 `if` 语句。

最终结果为：

> 执行用时: **76 ms**，超过了 **88%** 的 **c** 提交记录。
>
> 内存消耗: **11.4 MB**，超过了 **67%** 的 **c** 提交记录。

## 结尾碎碎念

---

现在时间是2:37，原本只想随便写写，结果越写越多。这同样是一个梳理自己思路的过程，很多不清晰的事情，如果不加以思考，是很难落笔的。第一次写关于技术细节的文章，只能说比写浅入浅出教程有意义多了。

下一步学学怎么画图，图解还是非常重要的。有时间把 LC10 正则匹配也记录一下，用到了动态规划，也非常有用。

夜深了，该洗洗衣服睡觉了。

---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/lc11/  

