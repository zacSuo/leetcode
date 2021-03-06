
---
title: 2中解法：计算阶乘末尾零的个数
categories:
  - 技术资料
  - 算法
tags:
  - 阶乘
  - 统计0个数
  - 整数
date: 2020-01-20 19:34:31
updated: 2020-01-20 19:34:31
description: 通过直接计算和累计两种方法，计算一个整数阶乘的末尾零的个数...
---
给定一个整数 n，返回 n! 结果尾数中零的数量。

示例 1:

输入: 3
输出: 0
解释: 3! = 6, 尾数中没有零。
示例 2:

输入: 5
输出: 1
解释: 5! = 120, 尾数中有 1 个零.
说明: 你算法的时间复杂度应为 O(log n) 。
***

## 题目


[NO. 172](https://leetcode-cn.com/problems/factorial-trailing-zeroes/)

给定一个整数 n，返回 n! 结果尾数中零的数量。

示例 1:

>输入: 3
输出: 0
解释: 3! = 6, 尾数中没有零。

示例 2:

>输入: 5
输出: 1
解释: 5! = 120, 尾数中有 1 个零.

说明: 你算法的时间复杂度应为 O(log n) 。


## 解法一
思路：看这个时间复杂度要求，首先不能是进行乘法然后再对10进行求余判断，其实也排除了从1到N依次对每个值进行判断（这样的时间复杂度是O(n)）。

分析之后发现，只要出现5就一定会出现结尾多一个0，那么现在的问题就变成了1-N有多少个5的乘法因子。

1. 先获取5的倍数
2. 由于5的幂次存在多次运算的情况，因此，对5的幂次进行补充增加，每使用一次幂次就增加一次累计

* 时间复杂度：O（$log_5(n)$）
* 空间复杂度：O（1）
```csharp
public class Solution {
    public int TrailingZeroes(int n) {
        int r = n/5,k=1;
        while(true)
        {
            k++;
            if(Math.Pow(5,k) > n)
            {
                break;
            }
            r += (int)(n/Math.Pow(5,k));
        }
        return r;
    }
}
```
***
## 解法二
思路：基于解法一进行了编码的优化
1. 由于每次遇到5的幂次进行累计，因此，可以直接对n进行迭代除5求和
2. 区别类似求解（5^1^+5^2^+...+5^k^）时候，解法一是每次在循环中计算5^i^，然后求和累计，解法二是在循环中利用上一次计算的5^i-1^，乘以5得到本次的5^i^

* 复杂度跟解法一完全一样
```csharp
public class Solution {
    public int TrailingZeroes(int n) {
        int r = 0;
        while(n>=5)
        {
            n /= 5;
            r += n;
        }
        return r;
    }
}
```
