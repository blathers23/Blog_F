# 使用C语言学习算法及数据结构

{{<admonition tip>}}

编程能力的欠缺是非常严重的问题。期望通过将LeetCode中的问题与考研大纲中所考内容对照性练习，来提高编程能力。编程语言选择C语言，更为通用。

{{</admonition>}}

<!--more-->

{{<admonition success>}}

考试结束，算法题做的算是比较顺利，可以说今年刷的100道LC劳苦功高。重新组织了一下文章的结构，删去了一些不常见的内容，这篇文章权当存档 。(Dec/31/2022)

{{</admonition>}}

{{<image src = "https://s2.loli.net/2022/12/31/GLeryt29PuNAxFI.png" caption = "LC年度总结" title = "LC年度总结" height = "50%" width = "50%">}}

## 1.二叉树

---

### 1.1 二叉树的层次遍历-迭代算法

代码：

```C
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
int** levelOrder(struct TreeNode* root, int* returnSize, int** returnColumnSizes){
    *returnSize = 0;
    if (root == NULL) {
        return NULL;
    }
    
    *returnColumnSizes = (int *)malloc(sizeof(int) * 1000);	// 二叉树的最大深度
    int** ans = (int **)malloc(sizeof(int *) * 1000);		// 二叉树的最大深度
    struct TreeNode *queue[2000], *curNode;					// 二叉树的节点数量
    int head = 0, tail = 0, curLevelTail, curLevelNodeNum;
    
    queue[tail++] = root;
    
    while (head != tail) {
        curLevelTail = tail;
        curLevelNodeNum = 0;
        ans[*returnSize] = (int *)malloc(sizeof(int) * (tail - head));
        
        while(head != curLevelTail) {
            curNode = queue[head++];
            ans[*returnSize][curLevelNodeNum++] = curNode -> val;
            
            if (curNode -> left) {
                queue[tail++] = curNode -> left;
            }
            if (curNode -> right) {
                queue[tail++] = curNode -> right;
            }
        }
        (*returnColumnSizes)[(*returnSize)++] = curLevelNodeNum;
    }
    
    return ans;
}
```

### 1.2 二叉树前序遍历-递归算法

代码：

```C
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* preorderTraversal(struct TreeNode* root, int* returnSize){
    int *ans = malloc(sizeof(int) * 100);
    *returnSize = 0;
    
    void preOrder(struct TreeNode* node) {
        if (node) {
            ans[(*returnSize)++] = node -> val;
            preOrder(node -> left);
            preOrder(node -> right);
        }
    }
    
    preOrder(root);
    return ans;
}
```

### 1.3 二叉树前序遍历-迭代算法

代码：

```C
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* preorderTraversal(struct TreeNode* root, int* returnSize){
    int *ans = malloc(sizeof(int) * 100);
    *returnSize = 0;
    if (!root) {
        return NULL;
    }

    struct TreeNode** stack = malloc(sizeof(struct TreeNode*) * 100);
    // struct TreeNode* stack[100];
    int stackTop = 0;

    while (stackTop > 0 || root != NULL) {
        while (root != NULL) {
            ans[(*returnSize)++] = root -> val;
            stack[stackTop++] = root;
            root = root -> left;
        }
        root = stack[--stackTop];
        root = root -> right;
    }

    return ans;
}
```

### 1.4 二叉树的中序遍历-递归算法

代码：

```C
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* inorderTraversal(struct TreeNode* root, int* returnSize){
    int *ans = malloc(sizeof(int) * 100);
    *returnSize = 0;

    void inOrder(struct TreeNode* node) {
        if (node) {
            inOrder(node -> left);
            ans[(*returnSize)++] = node -> val;
            inOrder(node -> right);
        }
    }

    inOrder(root);
    return ans;
}
```

### 1.5 二叉树的中序遍历-迭代算法

代码：

```C
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* inorderTraversal(struct TreeNode* root, int* returnSize){
    int *ans = malloc(sizeof(int) * 100);
    *returnSize = 0;

    if (!root) {
        return NULL;
    }

    struct TreeNode** stack = malloc(sizeof(struct TreeNode *) * 100);
    int stackTop = 0;

    while (stackTop > 0 || root != NULL) {
        while (root != NULL) {
            stack[stackTop++] = root;
            root = root -> left;
        }
        root = stack[--stackTop];
        ans[(*returnSize)++] = root -> val;
        root = root -> right;
    }

    return ans;
}
```

### 1.6 二叉树后序遍历-递归算法

代码：

```C
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* postorderTraversal(struct TreeNode* root, int* returnSize){
    int* ans = malloc(sizeof(int) * 100);
    *returnSize = 0;

    void postOrder(struct TreeNode* node) {
        if (node) {
            postOrder(node -> left);
            postOrder(node -> right);
            ans[(*returnSize)++] = node -> val;
        }
    }

    postOrder(root);
    return ans;
}
```

### 1.7 二叉树后序遍历-迭代算法

代码：

```c
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* postorderTraversal(struct TreeNode* root, int* returnSize){
    int* ans = malloc(sizeof(int) * 100);
    *returnSize = 0;

    if (!root) {
        return NULL;
    }

    struct TreeNode** stack = malloc(sizeof(struct TreeNode *) * 100);
    struct TreeNode* prev = NULL;
    int stackTop = 0;

    while (stackTop > 0 || root != NULL) {
        while (root != NULL) {
            stack[stackTop++] = root;
            root = root -> left;
        }

        root = stack[--stackTop];

        if (root -> right == NULL || root -> right == prev) {	// 如果节点无右孩子或者右孩子已经被输出，则输出该节点
            ans[(*returnSize)++] = root -> val;
            prev = root;
            root = NULL;
        } else {	// 将节点重新加入栈，并访问节点的右孩子
            stack[stackTop++] = root;
            root = root -> right;
        }
    }

    return ans;
}
```

## 2.排序

---

### 2.1 快速排序

代码：

```c
int partition(int* nums, int i, int j) {
    int key = nums[i];
    while (i < j) {
        while (i < j && nums[j] <= key) j--;
        nums[i] = nums[j];
        while (i < j && nums[i] >= key) i++;
        nums[j] = nums[i]; 
    }
    nums[i] = key;
    return i;
}

void quickSort(int* nums, int l, int r) {
    if (l >= r) return;
    int m = partition(nums, l, r);
    quickSort(nums, l, m - 1);
    quickSort(nums, m + 1, r);
}

void Sort(int* nums, int numsSize) {
    quickSort(nums, 0, numsSize - 1);
}
```

时间复杂度：$\mathcal{O}(nlogn)$

空间复杂度：$\mathcal{O}(logn)$

稳定性：不稳定

### 2.2 堆排序

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
        maxHeapify(a, largest, heapSize);
    }
}

void buildMaxHeap(int* nums, int heapSize) {
    for (i = heapSize / 2; i >= 0; --i) {
        maxHeapify(nums, i, heapSize);
    }
}

void Sort(int* nums, int numsSize) {
    int temp, heapSize = numsSize;
    buildMaxHeap(nums, heapSize);
}
```

时间复杂度：$\mathcal{O}(nlogn)$

空间复杂度：递归实现$\mathcal{O}(logn)$、非递归实现$\mathcal{O}(1)$*（847非递归实现）*

稳定性：不稳定

### 2.3 二路归并排序

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

## 3.算法设计

---

### [LC 50. Pow(x, n)](https://leetcode.cn/problems/powx-n/)

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

### [剑指 Offer 65. 不用加减乘除做加法](https://leetcode.cn/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

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

### [面试题 08.05. 递归乘法](https://leetcode.cn/problems/recursive-mulitply-lcci/)

代码：

```C
int multiply(int A, int B) {
    return B == 1 ? A : B % 2 == 0 ? multiply(A + A, B / 2) : multiply(A + A, B / 2) + A;
}
```

### [剑指 Offer 51. 数组中的逆序对](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

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

### [剑指 Offer II 076. 数组中的第 k 大的数字](https://leetcode.cn/problems/xx4gT2/)

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

### [LC 315. 计算右侧小于当前元素的个数](https://leetcode.cn/problems/count-of-smaller-numbers-after-self/)

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

### [LC 493. 翻转对](https://leetcode.cn/problems/reverse-pairs/)

**Hard题，开摆**

### [LC 53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

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

### [LC 88. 合并两个有序数组](https://leetcode.cn/problems/merge-sorted-array/)

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

### [剑指 Offer 24. 反转链表](https://leetcode.cn/problems/fan-zhuan-lian-biao-lcof/)

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

### [LC 203. 移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/)

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

### [LC 24. 两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

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

### [剑指 Offer II 026. 重排链表](https://leetcode.cn/problems/LGjMqU/)

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

### [LC 92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/)

第一思路组合拳，先把需要反转的部分拆出，反转后再装回。代码：

```C
struct ListNode* reverseListNode(struct ListNode* curr) {
    struct ListNode* next;
    struct ListNode* prev = NULL;

    while(curr) {
        next = curr -> next;
        curr -> next = prev;
        prev = curr;
        curr = next;
    }

    return prev;
}

struct ListNode* reverseBetween(struct ListNode* head, int left, int right){
    if (left == right) {
        return head;
    }

    struct ListNode *start, *end = head, *prev, *next;
    for (int i = 1; i < right; i++) {
        if (i == left - 1) {
            prev = end;
        }
        end = end -> next;
    }
    next = end -> next, end -> next = NULL;

    if (left == 1) {
        start = reverseListNode(head);
        head = start;
    } else {
        start = prev -> next, prev -> next = NULL;
        start = reverseListNode(start);
        prev -> next = start;
    }

    struct ListNode* curr = start;
    while (curr -> next) {
        curr = curr -> next;
    }
    curr -> next = next;

    return head;
}
```

第二思路连续拳，走到需要反转的地方就进行反转。代码：

```C
struct ListNode* reverseBetween(struct ListNode* head, int left, int right){
    struct ListNode *dummy = malloc(sizeof(struct ListNode));
    dummy -> next = head;

    struct ListNode *pre = dummy;
    int i = 1;
    for (; i < left; i++) {
        pre = pre -> next;
    }

    struct ListNode *cur = pre -> next;
    struct ListNode *next;

    for (; i < right; i++) {
        next = cur -> next;
        cur -> next = next -> next;
        next -> next = pre -> next;
        pre -> next = next;
    }

    return dummy -> next;
}
```

### [LC 105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

前闭后闭注意退出条件简单递归即可。

```C
struct TreeNode* buildSubTree(int* preorder, int prel, int prer, int* inorder, int inl, int inr) {
    if (prel > prer || inl > inr) {
        return NULL;
    }

    struct TreeNode* root = malloc(sizeof(struct TreeNode));
    root -> val = preorder[prel];

    if (prel < prer) {
        int NodeSize = 0;
        for (int i = inl; preorder[prel] != inorder[i]; i++) {
            NodeSize++;
        }

        root -> left = buildSubTree(preorder, prel + 1, prel + NodeSize, inorder, inl, inl + NodeSize - 1);
        root -> right = buildSubTree(preorder, prel + 1 + NodeSize, prer, inorder, inl + 1 + NodeSize, inr);
    } else {
        root -> left = NULL;
        root -> right = NULL;
    }

    return root;
}

struct TreeNode* buildTree(int* preorder, int preorderSize, int* inorder, int inorderSize){
    struct TreeNode* root = buildSubTree(preorder, 0, preorderSize - 1, inorder, 0, inorderSize - 1);
    return root;
}
```

### [LC 106. 从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

故技重施，顺带做了一些微不足道的简化。

```C
struct TreeNode* buildSubTree(int* inorder, int inl, int inr, int* postorder, int postl, int postr) {
    if (inl > inr || postl > postr) {
        return NULL;
    }

    struct TreeNode* root = malloc(sizeof(struct TreeNode));
    root -> val = postorder[postr];

    int NodeSize = 0;
    for (; inorder[inl + NodeSize] != postorder[postr]; NodeSize++);

    root -> left = buildSubTree(inorder, inl, inl + NodeSize - 1, postorder, postl, postl + NodeSize - 1);
    root -> right = buildSubTree(inorder, inl + NodeSize + 1, inr, postorder, postl + NodeSize, postr - 1);

    return root;
}

struct TreeNode* buildTree(int* inorder, int inorderSize, int* postorder, int postorderSize){
    struct TreeNode* root = buildSubTree(inorder, 0, inorderSize - 1, postorder, 0, postorderSize - 1);
    return root;
}
```

### [LC 108. 将有序数组转换为二叉搜索树](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)

没啥好说的，都在代码里。

```C
struct TreeNode* A2B(int * nums, int left, int right) {
    if (left > right) return NULL;

    int mid = (left + right) / 2;

    struct TreeNode* node = malloc(sizeof(struct TreeNode));
    node -> val = nums[mid];
    node -> left = A2B(nums, left, mid - 1);
    node -> right = A2B(nums, mid + 1, right);
    return node;
}

struct TreeNode* sortedArrayToBST(int* nums, int numsSize){
    return help(nums, 0, numsSize-1);
}
```

### [LC 98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

中序遍历即可。

```C
bool isValidBST(struct TreeNode* root) {
    
    long long pre = LLONG_MIN;

    bool walk(struct TreeNode* root) {
        bool l, r;
        if (root) {
            l = walk(root -> left);
            if (pre < root -> val) {
                pre = root -> val;
            } else {
                return false;
            }
            r = walk(root -> right);
            return l && r;
        } else {
            return true;
    }
}

    return walk(root);
}
```

### [剑指 Offer 55 - I. 二叉树的深度](https://leetcode.cn/problems/er-cha-shu-de-shen-du-lcof/)

二叉树的遍历，依旧没什么好说的。

```C
int maxDepth(struct TreeNode* root){
    int max = 0;
    void walk(struct TreeNode* root, int depth) {
        if (root) {
            walk(root -> right, depth + 1);
            walk(root -> left, depth + 1);
        } else {
            if (depth > max) {
                max = depth;
            }
        }
    }
    walk(root, 0);
    return max;
}
```

### [剑指 Offer 55 - II. 平衡二叉树](https://leetcode.cn/problems/ping-heng-er-cha-shu-lcof/)

对于本题，递归时我们需要两种信息，一种是当前节点的高度，第二种是该节点是否平横。这对于只能返回一个值的C语言来讲相当不友好。但是注意到，如果某节点不平衡，那么节点的高度便没有计算下去的必要，只需要递归返回将节点不平衡的信息return即可，同时节点的高度一定是非负数。因此可以使用负数来表示节点不平衡的状态。

代码如下：

```C
int height(struct TreeNode* root) {
    if (root == NULL) {
        return 0;
    }

    int leftheight = height(root -> left);
    int rightheight = height(root -> right);
    if (leftheight == -1 || rightheight == -1 || fabs(leftheight - rightheight) > 1) {
        return -1;
    } else {
        return fmax(leftheight, rightheight) + 1;
    }
}

bool isBalanced(struct TreeNode* root){
    return height(root) >= 0;
}
```

### [剑指 Offer 34. 二叉树中和为某一值的路径](https://leetcode.cn/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

深度优先搜索回溯算法。

```C
int** ret;				// 当前所有的可行路径
int retSize;			// 当前所有可行路径的数量
int* retColSize;		// 当前所有可行路径每个路径的大小
int* path;				// 当前路径值的数组
int pathSize;			// 当前路径值数组的大小
int tar;				// 当前路径值数组和与target的差距

void dfs(struct TreeNode* root) {
    if (root == NULL) {
        return;			// 结束条件
    }

    path[pathSize++] = root -> val;		// 将当前节点值串在当前路径数组后
    tar -= root -> val;					// tar减去当前节点值

    if (root -> left == NULL && root -> right == NULL && tar == 0) {	// 若满足所寻路径条件
        int* tmp = malloc(sizeof(int) * pathSize);		// 分配临时数组
        memcpy(tmp, path, sizeof(int) * pathSize);		// 内存复制
        ret[retSize] = tmp;								// 将路径保存到ret
        retColSize[retSize++] = pathSize;				// 将路径大小保存到retColSize
    }

    dfs(root -> right);	// 继续搜索
    dfs(root -> left);
    pathSize--;			// 子树搜索完毕，返回前退回pathSize和tar
    tar += root -> val;
}

int** pathSum(struct TreeNode* root, int target, int* returnSize, int** returnColumnSizes){
    ret = malloc(sizeof(int*) * 1000);	// 初始换全部变量
    retColSize = malloc(sizeof(int) * 1000);
    path = malloc(sizeof(int) * 1000);
    tar = target;
    pathSize = 0;
    retSize = 0;
    dfs(root);			// 深度优先搜索
    *returnSize = retSize;
    *returnColumnSizes = retColSize;

    return ret;
}
```

### [剑指 Offer II 046. 二叉树的右侧视图](https://leetcode.cn/problems/WNC0Lk/)

一眼深度优先搜索

```C
int* rightSideView(struct TreeNode* root, int* returnSize){
    int* res = malloc(sizeof(int) * 100);
    int size = 0;

    void walk(struct TreeNode* root, int depth) {
        if (root == NULL) {
            return;
        }

        if (depth == size) {
            res[size++] = root -> val;
        }
        walk(root -> right, depth + 1);
        walk(root -> left, depth + 1);
    }

    walk(root, 0);
    *returnSize = size;
    return res;
}
```

### [剑指 Offer 19. 正则表达式匹配](https://leetcode.cn/problems/zheng-ze-biao-da-shi-pi-pei-lcof/)

**Hard动态规划，开摆！**

### [LC 38. 外观数列](https://leetcode.cn/problems/count-and-say/)

还行，就是字符串数组有点卡。

```C
void CAS(char* res) {
    char* tmp = malloc(sizeof(char) * 4500);
    char pre = res[0];
    int count = 1;
    int size = 0;

    for (int i = 1; res[i] != '\0'; i++) {
        if (pre == res[i]) {
            count++;
        } else {
            tmp[size++] = count + '0';
            tmp[size++] = pre;
            pre = res[i];
            count = 1;
        }
    }
    tmp[size++] = count + '0';
    tmp[size++] = pre;
    tmp[size] = '\0';

    memcpy(res, tmp, sizeof(char) * (size + 1));
}

char* countAndSay(int n) {
    char* res = malloc(sizeof(char) * 4500);
    res[0] = '1';
    res[1] = '\0';

    for (int i = 1; i < n; i++) {
        CAS(res);
    }

    return res;
}
```

### [剑指 Offer II 101. 分割等和子集](https://leetcode.cn/problems/NUPfPr/)

NP完全问题，动态规划求解。

```C
bool canPartition(int* nums, int numsSize) {
    if (numsSize < 2) {
        return false;
    }
    int sum = 0, maxNum = 0;
    for (int i = 0; i < numsSize; ++i) {
        sum += nums[i];
        maxNum = fmax(maxNum, nums[i]);
    }
    if (sum & 1) {
        return false;
    }
    int target = sum / 2;
    if (maxNum > target) {
        return false;
    }
    int dp[numsSize][target + 1];
    memset(dp, 0, sizeof(dp));
    for (int i = 0; i < numsSize; i++) {
        dp[i][0] = true;
    }
    dp[0][nums[0]] = true;
    for (int i = 1; i < numsSize; i++) {
        int num = nums[i];
        for (int j = 1; j <= target; j++) {
            if (j >= num) {
                dp[i][j] = dp[i - 1][j] | dp[i - 1][j - num];
            } else {
                dp[i][j] = dp[i - 1][j];
            }
        }
    }
    return dp[numsSize - 1][target];
}
```

事实上注意到状态转移方程当前行只与上一行有关，因此可以原地dp。

代码：

```C
bool canPartition(int* nums, int numsSize) {
    if (numsSize < 2) {
        return false;
    }
    int sum = 0, maxNum = 0;
    for (int i = 0; i < numsSize; ++i) {
        sum += nums[i];
        maxNum = fmax(maxNum, nums[i]);
    }
    if (sum & 1) {
        return false;
    }
    int target = sum / 2;
    if (maxNum > target) {
        return false;
    }
    int dp[target + 1];
    memset(dp, 0, sizeof(dp));
    dp[0] = true;
    for (int i = 0; i < numsSize; i++) {
        int num = nums[i];
        for (int j = target; j >= num; --j) {
            dp[j] |= dp[j - num];
        }
    }
    return dp[target];
}
```



 



---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/datastructurewithc/  

