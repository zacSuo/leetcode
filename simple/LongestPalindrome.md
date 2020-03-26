---
title: 3种解法： 计算最长回文串
categories:
  - 技术资料
  - 算法
tags:
  - 回文串
  - 字符串
  - Python
  - 字典
date: 2020-03-18 19:34:29
updated: 2020-03-18 19:34:29
description: 使用（字典、固定数组、Pythonic）3种解法，来计算一个字符串可以构建的最长回文串...
---
## 题目

[NO. 409](https://leetcode-cn.com/problems/longest-palindrome)

给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。

在构造过程中，请注意区分大小写。比如 "Aa" 不能当做一个回文字符串。

注意:
假设字符串的长度不会超过 1010。

示例 1:

输入:
"abccccdd"

输出:
7

解释:
我们可以构造的最长的回文串是"dccaccd", 它的长度是 7。

***

## 解法一（字典）
思路：可以构建回文串就相当于构建左右对称的字符，但是最中间可以出现一个字符，因此对于奇数可以进行多一个字符。
即题目的思路就是统计字符中可以构建最多的偶数个数，如果是奇数则减一即为最大的偶数，如果是偶数则本身即为最大的偶数。

1. 统计每个字符的个数
2. 对字符个数进行奇偶判断，偶数直接累加，奇数标记并减一后累加
3. 如果出现过奇数，则整体加1

* 时间复杂度：O(n)
* 空间复杂度：O(n)
```csharp
public class Solution {
    //author: ​suoxd123@126.com
    public int LongestPalindrome(string s) {
        int cnt = 0;
        Dictionary<char,int> dict = new Dictionary<char,int>();
        foreach(char item in s)
        {//统计每个字符个数
            if(dict.ContainsKey(item))
            {
                dict[item] ++;
            }
            else
            {
                dict.Add(item,1);
            }
        }
        bool flag = false;
        foreach(int item in dict.Values)
        {
            if((item & 1) == 1)
            {//奇数
                cnt += item - 1;
                flag = true;
            }
            else
            {//偶数
                cnt += item;
            }
        }
        cnt += flag?1:0;
        return cnt;
    }
}
```
***
## 解法二（固定数组）
思路：前面解法是最常规或者第一反应的做法，现在使用固定数组保存字符个数，减少字典hash的计算，尽管消耗也不大。
1. 申请固定长度数组：58 = 'z' - 'A' + 1，或者可以直接申请128（ascii）个，则不用进行减'A'的计算
2. 统计字符个数，利用了除法取整进行了奇数过滤，即3/2 ==(int)1.5 == 1
3. 本解法判断奇数方法跟前面不同，是通过整体字符长度大于当前求和长度来判断。

* 时间复杂度：O(n)
* 空间复杂度：O(1)
```csharp
public class Solution {
    //author: ​suoxd123@126.com
    public int LongestPalindrome(string s) {
        int cnt = 0;
        int [] arrChar = new int[58];
        foreach(char item in s)
        {//统计每个字符个数
            arrChar[item - 'A'] ++;
        }        
        foreach(int item in arrChar)
        {//字符计数
            cnt += (int)(item / 2) * 2;
        }
        cnt += cnt < s.Length?1:0;
        return cnt;
    }
}
```
***
## 解法三（Pythonic)
思路：用Python的内置函数进行运算来简化程序
1. 使用collections.Counter函数，对每个字符出现次数进行统计
2. 使用sum函数，对所有次数里的偶数求和
3. 如果有奇数存在，则整体加1

```python
class Solution:
    #author: ​suoxd123@126.com
    def longestPalindrome(self, s: str) -> int:
        cnt = sum([v-1 if v%2==1 else v for v in collections.Counter(s).values()])
        return cnt + 1 if cnt < len(s) else cnt
```

***
<center>    <img src="/img/tech/20200319093107.png" title="执行结果"/>    </center>
截图从下往上分别是：解法3\1\2
解法2执行出错，是开始申请的空间是26，忽略了大小写