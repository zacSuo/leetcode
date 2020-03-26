---
title: 2种解法：统计质数的个数
categories:
  - 技术资料
  - 算法
tags:
  - 质数
  - 循环判断
  - 空间换时间
date: 2020-01-30 19:34:29
updated: 2020-01-30 19:34:29
description: 基于依次检测和整体赋值两种方法，统计小于一个数的质数个数...
---

## 题目
统计所有小于非负整数 n 的质数的数量。

示例:

输入: 10
输出: 4
解释: 小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
***
## 解法一（检测）
思路：从头开始对数据依次进行判断，如果是质数则进行计数。有两个地方：
1. 首先是判断一个数x是否是质数时，只需要检测到$\sqrt x$
2. 其次由于偶数本身肯定不是质数，自增步长为2

* 时间复杂度：O(n$\sqrt n$)
* 空间复杂度：O(1)
```csharp
public class Solution {
    public int CountPrimes(int n) {
        if(n <= 2) return 0;
        int c = 0;
        for(int i=1;i<n;i+=2)
        {
            if(CheckPrime(i))
            {
                c++;
            }
        }
        return c;
    }

    public bool CheckPrime(int n)
    {
        int k = (int)Math.Pow(n,0.5);
        for(int i=2;i<=k;i++)
        {
            if(n%i == 0)
            {
                return false;
            }
        }
        return true;
    }
}
```
***
## 解法二（赋值）
思路：基于空间换时间的思路，不对每个数值进行单独判断，而是直接从前到后，使用已知的质数对后面未知的数据进行排除，即将质数的倍数本身进行排除，剩下的就是质数本身了。
1. 初始化保存质数的空间
2. 若当前数值没被标记过则为质数，即将质数的倍数进行标记为非质数

* 时间复杂度：小于O(nlog(n))
* 空间复杂度：O(n)

```csharp
public class Solution {
    public int CountPrimes(int n) {
        bool [] r = new bool[n];
        for(int i=0;i<n;i++)
        {
            r[i] = false;
        }
        int c = 0;
        for(int i=2;i<n;i++)
        {
            if(r[i] == false)
            {
                c++;
                for(int j=i*2;j<n;j+=i)
                {
                    r[j] = true;
                }
            }
        }
        return c;
    }
}
```
