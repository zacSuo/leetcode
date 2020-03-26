---
title: 2种解法：找到第k个排列结果
categories:
  - 技术资料
  - 算法
tags:
  - 排列
  - 数据排列
  - 数值顺序排序
  - 循环
  - 递归
date: 2020-01-14 19:34:29
updated: 2020-01-14 19:34:29
description: 使用循环和递归2种解法，实现数组的第k个排列问题...
---

## 题目

[NO. 60](https://leetcode-cn.com/problems/permutation-sequence/)

给出集合 [1,2,3,…,n]，其所有元素共有 n! 种排列。

按大小顺序列出所有排列情况，并一一标记，当 n = 3 时, 所有排列如下：

"123"
"132"
"213"
"231"
"312"
"321"
给定 n 和 k，返回第 k 个排列。

说明：

* 给定 n 的范围是 [1, 9]。
* 给定 k 的范围是[1,  n!]。

示例 1:

>输入: n = 3, k = 3
输出: "213"

示例 2:

>输入: n = 4, k = 9
输出: "2314"

## 解法一
思路：用递归的思想，第一个数字的索引是由后面n-1个数值的阶层倍数，即Idx[0] = k/(n-1)!
1. 先将数组存入线性表List
2. 按递归逻辑，依次从前到后进行访问
3. 将剩余数据依次输出

时间复杂度：O(n)
空间复杂度：O(n)
```csharp
public class Solution {
    public string GetPermutation(int n, int k) {
        string str = "";
        int t, idx,nF=1;
        List<char> r = new List<char>();
        for(int i=1;i<=n;i++)
        {//从小打大存入数据
            nF *= i;
            r.Add((char)(i+'0'));
        }
        nF /= n;
        for(int i=n-1;i> 0 && k>0;i--)
        {//按顺序读出数据
            idx = (k-1) / nF;        
            str += r[idx] ;
            r.RemoveAt(idx);
            k -= idx * nF;
            nF /= i;
        }
        str += r[0];//剩余最后一个数据
        return str;
    }
}
```

<center><img src="/img/tech/aHR0cHM6Ly9waW.jpg" title="执行结果"/></center>

## 解法二：
思路：同上面一样，改用递归的方式实现，逻辑一样，因此执行效率跟上面差不多

```csharp
public class Solution {

    List<char> r = new List<char>();
    public string Recursion(int n,int fa,int k)
    {
        if(n == 0 || k == 0) return r[0].ToString();
        int idx = (k-1)/fa;
        string str = r[idx].ToString();
        r.RemoveAt(idx);
        return  str + Recursion(n-1,fa/n, k-idx*fa);
    }

    public string GetPermutation(int n, int k) {
        string str = "";
        int t, idx,nF=1;
        
        for(int i=1;i<=n;i++)
        {
            nF *= i;
            r.Add((char)(i+'0'));
        }
        return Recursion(n-1,nF/n,k);
    }
}
```
