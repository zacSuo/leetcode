---
title: 3种解法：计算无重复字符的最长子串
categories:
  - 技术资料
  - 算法
tags:
  - 窗口滑动
  - 数组
  - 暴力法
date: 2020-02-05 19:34:29
updated: 2020-02-05 19:34:29
description:  基于窗口滑动、固定数组及暴力法，3种解法计算一个字符串中，无重复字符的最长子字符串的长度...
---

## 题目
给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:

输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
示例 2:

输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
示例 3:

输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
***
## 解法一（滑动窗口）
思路：使用一个列表保存无重复子字符串，如果新字符在子串中，那么已经统计了当前子串的最大值，需要往后进行访问，寻找其它子串，为了防止重复，则把最开头到子串中字符重复位置的都删除，并加入新字符，保证子串中没有重复的字符。
1. 将字符串从前到后按字符进行遍历，并建立无重复子字符串
2. 找到子串中跟新字符相同字符的位置，并删除从前面到该位置所有字符，重新开始寻找新的子串
3. 用c记录每个子串长度，用mx记录最大的长度
* 时间复杂度：O(n)
* 空间复杂度：O(n)
```csharp
public class Solution {
    public int LengthOfLongestSubstring(string s) {
        List<char> r = new List<char>();
        int c = 0,mx = 0,t;
        foreach(char item in s)
        {
            if(r.Contains(item))
            {
                t = r.IndexOf(item)+1;
                r.RemoveRange(0,t);
                c-=t;
            }
            r.Add(item);
            c++;
            mx = c>mx?c:mx;
        }
        return mx;
    }
}
```

## 解法二（固定数组）
思路：使用一个字符串存储每个字符的最大位置，如果重复出现，使用当前位置减去上次出现的位置，即为当前子串的最大长度。
1. 记录每个字符出现的最大位置，c记录最后开始位置
2. 用本次的位置减去上次的开始位置，即为当前包含子串的最大长度
3. 用mx记录每个子串的最大值
* 时间复杂度：O(n)
* 空间复杂度：O(1)

```csharp
public class Solution {
    public int LengthOfLongestSubstring(string s) {
        int [] r = new int[128];//ascii码个数
        int c = 0,mx = 0,idx = 0;
        foreach(char item in s)
        {//c记录子串开始位置，idx为当前位置
            idx++;
            c = c>r[item]?c:r[item];
            r[item] = idx;
            mx = idx-c>mx?idx-c:mx;            
        }
        return mx;
    }
}
```
***
## 解法三（暴力法）
思路：遍历全部子串，计算所有没重复字符子串的长度，并记录最大长度作为结果，尽管这种做法是最低效的，但通常在没有思路的时候也是第一想到的，本题目中不推荐，但是给出算法供参考。
1. 两层循环得到全部子串
2. 判断子串是否存在相同字符
3. 保存满足条件的最长子串

* 时间复杂度：O(n^3)
* 空间复杂度：O(n)
暴力法的时间复杂度有点高，不过由于可以提前退出内循环，因此通过了937个测试用例中的936个，最后一个测试用例超时了，超时用例及源码如下：
[https://www.zhenxiangsimple.com/files/tech/testCase20200205.txt](https://www.zhenxiangsimple.com/files/tech/testCase20200205.txt)
```csharp
public class Solution {
    public int LengthOfLongestSubstring(string s) {
        int c = 0,mx = 0;
        bool flag;
        List<char> r = new List<char>();
        for(int i=0;i<s.Length;i++)
        {//子串开头索引
            for(int j=i+1;j<=s.Length;j++)
            {//子串结束位置
                if(j<s.Length && s[i] == s[j]) break;//结尾跟开头重复字符，后面就都不再满足
                r.Clear();
                flag = false;
                for(int k=i;k<j;k++)
                {//判断子串是否有重复
                    if(r.Contains(s[k]))
                    {//出现重复字符
                        flag = true;
                        break;
                    }
                    r.Add(s[k]);                    
                }
                if(!flag)
                {//没有重复字符
                    mx = mx>j-i?mx:j-i;
                }
            }
        }
        return mx;
    }
}
```
