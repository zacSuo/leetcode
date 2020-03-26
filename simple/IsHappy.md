---
title: 2种解法：判断快乐数
categories:
  - 技术资料
  - 算法
tags:
  - 快乐数
  - 循环
  - 快慢指针
date: 2020-01-20 19:34:29
updated: 2020-01-20 19:34:29
description:  使用循环（集合）和快慢指针两种方法，判断一个数是否为快乐数...
---
## 题目

[NO. 202](https://leetcode-cn.com/problems/happy-number/)

编写一个算法来判断一个数是不是“快乐数”。

一个“快乐数”定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是无限循环但始终变不到 1。如果可以变为 1，那么这个数就是快乐数。

示例: 

输入: 19
输出: true
解释: 
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
***
## 解法一（循环）
思路：每次计算结果存储起来，如果集合中再次发现了这个值，表示出现循环不适快乐数，如果计算到1了还没出现相同的值，表示是快乐数
1. 最外层循环判断是否是1，如果是1则退出循环，认为是快乐数
2. 内层循环进行计算，如果没重复将结果存储起来
3. 如果发现有值重复，则不是快乐数，提前返回
* 时间复杂度是空间复杂度的$ln(n)$倍，具体空间复杂度不太好表达，如果很大的一个是可能导致空间溢出。
```csharp
public class Solution {
    public bool IsHappy(int n) {
        List<int> r = new List<int>();
        int m = 0;
        while(n>1)
        {
            m = 0;
            r.Add(n);
            while(n>0)
            {
                m += (int)Math.Pow(n%10,2);
                n/=10;
            }
            n = m;
            if(r.Contains(n))
            {
                return false;
            }
        }
        return true;
    }
}
```
***
## 解法二（快慢指针）
思路：使用快慢指针思想，如果是快乐数那么最后结果将保持为1，如果是循环那么当两指针相遇时即为循环点。即，同一段距离速度是2倍，时间相同，第一次相遇就是循环的起点，$s_0=v_0t_0，s_1=2v_0t_0$
1. 按规则对数值进行计算
2. 进行快慢指针推进
3. 循环结束后判断快乐数
* 时间复杂度是解法一的3倍
* 空间复杂度：O（1）

```csharp
public class Solution {
    public int getValue(int n)
    {
        int m = 0;
        while(n>0)
        {
            m += (int)Math.Pow(n%10,2);
            n/=10;
        }
        return m;
    }
    public bool IsHappy(int n) {
        int m = n;
        do
        {
            m = getValue(m);//慢指针m走1步
            n = getValue(n);
            n = getValue(n);//快指针n走2步
        }while(m != n);
        return m==1;
    }
}
```
