---
title: 3种解法：实现字符串Z字形变换
categories:
  - 技术资料
  - 算法
tags:
  - 字符串变换
  - 数学推理
  - 按列存储
  - 按行存储
  - Z字形
date: 2020-02-07 19:34:29
updated: 2020-02-07 19:34:29
description:  通过数学推理、按列存储、按行存储，3种方法实现将一个字符串按Z字形变换...
---
## 题目
将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 "LEETCODEISHIRING" 行数为 3 时，排列如下：

L   C   I   R
E T O E S I I G
E   D   H   N
之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："LCIRETOESIIGEDHN"。

请你实现这个将字符串进行指定行数变换的函数：

string convert(string s, int numRows);
示例 1:

输入: s = "LEETCODEISHIRING", numRows = 3
输出: "LCIRETOESIIGEDHN"
示例 2:

输入: s = "LEETCODEISHIRING", numRows = 4
输出: "LDREOEIIECIHNTSG"
解释:

L     D     R
E   O E   I I
E C   I H   N
T     S     G

***
## 解法一（数学推理）
思路：每个独立的Z形状进行分组，然后计算得到每个分组成员的相对索引，根据排列规则翻译过来输出字符即可：从上到下翻译为按行遍历，从左到右翻译为按列遍历。
1. 将每组成员看为一个竖钩的形状，最后一组可能存在不全在的情况，因此每次访问前都检测是否越界
2. 第一行和最后一行仅有一个元素，中间行有两个元素，因此一个组的元素个数为：$numRows * 2 - 2$
3. 元素索引由组内索引加组索引：竖列中元素，其组内相对索引为行号$i$，加上组的索引$j*count$；勾列中元素，距离下一组元素开头的距离为$i$，因此使用下一组元素索引减去行号
* 时间复杂度：O(n)
* 空间复杂度：O(n)
```csharp
public class Solution {
    public string Convert(string s, int numRows) {
        if(numRows == 1)
        {//只有一行时，直接返回结果，防止下面除数为0
            return s;
        }
        StringBuilder sb = new StringBuilder();
        int count = numRows*2-2;
        int idx = 0,columns = (int)Math.Ceiling((double)s.Length / count);
        for(int i=0;i<numRows;i++)
        {//行
            for(int j=0;j<columns;j++)
            {//组
                idx = count*j + i;
                if(idx < s.Length)
                {
                    sb.Append(s[idx]);
                }
                if(i != 0 && i != numRows-1)
                {//首尾两行仅有一个字母
                    idx = count * (j+1) - i;
                    if(idx < s.Length)
                    {
                        sb.Append(s[idx]);
                    }
                }
            }
        }
        return sb.ToString();
    }
}
```
***
## 解法二（按列存储）
思路：将每列的子字符串都存储起来，对于单列只有一个字符的串，将其前后使用空格填充，在最后组合的时候，仅读取非空格的有效字符。
1. 跟解法一相同，先确定分组数量，依次对每组字符进行封装
2. 对每组数据，第一列字符数等于行数，后面每列空格依次从字符上面放到下面

* 时间复杂度：O(n^2^)，第三层循环为填充空格，用库函数的可以改为O(n)
* 空间复杂度：O(n)

```csharp
public class Solution {
    public string Convert(string s, int numRows) {
        if(numRows == 1)
        {
            return s;
        }
        StringBuilder sb = new StringBuilder();
        List<string> ts = new List<string>();
        int count = numRows*2-2;
        int columns = (int)Math.Ceiling((double)s.Length / count);
        for(int i = 0;i < columns;i++)
        {
            if(i*count + numRows > s.Length)
            {//最后一列
                ts.Add(s.Substring(i*count,s.Length - i*count));
            }
            else
            {
                ts.Add(s.Substring(i*count,numRows));//单组里面第一列
                for(int j=1;j<numRows-1;j++)
                {
                    if(i*count + numRows + j > s.Length)
                        break;//最后不够一组情况
                    sb.Clear();
                    for(int k=1;k<numRows-j;k++)
                        sb.Append(' ');//上面空格
                    sb.Append(s[i*count+numRows+j-1]);//当前字符
                    for(int k=0;k<j;k++)
                        sb.Append(' ');//下面空格
                    ts.Add(sb.ToString());
                }
            }
        }
        sb.Clear();
        for(int i=0;i<numRows;i++)
        {//行
            for(int j=0;j<ts.Count;j++)
            {//列
                if(i<ts[j].Length && ts[j][i] != ' ')
                    sb.Append(ts[j][i]);
            }
        }
        return sb.ToString();
    }
}
```
***
## 解法三（按行存储）
思路：从行的角度来看，字符是顺序先从上到下，再从下到上进行存储的，因此可以按照该思路，将字符存储到各行中，然后将每行的子串组合成结果。
1. 遇到首行或最后一行时候改变方向，沿着当前方向（向上或向下）进行存储
2. 将按行存储的结果，依次读取出来即可
* 时间复杂度：O(n)
* 空间复杂度：O(n)

```csharp
public class Solution {
    public string Convert(string s, int numRows) {
        if(numRows == 1)
        {
            return s;
        }
        List<StringBuilder> rs = new List<StringBuilder>(numRows);
        for(int i=0;i<numRows;i++)
        {
            rs.Add(new StringBuilder());
        }
        bool flag = false;
        int idx = 0;
        for(int i=0;i<s.Length;i++)
        {
            rs[idx].Append(s[i]);
            if(idx == 0 || idx == numRows - 1)
            {//第一行和最后一行，更换方向
                flag = !flag;
            }
            idx += flag?1:-1;
        }
        StringBuilder sb = new StringBuilder();
        foreach(StringBuilder item in rs)
        {
            sb.Append(item.ToString());
        }
        return sb.ToString();
    }
}
```
