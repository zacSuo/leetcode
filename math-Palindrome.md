---
title: 4个解法：判断回文数
categories:
  - 技术资料
  - 算法
tags:
  - 算法
  - 回文数
  - 循环
  - 数组
  - 字符串
date: 2020-01-01 01:01:01
updated: 2020-01-01 01:01:01
description: 通过数值和字符串等4种算法实现，用来判断一个数是否是回文数，类似于“上海自来水来自海上”...
---
## 题目
判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例 1:

输入: 121
输出: true
示例 2:

输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
示例 3:

输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
***


## 解法一
<center><img src="/img/tech/20200101130116460.jpg" title="解法一"/></center>
1. 直接将数值反转
2. 对比两个值是否相等

```csharp
public class Solution {
    public bool IsPalindrome(int x) {
        if(x < 0)   return false;
        int t = x,c = 0;
        while(t>0)
        {
            c = c*10 + t%10;
            t /= 10;
        }
        return c == x;
    }
}
```
***
## 解法二
<center><img src="/img/tech/20200101130116462.png" title="解法二"/></center>
 1. 基于解法一的改版
 2. 不用将全部位置反转，仅反转一半数值

```csharp
public class Solution {
    public bool IsPalindrome(int x) {
        if(x < 0 || (x>0 && x%10 == 0))   return false;
        int c = 0;
        while(x>c)
        {
            c = c*10 + x%10;
            x /= 10;
        }
        return c == x || x == c/10;
    }
}
```

***
## 解法三
<center><img src="/img/tech/2020010113183960.png" title="解法三"/></center>
这是我的第一种写法。
1. 从两端向中间，依次对比单个数值是否相等
2. 拿掉相等的数值后，最后剩余长度如果不超过1，即为true

```csharp
public class Solution {
    public bool IsPalindrome(int x) {
        if(x < 0)   return false;
        int t = x;
        bool flag = false;
        int len = 0,idx = 0;
        while(t>0)
        {//获取最高位
            t /= 10;
            len++;
        }
        for(;len > 0;len -= 2)
        {
            if(x%10 == x/((int)Math.Pow(10,len-1)))
            {
                x %= (int)Math.Pow(10,len - 1);
                x /= 10;
            }
            else
            {
                break;
            }
        }
        return len <= 1;
    }
}
```
***
## 解法四
转换为字符串，通过字符串的直接对比，或通过下标索引对比
<center><img src="/img/tech/20200101131626921.png" title="解法四"/></center>

```csharp
public class Solution {
    public bool IsPalindrome(int x) {
        string xStr = x.ToString ();
        char[] xArray = xStr.ToCharArray();
        Array.Reverse(xArray);
        string tStr = string.Join(string.Empty,xArray);
        return xStr.Equals(tStr);
    }
}
```

