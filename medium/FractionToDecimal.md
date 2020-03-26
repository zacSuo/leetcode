
---
title: 2种解法：分数到小数
categories:
  - 技术资料
  - 算法
tags:
  - 字符串
  - 分数
  - 小数
  - 有理数
  - 线性表
  - 哈希表
date: 2020-01-19 19:34:31
updated: 2020-01-19 19:34:31
description: 通过字典（哈希表）和线性表两种数据结构，计算一个分数的小数形式...
---

## 题目

[NO. 166](https://leetcode-cn.com/problems/fraction-to-recurring-decimal/)

给定两个整数，分别表示分数的分子 numerator 和分母 denominator，以字符串形式返回小数。

如果小数部分为循环小数，则将循环的部分括在括号内。

示例 1:

输入: numerator = 1, denominator = 2
输出: "0.5"
示例 2:

输入: numerator = 2, denominator = 1
输出: "2"
示例 3:

输入: numerator = 2, denominator = 3
输出: "0.(6)"

***
## 解法一（字典/哈希表）
思路：直接使用数值相除进行求解，如果不够除就乘以10，然后结果补0，不过为了防止溢出使用了long。
1. 把负数转换为正数进行求解，如果是异号，给结果字符串增加负号
2. 先对整数部分求解，整数部分直接相除后把字符串结果保存起来
3. 小数部分，每次使用字典记录分子出现过的索引位置，如果分子再次出现则表示是循环小数，那么再分子上一次出现的位置和字符串结尾插入括号
```csharp
public class Solution {
    public string FractionToDecimal(int numerator, int denominator) {
        long n = numerator,m=denominator;
        long nChild;
        if(n == 0) return "0";
        List<char> r = new List<char>();
        Dictionary<long,int> nIdx = new Dictionary<long,int>();
        if(n < 0 && m > 0 || n > 0 && m < 0) r.Add('-');//异号时候先加负号
        if(n < 0) n *= -1;//转为正数
        if(m < 0) m*= -1;
        string str = (n/m).ToString();
        foreach(char item in str)
        {//获取整数部分
            r.Add(item);
        }
        n %= m;
        if(n > 0)
        {//小数部分
            r.Add('.');
            while(n>0)
            {
                if(nIdx.ContainsKey(n))
                {//循环小数（分子出现过）
                    r.Insert(nIdx[n],'(');
                    r.Add(')');
                    break;
                }
                nIdx.Add(n, r.Count);
                n *= 10;
                nChild = n/m;
                r.Add((char)(nChild + '0'));
                if(nChild > 0)
                {//够除的时候才除
                    n %= m;
                }
            }
        }
        return string.Join("",r.ToArray());
    }
}
```
***
## 解法二（线性表List）
思路：逻辑跟解法一完全一样，编码上做了下面的优化或调整
1. 使用线性表List代替字典Dictionary
2. 使用负数代替分母的long，负数可以防止溢出
3. 异号判断使用异或运算，整数部分最后再合并到结果中
4. 如果没有小数部分，直接返回，将后面一大段从if中解放出来

```csharp
public class Solution {
    public string FractionToDecimal(int numerator, int denominator) {
        long n = numerator;
        int nChild,m=denominator;
        if(n == 0) return "0";
        string strFlag = "";
        List<char> r = new List<char>();
        List<long> nList = new List<long>();
        
        if(n < 0 ^ m < 0) strFlag = "-";//异号
        if(n > 0) n *= -1;//转为负数
        if(m > 0) m*= -1;
        string str = (n/m).ToString();
        n %= m;
        if(n == 0) 
        {
            return strFlag + str;
        }
        //小数部分
        while(n != 0)
        {
            if(nList.Contains(n))
            {//循环小数（分子出现过）
                r.Insert(nList.IndexOf(n),'(');
                r.Add(')');
                break;
            }
            nList.Add(n);
            n *= 10;
            nChild =(int)( n/m);
            r.Add((char)(nChild + '0'));
            if(nChild > 0)
            {//够除的时候才除
                n %= m;
            }
        }
        return strFlag + str +"."+string.Join("",r.ToArray());
    }
}
```
