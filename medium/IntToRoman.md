---
title: 2个解法：整数转为罗马数字
categories:
  - 技术资料
  - 算法
tags:
  - 算法
  - 匹配
  - 罗马数字
date: 2020-01-07 19:34:29
updated: 2020-01-07 19:34:29
description: 使用2种算法，将一个正整数转为罗马字母表达...
---

## 题目

[NO. 12](https://leetcode-cn.com/problems/integer-to-roman/)

罗马数字包含以下七种字符： I， V， X， L，C，D 和 M。

字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
给定一个整数，将其转为罗马数字。输入确保在 1 到 3999 的范围内。

示例 1:

输入: 3
输出: "III"
示例 2:

输入: 4
输出: "IV"
示例 3:

输入: 9
输出: "IX"
示例 4:

输入: 58
输出: "LVIII"
解释: L = 50, V = 5, III = 3.
示例 5:

输入: 1994
输出: "MCMXCIV"
解释: M = 1000, CM = 900, XC = 90, IV = 4.
***
## 解法一
思路：贪心算法
将小数置前的特例作为一个固定的值，跟默认的整数位一样，设置成数组，进行匹配，然后利用俗称的贪心思路，从大到小进行进行减法，最终得到结果，这是第一次的写法。

```csharp
public class Solution {
    public string IntToRoman(int num) {
        int[] intArray = {1000,900,500,400,100,90,50,40,10,9,5,4,1};
        string[] strArray = {"M","CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};
        int len = intArray.Length, idx = 0;
        string str = "";
        while(num > 0)
        {
            while(num >= intArray[idx])
            {
                str += strArray[idx];
                num -= intArray[idx];
            }
            idx++;
        }
        return str;
    }
}
```
<center><img src="/img/proj/20200107154441649.png" title="测试用例结果"/></center>


***
## 解法二
思路：权值匹配
类似十进制里面，使用空间换时间，对每个位置的值进行匹配求解，看起来比较麻烦，但确实思路清奇，似乎没技术含量，但解决问题高效。

```csharp
public class Solution {
    public string IntToRoman(int num) {
        string [] bit1 = { "", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX" };
        string [] bit10 = { "", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC" };
        string [] bit100 = { "", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM" };
        string [] bit1000= { "", "M", "MM", "MMM" };
        
        return  bit1000[num / 1000] + bit100[num % 1000 / 100] + bit10[num % 100 / 10] + bit1[num % 10];;
    }
}
```