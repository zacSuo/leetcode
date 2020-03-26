---
title: 3种解法：查找最长回文子串
categories:
  - 技术资料
  - 算法
tags:
  - 回文字符串
  - 动态规划
  - 暴力法
  - 中心扩展
date: 2020-02-06 19:34:29
updated: 2020-02-06 19:34:29
description:  通过暴力法、中心拓展及动态规划，3种方法查找一个字符串的最长回文子串...
---
## 题目
给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

示例 1：

输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
示例 2：

输入: "cbbd"
输出: "bb"
***
## 解法一（暴力法）
思路：原理就是对字符串从前到后依次进行遍历，最外层循环为子串头，第二层循环为确定子串尾，第三层循环为确定子串是否满足回文约束。
1. 确定子串头i，子串尾j
2. 判断子串是否满足回文，并记录最长回文子串
* 时间复杂度：O(n^3)
* 空间复杂度：O(1)
当前方案的时间复杂度太高，对于比较长的字符串会执行超时，尤其是字符串内容完全相同的情况，超时测试用例（1000个z）及源码如下：
[https://www.zhenxiangsimple.com/files/tech/testCase20200206.txt](https://www.zhenxiangsimple.com/files/tech/testCase20200206.txt)
```csharp
public class Solution {
    //author: ​suoxd123@126.com
    public string LongestPalindrome(string s) {
        int idx = 0,len = -1;
        bool flag = false;
        for(int i=0;i<s.Length;i++)
        {//从前往后遍历，确定起始字符
            for(int j=s.Length-1;j>=i;j--)
            {//从后往前遍历，确定结束字符
                if(s[i] != s[j]) continue;
                flag = false;
                for(int m=i,n=j;m<n;m++,n--)
                {//检查中间部分是否为回文子串
                    if(s[m] != s[n]) 
                    {
                        flag = true;
                        break;
                    }
                }
                if(!flag && j-i > len)
                {//记录最长子串
                    len = j - i;
                    idx = i;
                }
            }
        }
        return len > -1?s.Substring(idx,len+1):"";
    }
}
```
***
## 解法二（中心扩展）
思路：把每个字符作为回文串的中心点，向两边扩展进行检测来确定当前字符为中心的回文串长度，所有子串的最大长度即为所求。
1. 以字符为中心位置进行检测是否满足回文串
2. 对所有字符进行遍历
3. 当子串为偶数和奇数时对应的顺序不一致，本解法种分别对每个字符验证偶数和奇数两种情况，并保留最长串
* 时间复杂度：O(n^2)
* 空间复杂度：O(1)

```csharp
public class Solution {
    //author: ​suoxd123@126.com
    public void CheckWord(string s,int m,int n,out int idx,out int len)
    {
        for(;m>=0 && n<s.Length;m--,n++)
        {
            if(s[m] != s[n]) 
            {
                break;
            } 
        }
        //需要回溯一个位置
        m++;
        n--;
        len = n - m;
        idx = m;        
    }

    public string LongestPalindrome(string s) {
        int idx = 0,len = -1;
        int tmpIdx,tmpLen;
        for(int i=0;i<s.Length;i++)
        {
        	//奇数，即有一个字符在中间位置
            CheckWord(s,i-1,i+1,out tmpIdx,out tmpLen);
            if(tmpLen > len)
            {
                idx = tmpIdx;
                len = tmpLen;
            }
            //偶数，即中间是两个相同的字符，或没有一个具体字符在中间
            CheckWord(s,i,i+1,out tmpIdx,out tmpLen);
            if(tmpLen > len)
            {
                idx = tmpIdx;
                len = tmpLen;
            }
        }
        return len > -1?s.Substring(idx,len+1):"";
    }
}
```
***
## 解法三（动态规划）
思路：可以当做基于暴力解法的一种优化，将已经得到的回文串保存起来，用于求解新的回文串。对于一个回文子串，如果两边的字母一样，则可以分别向两边扩展一步形成新的回文串，如果两边字母不同，那当前串即为最长回文子串，直到找到最长的字符。
1. 1个和2个字符属于特殊情况需要特殊处理，1个字符都是回文串，2个字符如果相等就是回文串，如果不等就不是。
2. 利用递推公式：如果$s[i+1,j-1]$是回文，且$s[i]==s[j]$，那么$s[i,j]$也是回文串
3. 使用一个二维数组保存之前记录的回文串，数组横纵下标分别表示字符的起止索引是否为回文串，即t[i,j]=true表示s[i...j]是回文串
* 时间复杂度：O(n^2)
* 空间复杂度：O(n^2)

```csharp
public class Solution {
    //author: ​suoxd123@126.com
    public string LongestPalindrome(string s) {
        bool [,] t = new bool[s.Length,s.Length];
        int len=-1,idx=0, j;
        for(int l=0;l<s.Length;l++)
        {//子串前后间隔长度
            for(int i=0;i<s.Length-l;i++)
            {//子串起始位置
                j = i+l;//结束位置
                if(s[i] == s[j] && (l<=2 || t[i+1,j-1]))
                {
                    t[i,j] = true;
                    if(l > len)
                    {
                        len = l;
                        idx = i;
                    }
                }
            }
        }
        return len > -1?s.Substring(idx,len+1):"";
    }
}
```