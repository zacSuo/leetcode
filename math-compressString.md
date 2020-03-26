---
title: 3种解法： 实现字符串压缩
categories:
  - 技术资料
  - 算法
tags:
  - 字符串压缩
  - 双指针
  - python
date: 2020-03-15 19:34:29
updated: 2020-03-15 19:34:29
description: 使用（临时变量、双指针法、Python库函数）3种解法，实现对字符串的压缩...
---


## 题目
字符串压缩。利用字符重复出现的次数，编写一种方法，实现基本的字符串压缩功能。比如，字符串aabcccccaaa会变为a2b1c5a3。若“压缩”后的字符串没有变短，则返回原先的字符串。你可以假设字符串中只包含大小写英文字母（a至z）。

示例1:

 输入："aabcccccaaa"
 输出："a2b1c5a3"
示例2:

 输入："abbccd"
 输出："abbccd"
 解释："abbccd"压缩后为"a1b2c2d1"，比原字符串长度更长。
提示：

字符串长度在[0, 50000]范围内。

***
## 解法一（临时变量）
思路：使用临时变量保存上一次的字符，用当前字符进行判断并计数，遇到新字符时重新记录，这种写法还是比较浓厚C的写法，因为临时的整型变量可能会重新申请对象。
1. 记录首个字符，使用列表保存每次拼接的压缩字符
2. 对当前字符判断，如果跟之前相同就计数，否则重新记录字符
3. 将最后一个字符加入list

* 时间复杂度：O(n)
* 空间复杂度：O(n)
```python
class Solution:
    def compressString(self, S: str) -> str:
        if len(S) == 0:
            return S
        tmpChar = S[0]
        tmpCount = 0
        strLen = 0
        l = list()
        for item in S:
            if item == tmpChar:
                tmpCount += 1
            else:
                l.append('{}{}'.format(tmpChar,tmpCount))
                tmpChar = item
                tmpCount = 1
                strLen += len(str(tmpCount)) + 1
        #最后一个字符
        l.append('{}{}'.format(tmpChar,tmpCount))
        strLen += len(str(tmpCount)) + 1

        result = S
        if strLen < len(S):
            result = ''.join(l)
        return result

```
***
## 解法二（快慢指针）
思路：跟解法一思路类似，做法上通过两个指针实现，慢指针始终指向字符开始位置，快指针指向相同字符的结束位置。
1. 使用开始索引定位当前字符，一直访问字符串，直到开始位置到达字符串结尾
2. 结束索引先到达字符串结尾时，由于开头字符还未到达，因此会进入else模块进行拼接

```python
class Solution:
    def compressString(self, S: str) -> str:
        start, end, sLen = 0,0,len(S)
        result = ""
        tmpCount = 0
        while start < sLen:
            if end<sLen and S[start] == S[end]:
                tmpCount += 1
                end += 1
            else:
                result += '{}{}'.format(S[start],tmpCount)
                start = end
                tmpCount = 0
        return result if len(result) < len(S) else S
```
***
## 解法三（Pythonic)
思路：利用python自身的库函数itertools.groupby进行字符串归类和统计
1. 使用库函数获取字符串
2. 使用三元运算返回结果

```python
class Solution:
    def compressString(self, S: str) -> str:
        sResult = ''.join([item+str(len(list(itemStr))) for item,itemStr in itertools.groupby(S)])
        return sResult if len(sResult) < len(S) else S
```
