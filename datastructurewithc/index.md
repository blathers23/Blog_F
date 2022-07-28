# 使用C语言学习算法及数据结构

<!--more-->

{{<admonition tip>}}

按照去年考研翻车的经验，编程能力的欠缺是非常严重的问题。期望通过将LeetCode中的问题与考研大纲中所考内容对照性练习，来提高编程能力。编程语言选择C语言，更为通用。

{{</admonition>}}

## 1. 算法设计部分

### 1.1 递归

---

#### [LC 50. Pow(x, n)](https://leetcode.cn/problems/powx-n/)

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

#### [剑指 Offer 65. 不用加减乘除做加法](https://leetcode.cn/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

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

C语言内置函数**qsort**可以快速排序列表，声明如下：

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

堆排序解法：

```C
void maxHeapify(int* a, int i, int heapSize) {
    int l = i * 2 + 1, r = i * 2 + 2, largest = i;
    if (l < heapSize && a[l] > a[largest]) {
        largest = l;
    } 
    if (r < heapSize && a[r] > a[largest]) {
        largest = r;
    }
    if (largest != i) {
        int t = a[i];
        a[i] = a[largest], a[largest] = t;
        maxHeapify(a, largest, heapSize);
    }
}

void buildMaxHeap(int* a, int heapSize) {
    for (int i = heapSize / 2; i >= 0; --i) {
        maxHeapify(a, i, heapSize);
    }
}

int findKthLargest(int* nums, int numsSize, int k) {
    int heapSize = numsSize;
    buildMaxHeap(nums, heapSize);
    for (int i = numsSize - 1; i >= numsSize - k + 1; --i) {
        int t = nums[0];
        nums[0] = nums[i], nums[i] = t;
        --heapSize;
        maxHeapify(nums, 0, heapSize);
    }
    return nums[0];
}
```

耗时为0ms。

手写快速排序解法：

```C
inline int partition(int* a, int l, int r) {
    int x = a[r], i = l - 1;
    for (int j = l; j < r; ++j) {
        if (a[j] <= x) {
            int temp = a[++i];
            a[i] = a[j];
            a[j] = temp;
        }
    }
    int temp = a[i + 1];
    a[i + 1] = a[r];
    a[r] = temp;
    return i + 1;
}

inline int randomPartition(int* a, int l, int r) {
    int i = rand() % (r - l + 1) + l;
    int temp = a[i];
    a[i] = a[r];
    a[r] = temp;
    return partition(a, l, r);
}

int quickSelect(int* a, int l, int r, int index) {
    int q = randomPartition(a, l, r);
    if (q == index) {
        return a[q];
    } else {
        return q < index ? quickSelect(a, q + 1, r, index)
            			 : quickSelect(a, l, q - 1, index);
    }
}

int findKthLargest(int* nums, int numsSize, int k) {
    srand(time(0));		// 初始化随机种子
    return quickSelect(nums, 0, numsSize - 1, numsSize - k);
}
```

耗时为4ms。

#### [LC 315. 计算右侧小于当前元素的个数](https://leetcode.cn/problems/count-of-smaller-numbers-after-self/)

思路：

- 倒置输入整数数组名为I，新建排序数组名为S和结果数组R。进入循环。
	- 若输入数组I被取尽，则退出循环
	- 每次循环从输入数组I中取出一个数
	- 使用二分查找查询应当插入S的位置，插入的位置即为右侧小于当前元素的个数，保存至R。
	- 将数字插入S。
- 返回R。

这样可以控制时间复杂度在$\mathcal{O}(nlogn)$。

代码：

```C
int binarySearch(int* nums, int l, int r, int target) {
    if (l < r) {
        int m = (l + r) / 2;
        if (nums[m] == target) {
            while(nums[m - 1] == target && m > l){
                m -= 1;
            }
            return m;
        } else if (nums[m] > target) {
            return binarySearch(nums, l, m, target);
        } else {
            return binarySearch(nums, m + 1, r, target);
        }
    } else {
        return l;
    }
}

int insert(int* nums, int l, int index) {
    int i, num = nums[l];
    for (i = l; i < l + index; i++) {
        nums[i] = nums[i + 1];
    };
    nums[i] = num;
    return 0;
}

int* countSmaller(int* nums, int numsSize, int* returnSize){
    int* res = malloc(sizeof(int) * numsSize), index;
    *returnSize = numsSize;
    res[numsSize - 1] = 0;

    for (int i = numsSize - 1; i > 0; i--) {
        if (i != numsSize - 1 && nums[i] == nums[i + 1]) {
            index = res[i];
        } else {
            index = binarySearch(nums, i, numsSize, nums[i - 1]);
        }
        insert(nums, i - 1, index - i);
        res[i - 1] = index - i;
    } 

    // for (int i = 0; i < numsSize; i++) {
    //     printf("%d,", nums[i]);
    // }

    return res;
}
```

理论上应该是可以的，但是顶不住常数大。最后TLE，麻了。

**题太难先摸了罢。**

#### [LC 493. 翻转对](https://leetcode.cn/problems/reverse-pairs/)

Hard润了

#### [LC 53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

代码：

```C
int maxSubArray(int* nums, int numsSize){
    int max = nums[0], start = nums[0], cur = nums[0], i = 1;

    while (i < numsSize) {
        cur += nums[i];
        if (start < 0 && nums[i] > start) {	// 逐i去掉负贡献数字
            cur = nums[i];
            start = nums[i];
        } else if (cur < start && cur < 1 && i + 1 < numsSize) { // 逐组去掉负贡献数字
            cur = nums[i + 1];
            start = nums[i + 1];
            i += 1;
        }
        if (cur > max) {
            max = cur;
        }
        i += 1;
    }
    return max;
}
```

{{<admonition warning>}}

不知道我是怎么想的😀

{{</admonition>}}

代码简化：

```C
int maxSubArray(int* nums, int numsSize){
    int subsum = 0, max = nums[0];
    for (int i = 0; i < numsSize; i++) {
        subsum = fmax(subsum + nums[i], nums[i]);
        max = fmax(max, subsum);
    }
    return max;
}
```

#### [LC 88. 合并两个有序数组](https://leetcode.cn/problems/merge-sorted-array/)

简单的一道二路归并。代码：

```C
void merge(int* nums1, int nums1Size, int m, int* nums2, int nums2Size, int n){
    int* nums0 = malloc(sizeof(int) * m);
    for (int i = 0; i < m; i++) {
        nums0[i] = nums1[i];
    }
    int i = 0, j = 0;

    while (i < m && j < n) {
        if (nums0[i] <= nums2[j]) {
            nums1[i + j] = nums0[i];
            i += 1;
        } else {
            nums1[i + j] = nums2[j];
            j += 1;
        }
    }

    if (i == m) {
        for (; j < n; j++) {
            nums1[i + j] = nums2[j];
        }
    } else {
        for (; i < m; i++) {
            nums1[i + j] = nums0[i];
        }
    }
}
```

#### [剑指 Offer 24. 反转链表](https://leetcode.cn/problems/fan-zhuan-lian-biao-lcof/)

代码：

```C
struct ListNode* reverseList(struct ListNode* head) {
    struct ListNode* prev = NULL;
    struct ListNode* curr = head;
    while (curr) {
        struct ListNode* next = curr -> next;
        curr -> next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
}
```

递归代码：

```C
struct ListNode* reverseList(struct ListNode* head) {
    if (head == NULL || head -> next == NULL) {
        return head;
    }
    struct ListNode* newHead = reverseList(head -> next);
    head -> next -> next = head;
    head -> next = NULL;
    return newHead;
}
```

#### [LC 203. 移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/)

代码：

```C
struct ListNode* removeElements(struct ListNode* head, int val){
    struct ListNode* res = malloc(sizeof(struct ListNode));
    res -> next = head;
    struct ListNode* curr = res;
    struct ListNode* next = curr -> next;
    
    while(next) {
        if (next -> val == val) {
            next = next -> next;
            curr -> next = next;
        } else {
            curr = curr -> next;
            next = curr -> next;
        }
    }
    return res -> next;
}
```

递归代码：

```C
struct ListNode* removeElements(struct ListNode* head, int val){
    if (head == NULL) {
        return head;
    }
    
    head -> next = removeElements(head -> next, val);
    return head -> val == val ? head -> next : head;
}
```

#### [LC 24. 两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

递归代码：

```C
struct ListNode* swapPairs(struct ListNode* head){
    if (head == NULL || head -> next == NULL) {
        return head;
    }

    struct ListNode* next = head -> next;
    head -> next = swapPairs(next -> next);
    next -> next = head;
    return next;
}
```

{{<admonition success>}}

递归也太好用了

{{</admonition>}}

#### [剑指 Offer II 026. 重排链表](https://leetcode.cn/problems/LGjMqU/)

代码：

```C
struct ListNode* findMid(struct ListNode* head) {
    struct ListNode* f = head;
    struct ListNode* s = head;
    while (f && f -> next) {
        f = f -> next -> next;
        s = s -> next;
    }
    return s;
}

struct ListNode* reverseList(struct ListNode* curr) {
    struct ListNode* next;
    struct ListNode* prev = NULL;

    while (curr) {
        next = curr -> next;
        curr -> next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
}

void merge(struct ListNode* l1, struct ListNode* l2) {
    struct ListNode* l1_next;
    struct ListNode* l2_next;

    while (l2) {
        l1_next = l1 -> next;
        l2_next = l2 -> next;

        l1 -> next = l2;
        l1 = l1_next;

        l2 -> next = l1; 
        l2 = l2_next;
    }
}

void reorderList(struct ListNode* head){
    // 寻找中间节点
    struct ListNode* mid = findMid(head);
    struct ListNode* l1 = head;
    struct ListNode* l2 = mid -> next;
    mid -> next = NULL;
    // 反转链表
    l2 = reverseList(l2);
    // 合并链表
    merge(l1, l2);
}
```

#### [LC 92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/)

#### [LC 105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

#### [LC 106. 从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

#### [LC 108. 将有序数组转换为二叉搜索树](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)

#### [LC 98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

#### [剑指 Offer 55 - I. 二叉树的深度](https://leetcode.cn/problems/er-cha-shu-de-shen-du-lcof/)

#### [剑指 Offer 55 - II. 平衡二叉树](https://leetcode.cn/problems/ping-heng-er-cha-shu-lcof/)

#### [剑指 Offer 34. 二叉树中和为某一值的路径](https://leetcode.cn/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

#### [剑指 Offer II 046. 二叉树的右侧视图](https://leetcode.cn/problems/WNC0Lk/)

#### [剑指 Offer 19. 正则表达式匹配](https://leetcode.cn/problems/zheng-ze-biao-da-shi-pi-pei-lcof/)

Hard动态规划，开摆。

#### [LC 38. 外观数列](https://leetcode.cn/problems/count-and-say/)

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

代码：

```C
void maxHeapify(int* nums, int i, int heapSize) {
    int l = i * 2 + 1, r = i * 2 + 2, largest = i;
    if (l < heapSize && nums[l] > nums[largest]) {
        largest = l;
    }
    if (r < heapSize && nums[r] > nums[largest]) {
        largest = r;
    }
    if (largest != i) {
        int temp = nums[i];
        nums[i] = nums[largest];
        nums[largest] = temp;
    }
}

void buildMaxHeap(int* nums, int heapSize) {
    for (i = heapSize / 2; i >= 0; --i) {
        maxHeapify(nums, i, heapSize);
    }
}

void Sort(int* nums, int numsSize) {
    int temp, heapSize = numsSize;
    for (i = numsSize; i > 0; --i) {
        temp = nums[0];
    	nums[0] = nums[temp];
    	nums[heapSize - 1] = temp;
    	heapSize -= 1;
        
		maxHeapify(nums, 0, heapSize - 1);   
    }
}
```

时间复杂度：$\mathcal{O}(nlogn)$

空间复杂度：递归实现$\mathcal{O}(logn)$、非递归实现$\mathcal{O}(1)$

稳定性：不稳定

### 2.8 二路归并排序

代码：

```C
void mergeSort(int l, int r, int *nums, int *tmp) {
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

void Sort(int *nums, int numsSize) {
    int *tmp = malloc(sizeof(int) * numsSize);
    mergeSort(0, numsSize - 1, nums, tmp);
}
```

时间复杂度：$\mathcal{O}(nlogn)$

空间复杂度：$\mathcal{O}(n)$

稳定性：稳定

### 2.9 基数排序

### 2.10 各种内部排序算法的分析和比较

### 2.11 内部排序算法及应用

## 3 查找

### 3.1 查找的基本概念

### 3.2 顺序查找及其性能分析

### 3.3 折半查找及其性能分析

### 3.4 二叉排序树及其性能分析

### 3.5 平衡二叉树定义及其转换方法

### 3.6 B-树及其基本操作、B+树的基本概念

### 3.7 散列（hash）表构造及其查找方法

### 3.8 常用查找算法的分析及应用

## 4 图

### 4.1 图的基本概念和术语

### 4.2 图的存储结构和基本操作

### 4.3 图的遍历

### 4.4 图的基本应用

## 5 树与二叉树

### 5.1 树的概念

### 5.2 二叉树

### 5.3 树、森林

## 6 数组与广义表

### 6.1 多维数组的存储及数组元素的地址计算方法

### 6.2 特殊矩阵（三角矩阵、对称矩阵、多对角矩阵等）的压缩存储方法

### 6.3 稀疏矩阵概念及存储方法

### 6.4 广义表定义及存储方法

## 7 栈、队列

### 7.1 栈和队列的基本概念

### 7.2 栈和队列的顺序存储结构及操作实现

### 7.3 栈和队列的链式存储结构及操作实现

### 7.4 栈和队列的应用及其算法性能分析

## 8 线性表

### 8.1 线性表的定义和基本操作

### 8.2 线性表的实现

## 9 绪论

- 数据结构的基本概念
- 数据结构的分类
- 数据类型和抽象数据类型
- 逻辑结构、物理结构的概念
- 算法的定义和特性
- 算法的设计目标
- 算法的时间、空间复杂度的概念
- 算法的时间、空间复杂的分析方法







 



---

> 作者: Blathers  
> https://blathers23.netlify.app/datastructurewithc/
