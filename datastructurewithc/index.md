# 使用C语言学习算法及数据结构

<!--more-->

{{<admonition tip>}}

按照去年考研翻车的经验，编程能力的欠缺是非常严重的问题。期望通过将LeetCode中的问题与考研大纲中所考内容对照性练习，来提高编程能力。编程语言选择C语言，更为通用。

{{</admonition>}}

## 1. 算法设计部分

### 1.1 递归

---

#### [LC50 Pow(x, n)](https://leetcode.cn/problems/powx-n/)

一个比较笨的写法，但是思想上是没有什么大问题的：

```C
double myPow(double x, int n) {
    double temp;

    double Pow(double x, int n) {
        if (n != 1) {
            if (n % 2 != 0) {
                temp *= x;
            }
            return Pow(x * x, n / 2);
        } else {
            return x * temp;
        }
    }

    if (n < -1) {
        x = 1 / x;
        if (n % 2 != 0) {
            temp = x;
        }
        return Pow(x * x, -(n / 2));	// 规避int取负超出转范围
    } else if (n == 0) {
        return 1;
    } else if (n == -1) {
        return 1 / x;
    } else {
        temp = 1;
        return Pow(x, n);
    }
}
```

简练风格：

```C
double myPow(double x, int n) {

    double Pow(double x, int n) {
        if (n != 0) {
            return  n % 2 == 0 ? Pow(x * x, n / 2) : Pow(x * x, n / 2) * x;
        } else {
            return 1.0;
        }
    }

    return n >= 0 ? Pow(x, n) : n % 2 == 0 ? Pow(1 / (x * x), -(n / 2)) : Pow(1 / (x * x), -(n / 2)) / x;
}
```

{{<admonition warning>}}

今日吃瘪：

1. 先动脑子后动手。
2. 用C语言尽量避免在所给函数外编程，否则容易造成**本地测试结果和提交测试结果不同**！！！
3. 注意C语言int数据类型的范围。

{{</admonition>}}

#### [剑指 Offer 65](https://leetcode.cn/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

代码：

```C
int xor, carry;

int add(int a, int b) {
    xor = a ^ b;
    carry = (unsigned int)(a & b) << 1;
    if (carry != 0) {
        return add(xor, carry);
    } else {
        return xor;
    }
}
```

简练风格：

```C
int add(int a, int b) {
    return b == 0 ? a : add(a ^ b, (unsigned int)(a & b) << 1);
}
```

解题思路：

考虑一般加法过程219 + 101，最低位9 + 1 = 10，发生进位。原本加法过程可以写为无进位和 + 进位值，即219 + 101 = 310（无进位和）+ 010（进位值）= 320。考虑二进制加法过程同理，可以每次求出无进位和与进位值，并相加。

对二进制而言，无进位和即为XOR得到的值，即相同为0，相异为1；进位值为AND，即仅当两数均为1时，发生进位，因此二进制加法可以表示为：**a + b = (a ^ b) + ((a & b) << 1)**。注意第二项((a & b) << 1)，由于是AND和左移运算，因此该项从最低位到第一个非零位的长度必然大于b。因此如果下一步令a = (a ^ b)，b = ((a & b) << 1)，并重复此过程，那么在有限步数内b终将等于0，此时a + b的值即等于a。

{{<admonition warning>}}

今日吃瘪：C对int类型的左移会有数据范围的限制，当超出int数据表示的范围会报错显示，这时需要考虑unsigned int类型（闭嘴给我算就是了！）。

{{</admonition>}}

#### [面试题 08.05. 递归乘法](https://leetcode.cn/problems/recursive-mulitply-lcci/)

代码：

```C
int multiply(int A, int B) {
    return B == 1 ? A : B % 2 == 0 ? multiply(A + A, B / 2) : multiply(A + A, B / 2) + A;
}
```

#### [剑指 Offer 51. 数组中的逆序对](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

注意到数组长度最长为50000，考虑到这是一道hard题，暴力解$\mathcal{O}(n^2)$八成会TLE。

**归并排序**解题代码：

```C
int mergeSort(int l, int r, int* nums, int* tmp) {
    // 退出条件
    if (l >= r) {
        return 0;
    }

    // 递归划分
    int m = (l + r) / 2;
    int res = mergeSort(l, m, nums, tmp) + mergeSort(m + 1, r, nums, tmp);

    // 合并阶段
    int i = l, j = m + 1;
    for (int k = l; k <= r; k++) {
        tmp[k] = nums[k];
    }
    for (int k = l; k <= r; k++) {
        if (i == m + 1) {
            nums[k] = tmp[j++];
        } else if (j == r + 1 || tmp[i] <= tmp[j]) {
            nums[k] = tmp[i++];
        } else {
            nums[k] = tmp[j++];
            res += m - i + 1;
        }
    }
    return res;
}

int reversePairs(int* nums, int numsSize){
    int *tmp = malloc(sizeof(int) * numsSize);
    return mergeSort(0, numsSize - 1, nums, tmp);
}
```

#### [剑指 Offer II 076. 数组中的第 k 大的数字](https://leetcode.cn/problems/xx4gT2/)

归并排序代码：

```C
int mergeSort(int l, int r, int *nums, int *tmp) {
    if (l >= r) {
        return 0;
    }

    int m = (l + r) / 2;
    mergeSort(l, m, nums, tmp);
    mergeSort(m + 1, r, nums, tmp);

    for (int k = l; k <= r; k++) {
        tmp[k] = nums[k];
    }
    int i = l, j = m + 1;
    for (int k = l; k <= r; k++) {
        if (i == m + 1) {
            nums[k] = tmp[j++];
        } else if (j == r + 1 || tmp[i] > tmp[j]) {
            nums[k] = tmp[i++];
        } else {
            nums[k] = tmp[j++];
        }
    }
    return 0;
}

int findKthLargest(int* nums, int numsSize, int k){
    int *tmp = malloc(sizeof(int) * numsSize);
    mergeSort(0, numsSize - 1, nums, tmp);
    return nums[k - 1];
}
```

归并排序表现不佳，用时12ms。

C语言内置函数qsort可以快速排序列表，声明如下：

```C
void qsort(void *base, size_t nitems, size_t size, int (*compar)(const void *, const void*))
```

参数：

- base -- 要进行排序的列表；
- nitems -- 由base指向的数组中元素的个数；
- size -- 数组中每个元素的大小，以字节为单位；
- compar -- 用于比较两个元素的函数。

借用内置函数qsort，快速排序代码如下：

```C
int cmp(const void *a, const void *b) {
    return *(int*)b - *(int*)a;
}

int findKthLargest(int* nums, int numsSize, int k){
    qsort(nums, numsSize, sizeof(int), cmp);
    return nums[k - 1];
}
```

耗时为8ms。仍有提升空间。

#### 计算右侧小于当前元素的个数

#### 翻转对

#### 最大子序和

#### 合并两个有序数组

#### 反转链表

#### 移除链表元素

#### 两两交换链表中的节点

#### 重排链表

#### 反转链表II

#### 从前序与中序遍历序列构造

#### 从中序与后序遍历序列构造

#### 将有序数组转换为二叉搜索树

#### 验证二叉搜索树

#### 二叉树的最大深度

#### 平衡二叉树

#### 二叉树中的最大路径和

#### 二叉树的右视图

#### 正则表达式匹配

#### 外观数列

### 1.2 分治策略



### 1.3 贪心算法



### 1.4 动态规划



### 1.5 线性规划



### 1.6 网络流



## 2 内部排序

### 2.1 排序的基本概念



### 2.2 插入排序



### 2.3 起泡排序



### 2.4 简单选择排序



### 2.5 希尔排序



### 2.6 快速排序



### 2.7 堆排序



### 2.8 二路归并排序

代码：

```C
void mergeSort_(int l, int r, int *nums, int *tmp) {
    if (l >= r) {
        return;
    }
    
    int m = (l + r) / 2;
    mergeSort(l, m, nums, tmp);
    mergeSort(m + 1, r, nums, tmp);
    
    for (int k = l; k <= r; k++) {
        tmp[k] = nums[k];
    }
    
    int i = l, j = m + 1;
    for (int k = l; k <= r; k++) {
        if (i == m + 1) {
            nums[k] = tmp[j++];
        } else if (j == r + 1 || tmp[i] <= tmp[j]) {
            nums[k] = tmp[i++];
        } else {
            nums[k] = tmp[j++];
        }
    }
}

void mergeSort(int *nums, int numsSize) {
    int *tmp = malloc(sizeof(int) * numsSize);
    mergeSort_(0, numsSize - 1, nums, tmp);
}
```

时间复杂度：$\mathcal{O}(nlogn)$

空间复杂度：$\mathcal{O}(n)$

稳定性：稳定

### 2.9 基数排序



### 2.10 各种内部排序算法的分析和比较

 



---

> 作者: Blathers  
> https://blathers23.netlify.app/datastructurewithc/
