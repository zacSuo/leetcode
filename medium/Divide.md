---
title: 2种解法：实现两数相除
categories:
  - 技术资料
  - 算法
tags:
  - 除法
  - 整数
  - 加减
  - 二进制
date: 2020-01-05 19:34:29
updated: 2020-01-05 19:34:29
description: 使用三种算法，实现子数组异或求和查询的问题...
---

## 题目

[NO. 29]()https://leetcode-cn.com/problems/divide-two-integers/

给定两个整数，被除数 dividend 和除数 divisor。将两数相除，要求不使用乘法、除法和 mod 运算符。

返回被除数 dividend 除以除数 divisor 得到的商。

示例 1:

输入: dividend = 10, divisor = 3
输出: 3
示例 2:

输入: dividend = 7, divisor = -3
输出: -2
说明:

被除数和除数均为 32 位有符号整数。
除数不为 0。
假设我们的环境只能存储 32 位有符号整数，其数值范围是 [−231,  231 − 1]。本题中，如果除法结果溢出，则返回 231 − 1。

***

## 解法一
思路：直接暴力减法肯定会超时，比如被除数等于2^32^，除数等于1，那么用时就是2^32，20亿次的运算，好点的CPU也得几秒钟，肯定超时，直接忽略这种做法，但这个思路是可以继承的。

不用每次只是减去除数，而是减去一个2的幂次，不能用乘法运算，但是可以用移位运算来形成2的幂次，然后使用被除数减去2的幂次来加快计算效率，实际执行如下：（search递归函数）
1. 被除数 = 除数 * 2^a + b
2. 满足公式的最大a，然后递归将b作为被除数
3. 直到被除数小于除数为止
4. 将所有的2^a进行求和
另外，为了防止整数溢出的情况，使用了负数进行减法运算。
```csharp
public class Solution {

    public int search(int m,int n)
    {
        if(m > n) return 0;//当被除数比除数的绝对值还小的时候结束

        int k = -1, t = n;
        while(m < t && m - t < t)
        {//使用2的幂次进行求解
            t <<= 1;
            k <<= 1;
        }
        return  k + search(m - t,n);
    }

    public int Divide(int dividend, int divisor) {
        int mFlag = dividend>0?-1:1;
        int nFlag = divisor>0?-1:1;
        int flag = -1* mFlag * nFlag; 
        //将值转换为负值进行计算
        int m = dividend * mFlag, n = divisor * nFlag;

        int r = search(m,n);
        if(r == -2147483648 && flag == -1)
        {//只有一种情况溢出，就是最小负值转换为正值时
            return 2147483647;
        }
        
        return r * flag;        
    }
}
```
<center><img src="/img/tech/20200108214026952.png" title="执行结果"/></center>

## 解法二
思路：
基于第一种解法的变种，商作为一个值，是由二进制组成，那么现在就是要寻找哪些二进制的值为1，只要将除数乘以2^i可以被除，那其对应位置的值即为1，直到最后，这样就省去了递归的栈空间消耗，代码如下：
被除数 = 除数 * (2^n+2^i...+2^0) + b

```csharp
public class Solution {
    public int Divide(int dividend, int divisor) {
        int mFlag = dividend>0?-1:1;
        int nFlag = divisor>0?-1:1;
        int flag = -1* mFlag * nFlag; 
        //将值转换为负值进行计算        
        int m = dividend * mFlag, n = divisor * nFlag;
        int r=0, k = 0;
        while(m<n && m-n<=n)
        {//找最大的幂次
            n <<= 1;
            k ++;
        }
        while(k>=0)
        {
            if(m<=n)
            {//第k个二进制位为1
                m -= n;
                r += (-1 << k);
            }
            n >>= 1;
            k--;
        }
        if(r == -2147483648 && flag == -1)
        {//只有一种情况溢出，就是最小负值转换为正值时
            return 2147483647;
        }
        
        return r * flag;        
    }
}
```
<center><img src="/img/tech/20200108223910174.png" title="执行结果"/></center>