---
title: 2种解法：判断字符串的子串
categories:
  - 技术资料
  - 算法
tags:
  - Python
  - 迭代器
  - 双指针
date: 2020-03-10 19:34:29
updated: 2020-03-10 19:34:29
description:  基于双指针法和Python的迭代器两种方法，判断一个字符串是否属于另一个字符串的子串...
---

## 题目

[NO. 392](https://leetcode-cn.com/problems/is-subsequence/)

给定字符串 s 和 t ，判断 s 是否为 t 的子序列。

你可以认为 s 和 t 中仅包含英文小写字母。字符串 t 可能会很长（长度 ~= 500,000），而 s 是个短字符串（长度 <=100）。

字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。

示例 1:
s = "abc", t = "ahbgdc"

返回 true.

示例 2:
s = "axc", t = "ahbgdc"

返回 false.

## 解法一（C# 双指针法）
就是使用双指针，一个指向子串，一个指向母串，如果相同子串指针向后移动，直到任一字符串遍历结束。
1. 字符相同时子串指针向后移动
2. 每次检查完母串指针向后移动
* 时间复杂度：O(n)
* 空间复杂度：O(1)
```csharp
public class Solution {
    public bool IsSubsequence(string s, string t) {
        int idxS=0, idxT=0;
        while(idxS < s.Length && idxT < t.Length)
        {
            if(s[idxS] == t[idxT])
            {
                idxS ++;
            }
            idxT ++;
        }
        return idxS == s.Length;
    }
}
```
## 解法二（Python 迭代器）
通过iter得到母串的迭代器，依次对子串进行检查，可以看到就两行代码，代码真是Pythonic
1. 母串使用iter生成迭代器，用于从前向后按顺序依次访问
2. 子串使用for in作为生成器，方便每次使用next向后遍历
3. all用于判断是否全为True


* 时间复杂度：O(n)
* 空间复杂度：O(n)

```python3
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        t = iter(t)
        return all(i in t for i in s)
```
