---
title: 4种解法：实现求解平方根
categories:
  - 技术资料
  - 算法
tags:
  - 迭代
  - 二分法
  - 牛顿迭代法
  - 数学
date: 2020-01-16 19:34:29
updated: 2020-01-16 19:34:29
description: 使用（迭代、二分法、牛顿法）4种逼近解法，计算一个整数的平方根...
---

## 题目
实现 int sqrt(int x) 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

示例 1:

输入: 4
输出: 2
示例 2:

输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 
     由于返回类型是整数，小数部分将被舍去。

***
## 解法一
思路：从1开始增加，直到被求解值，或者溢出
1. 每次计算当前值的平方
2. 使用本次的平方小于上次的平方，作为溢出判断条件
3. 如果是因为相等退出循环则为当前值，其它溢出和大于x的情况都减一

```csharp
public class Solution {
    public int MySqrt(int x) {
        int r = 1,sum = 1, sumLast = 1;
        while(sum < x)
        {
            r += 1;
            sum = r * r;
            if(sum < sumLast)
            {//溢出了
                break;
            }
            sumLast = sum;
        }
        return sum == x? r : r-1;
    }
}
```
***
## 解法二

思路：基于解法一，修改了溢出的判断方式，不用溢出做条件，而是改用除法代替乘法
1. 即使除了之后等于r，也要继续使得r增加个1，因为整数相除会去除了小数部分
2. 最终结果就是超出部分减一

```csharp
public class Solution {
    public int MySqrt(int x) {
        int r = 1;
        while(x /r >= r)
        {
            r += 1;
        }
        return r-1;
    }
}
```
## 解法三

思路：基于解法二，对查找的速度进行优化，使用二分法进行查找代替一个一个递增的方式。
1.  循环判断（+1）是为了防止死循环，即由于取整的原因，可能$l+1=r$，因此，mid = l
2. 结束后，mid应该等于l，就是结果，但考虑到进不去循环的情况，即x==1这一个特例，所以做了最后的判断。

```csharp
public class Solution {
    public int MySqrt(int x) {
        int l=0,r=x,mid;
        while(l+1<r)
        {
            mid = l + (r-l)/2;
            if(x/mid >= mid)
            {
                l = mid;
            }
            else
            {
                r = mid;
            }
        }
        return r==1?r:l;
    }
}
```
***
## 解法四

思路：基于解法三，将二分法使用牛顿迭代法进行替换，即，使用线性的斜率进行查找，特点就是距离远的时候很快到附近，距离近的时候慢慢找。
简单解释下牛顿法，相当于使用线性方程去逼近非线性问题的过程，最终的迭代公式为：$x_{k+1} = x_k-\frac{f(x_k)}{f^`(x_k)}$，本题目中$f(x)=x^2-N$，所以迭代公式为：$x_{k+1} = x_k-\frac{x^2_k-N}{2x_k} = \frac{1}{2}(x_k+\frac{N}{x_k})$
1. 由于越来越逼近，当两次迭代差值小于1时，表示最终结果在小于1以内了，如果不放心，可以改更小，比如0.1，或0.01等。
2. 对于求解的值，为了舍弃小数部分，选择更小的值，其整型即为待求解


```csharp
public class Solution {
    public int MySqrt(int x) {
        if(x==0) return 0;
        double l=1,r=x;
        while(Math.Abs(l-r) > 1)
        {
            r = l;
            l = (x/l + l)/2;
        }
        return (int)Math.Min(l,r);
    }
}
```
