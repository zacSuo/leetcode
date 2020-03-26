---
title: 3个解法：口算难题
categories:
  - 技术资料
  - 算法
tags:
  - 算法
  - 递归
  - 循环
  - 多元一次方程
  - 数组
date: 2019-12-30 19:34:29
updated: 2019-12-30 19:34:29
description: 通过循环和递归方式实现算法，用于使用多元一次方程的思想，解决字符串等式的口算难题...
---
## 题目
给你一个方程，左边用 words 表示，右边用 result 表示。

你需要根据以下规则检查方程是否可解：

每个字符都会被解码成一位数字（0 - 9）。
每对不同的字符必须映射到不同的数字。
每个 words[i] 和 result 都会被解码成一个没有前导零的数字。
左侧数字之和（words）等于右侧数字（result）。 
如果方程可解，返回 True，否则返回 False。

 
#### 示例说明
示例 1：

输入：words = ["SEND","MORE"], result = "MONEY"
输出：true
解释：映射 'S'-> 9, 'E'->5, 'N'->6, 'D'->7, 'M'->1, 'O'->0, 'R'->8, 'Y'->'2'
所以 "SEND" + "MORE" = "MONEY" ,  9567 + 1085 = 10652
示例 2：

输入：words = ["SIX","SEVEN","SEVEN"], result = "TWENTY"
输出：true
解释：映射 'S'-> 6, 'I'->5, 'X'->0, 'E'->8, 'V'->7, 'N'->2, 'T'->1, 'W'->'3', 'Y'->4
所以 "SIX" + "SEVEN" + "SEVEN" = "TWENTY" ,  650 + 68782 + 68782 = 138214
示例 3：

输入：words = ["THIS","IS","TOO"], result = "FUNNY"
输出：true
示例 4：

输入：words = ["LEET","CODE"], result = "POINT"
输出：false
 

#### 提示：

2 <= words.length <= 5
1 <= words[i].length, results.length <= 7
words[i], result 只含有大写英文字母
表达式中使用的不同字符数最大为 10

## 思路
如果将字符当作一个变量，整个算式就相当于一个多元一次方程，题目就可以看做是，求解10以内整数的多元一次方程，那么求解步骤就分为两步：
1. 将字符串整理为多元一次方程，即把字符本身当作变量，而字符所在位置的10进制和当作参数
2. 求解多元一次方程，这个地方就需要想办法减少计算了，下面给了三个解法，用于求解多元一次方程。
<center><img src="/img/tech/20191231141306551.png" title="思路"/></center>
***
## 解法一
 基于第二个解法，做了一个优化，将字典用数组替换掉，由于方案本身并不关心具体某个字母是由哪个数值来代表，只要分别为不同的字母使用不同的数组索引即可，这样做减少的步骤是，将通过字典查找值的方式，使用数组索引代替，源码如下：
 

```csharp
public class Solution {
    public bool IsSolvable(string[] words, string result)
    {
        bool[] used = new bool[10];
        Dictionary<char, int> sList = new Dictionary<char, int>();
        for (int i = 0; i < 10; i++)
        {
            used[i] = false;
        }
        //整理为多元一次方程
        for (int i = 0; i < words.Length; i++)
        {
            getFunction(words[i], sList,1);
        }
        getFunction(result, sList, -1);
        //获取变量总数
        int[]vArray = sList.Values.ToArray<int>();
        //求解方程
        return this.findSolution(0, 0, vArray,used);
    }

    public void getFunction(string str, Dictionary<char, int> chList, int dir)
    {//计算多元一次方程的常量
        int tmpIdx = 1;
        for (int j = str.Length - 1; j >= 0; j--)
        {
            if (chList.ContainsKey(str[j]))
            {
                chList[str[j]] += tmpIdx * dir;
            }
            else
            {
                chList[str[j]] = tmpIdx * dir;
            }
            tmpIdx *= 10;
        }
    }

    public bool findSolution(int idx, int sum, int[] vArray, bool[] used)
    {//实际求解
        if (idx == vArray.Length)
            return sum == 0;
        for (int j = 0; j <= 9; j++)
        {
            if (!used[j])
            {
                used[j] = true;
                if (findSolution(idx + 1, sum + j * vArray[idx],vArray,used))
                    return true;
                used[j] = false;
            }
        }
        return false;
    }
}
```

## 解法二
当前解法基于下面的第三种解法做了下面3个角度的优化，来减少运算内容，从而减少时间复杂度。不过，这个解法通过了大部分测试用例，但提交还是提示超时，但单独执行超时的最后一个测试用例显示150ms，可能还有个全部测试用例的整体执行时间约束：
1. 直接将等式两边值都合并到左边，只是等式右边符号设为负数，减少了查看变量个数的步骤（getKeys函数）；
2. 求解过程的判断直接求和，判断整体是否为0，不需要再进行对比计算（checkEqual函数）；
3. 不需要先记录下来每个变量是多少，然后再进行计算，而是在查询的时候直接计算（kList[kArray[idx]] = j;语句）

<center><img src="/img/tech/20191231141323361.png" title="解法二"/></center>

> ["YOUVE","NEVER","BEEN","TO"]
"EUROPE"

```csharp
public class Solution {
    Dictionary<char, int> sList = new Dictionary<char, int>();
    bool[] used = new bool[10];
    char[] kArray;
    public bool IsSolvable(string[] words, string result)
    {
        for (int i = 0; i < 10; i++)
        {
            used[i] = false;
        }
        //整理为多元一次方程
        for (int i = 0; i < words.Length; i++)
        {
            getFunction(words[i], sList,1);
        }
        getFunction(result, sList, -1);
        //获取变量总数
        kArray = sList.Keys.ToArray<char>();
        //求解方程
        return this.findSolution(0,0);
    }

    public void getFunction(string str, Dictionary<char, int> chList, int dir)
    {//计算多元一次方程的常量
        int tmpIdx = 1;
        for (int j = str.Length - 1; j >= 0; j--)
        {
            if (chList.ContainsKey(str[j]))
            {
                chList[str[j]] += tmpIdx * dir;
            }
            else
            {
                chList[str[j]] = tmpIdx * dir;
            }
            tmpIdx *= 10;
        }
    }
    
    public bool findSolution(int idx,int sum)
    {//实际求解
        if (idx == kArray.Length)
            return sum == 0;
        for (int j = 0; j <= 9; j++)
        {
            if (!used[j])
            {
                used[j] = true;
                if (findSolution(idx + 1, sum + j * sList[kArray[idx]]))
                    return true;
                used[j] = false;
            }
        }
        return false;
    }
}
```

## 解法三
这个是我第一次的写法，仅通过少量的测试用例，未通过的测试用例是因为超时了，后面通过解法2和解法3分别对其算法和数据结构做了优化，超时测试用例及实现源码如下：
<center><img src="/img/tech/2019123114132862.png" title="解法三"/></center>
> ["LEET","CODE"]
"POINT"

```csharp
public class Solution {
        Dictionary<char, int> sList = new Dictionary<char, int>();
        Dictionary<char, int> rList = new Dictionary<char, int>();
        Dictionary<char, int> kList = new Dictionary<char, int>();
        bool[] used = new bool[10];
        char[] kArray;
        public bool IsSolvable(string[] words, string result)
        {
            for (int i = 0; i < 10; i++)
            {
                used[i] = false;
            }
            //整理为多元一次方程
            for (int i = 0; i < words.Length; i++)
            {
                getFunction(words[i], sList);
            }
            getFunction(result, rList);
            //获取变量总数
            getKeys(kList, sList);
            getKeys(kList, rList);
            kArray = kList.Keys.ToArray<char>();
            //求解方程
            return this.findSolution(0);
        }

        public void getFunction(string str, Dictionary<char, int> chList)
        {//计算多元一次方程的常量
            int tmpIdx = 1;
            for (int j = str.Length - 1; j >= 0; j--)
            {
                if (chList.ContainsKey(str[j]))
                {
                    chList[str[j]] += tmpIdx;
                }
                else
                {
                    chList[str[j]] = tmpIdx;
                }
                tmpIdx *= 10;
            }
        }

        public void getKeys(Dictionary<char, int> kList, Dictionary<char, int> sList)
        {//获取多元变量本身
            foreach (char k in sList.Keys)
            {
                if (!kList.ContainsKey(k))
                {
                    kList.Add(k, 0);
                }
            }
        }

        public bool checkEqual(Dictionary<char, int> kList)
        {//判断方程两边是否相等
            int sInt = 0, rInt = 0;
            foreach (char c in sList.Keys)
            {
                sInt += sList[c] * kList[c];
            }
            foreach (char c in rList.Keys)
            {
                rInt += rList[c] * kList[c];
            }
            return rInt == sInt;
        }

        public bool findSolution(int idx)
        {//实际求解
            if (idx == kArray.Length)
                return checkEqual(kList);
            for (int j = 0; j <= 9; j++)
            {
                if (!used[j])
                {
                    kList[kArray[idx]] = j;
                    used[j] = true;
                    if (findSolution(idx+1))
                        return true;
                    used[j] = false;
                }
            }
            return false;
        }
}
```
