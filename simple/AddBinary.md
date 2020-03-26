---
title: 2种解法：二进制字符串求和
categories:
  - 技术资料
  - 算法
tags:
  - 整数
  - 加减
  - 二进制
  - 字符串
date: 2020-01-14 19:34:29
updated: 2020-01-14 19:34:29
description: 使用两种解法，实现对二进制字符串求和运算...
---

## 题目

[NO. 67](https://leetcode-cn.com/problems/add-binary/)

给定两个二进制字符串，返回他们的和（用二进制表示）。

输入为非空字符串且只包含数字 1 和 0。

示例 1:

输入: a = "11", b = "1"
输出: "100"
示例 2:

输入: a = "1010", b = "1011"
输出: "10101"
***
## 解法一
思路：一道简单的题目，本解法的核心是代码简化，就是对于两个字符都在同一个循环中完成，只是在进行计算前先判断是否达到了字符串长度。
1. 从后往前对字符串进行查找
3. 每个字符依次相加，如果进位使用临时变量n代替
4. 两个字符串都遍历完了，再做最后的判断

```csharp
public class Solution {
    public string AddBinary(string a, string b) {
        string str = string.Empty;
        int i=a.Length-1,j=b.Length-1;
        int t = 0, n = 0;
        for(;i>=0 || j>=0;i--,j--)
        {
            t = n;
            if(i >= 0)
            {
                t += (a[i]-'0');
            }
            if(j >= 0)
            {
                t += (b[j] - '0') ;
            }
            str = (char)(t%2 + '0') + str;
            n = t/2;
        }
        if(n > 0)
        {
            str = '1' + str;
        }
        return str;
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200115001637200.png)
***
## 解法二
思路：在进行计算前，先对短的字符串进行补位，即在短的字符串前面补‘0’，按道理时间复杂的一样的，但两个的用时相差挺大的，前一个96ms，后一个156ms，字符串申请这么耗时间呀。
1. 补位
2. 计算

```csharp
public class Solution {
    public string AddBinary(string a, string b) {
        string str = string.Empty;
        int len = Math.Max(a.Length,b.Length);
        if(a.Length < len)
        {
            a = new string('0',len - a.Length) + a;
        }
        if(b.Length < len)
        {
            b = new string('0',len - b.Length) + b;
        }

        int t = 0, n = 0, i=len-1;
        while(i >= 0)
        {
            t = (a[i]-'0') + (b[i] - '0') + n;
            str = (char)(t%2 + '0') + str;
            n = t/2;
            i--;
        }
        if(n > 0)
        {
            str = '1' + str;
        }
        return str;
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200115001508226.png)
