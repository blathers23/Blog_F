# 使用C语言学习算法及数据结构

<!--more-->

{{<admonition tip>}}

按照去年考研翻车的经验，编程能力的欠缺是非常严重的问题。期望通过将LeetCode中的问题与考研大纲中所考内容对照性练习，来提高编程能力。编程语言选择C语言，更为通用。

{{</admonition>}}

## 1. 算法设计部分

### 1.1 递归

---

#### [LC50 Pow(x, n)](https://leetcode.cn/problems/powx-n/)

{{<admonition note>}}

今日吃瘪：

1. 先动脑子后动手。
2. 用C语言尽量避免在所给函数外编程，否则容易造成**本地测试结果和提交测试结果不同**！！！
3. 注意C语言int数据类型的范围。

{{</admonition>}}

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

#### 

### 1.2 分治策略



### 1.3 贪心算法



### 1.4 动态规划



### 1.5 线性规划



### 1.6 网络流



---

> 作者: Blathers  
> https://blathers23.netlify.app/datastructurewithc/
