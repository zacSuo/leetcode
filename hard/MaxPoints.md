---
title: 3种解法：计算直线上最多的点数
categories:
  - 技术资料
  - 算法
tags:
  - 直线斜率
  - 辗转相除
  - 最简分子式
date: 2020-02-03 19:34:29
updated: 2020-02-03 19:34:29
description:  基于直线斜率，统计在一条直线上的最多的坐标数...
---
## 题目

[NO. 149](https://leetcode-cn.com/problems/max-points-on-a-line/)

给定一个二维平面，平面上有 n 个点，求最多有多少个点在同一条直线上。

示例 1:

输入: [[1,1],[2,2],[3,3]]
输出: 3
解释:
^
|
|        o
|     o
|  o  
+------------->
0  1  2  3  4
示例 2:

输入: [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
输出: 4
解释:
^
|
|  o
|     o        o
|        o
|  o        o
+------------------->
0  1  2  3  4  5  6

***
## 解法一（直线斜率）
思路：以某一个点A为基础点，查看所有其他点跟他形成直线的斜率，如果另外两个点B、C跟A连线的斜率一样，则ABC三个点则在同一条直线上，使用两点式的直线方程来计算斜率：$\frac{y-y_1}{x-x_1}=\frac{y_1-y_2}{x_1-x_2}$。
1. 为了计算方便先统计重复出现的点
2. 依次以单点为基础循环计算斜率，如果斜率一致则累计为同一条直线（和基础点重复则跳过、垂直X轴作为特殊情况使用最大值，其他值的计算使用直线公式：$k=\frac{y_1-y_2}{x_1-x_2}$）
3. 最大值加上基础点坐标重复出现的次数

* 时间复杂度：O(n^2^)
* 空间复杂度：O(n)
* <font color="red">这个方案有个问题</font>：如果两个点斜率非常接近，以至于超过double类型的分辨率，那这个方案即失效了。比如：
[[0,0],[2147483640,2147483641],[2147483641,2147483642]]
```csharp
public class Solution {
    public int MaxPoints(int[][] points) {
        string strFormat = "{0}_{1}",strTmp;

        int count = points.Length;
        if(count == 1) return 1;
        Dictionary<double,int> r = new Dictionary<double,int>();
        List<string> list = new List<string>();
        Dictionary<string,int> s = new Dictionary<string,int>();
        for(int i=0;i<count;i++)
        {//记录重复的点
            strTmp = string.Format(strFormat,points[i][0],points[i][1]);
            if(list.Contains(strTmp))
            {
                if(s.ContainsKey(strTmp))
                {
                    s[strTmp] ++;
                }
                else
                {
                    s[strTmp] = 1;
                }
            }
            else
            {
                list.Add(strTmp);
            }
        }
        double a;
        int mx=0,mxTmp;
        for(int i=0;i<count;i++)
        {
            r.Clear();
            mxTmp = 1;//坐标i自己
            for(int j=i+1;j<count;j++)
            {
                if(points[i][0] == points[j][0] && points[i][1] == points[j][1])
                {//节点重复跳过，放在最后计算
                    continue;
                }
                else if(points[i][0] == points[j][0])
                {//垂直Y轴直线
                    a = double.MaxValue;
                }
                else
                {//一般直线
                    a = (double)(points[i][1] - points[j][1])/(points[i][0] - points[j][0]);
                }
                if(r.ContainsKey(a))
                {//多个点在同一条线上
                    r[a]++;   
                }
                else
                {//两个点新建一条线
                    r[a] = 1;
                }
                if(r[a]+1>mxTmp)//+1表示坐标i自己
                {//记录最大值
                    mxTmp = r[a]+1;
                }
            }
            strTmp = string.Format(strFormat,points[i][0],points[i][1]);
            if(s.ContainsKey(strTmp))
            {//计算重复值
                mxTmp += s[strTmp];
            }
            mx = mx > mxTmp?mx:mxTmp;
        }
        return mx;
    }
}
```
***
## 解法二（最简分子式）
思路：由于解法一斜率的精度问题，使用分子分母的最简分子式来代替除法的精度丢失，即分子分母同时除以最大公约数，并通过分子分母组成的字符来代替double型。
1. 使用辗转相除法得到最大公约数，然后得到最简分子式，从而得到代替double值的字符串Key
2. 特殊垂直于X轴的直线，直接构建字符串
* 时间、空间复杂度跟解法一完全一致
```csharp
public class Solution {
    public string getKey(int up,int down)
    {//使用分数转换为字符串Key
        int a=up,b=down,c;
        while(a!=0)
        {
            c = b % a;
            b = a;
            a = c; 
        }
        return string.Format("{0}_{1}",up/b,down/b);
    }
    public int MaxPoints(int[][] points) {
        string strFormat = "{0}_{1}",strTmp;

        int count = points.Length;
        if(count == 1) return 1;
        Dictionary<string,int> r = new Dictionary<string,int>();
        List<string> list = new List<string>();
        Dictionary<string,int> s = new Dictionary<string,int>();
        for(int i=0;i<count;i++)
        {//记录重复的点
            strTmp = string.Format(strFormat,points[i][0],points[i][1]);
            if(list.Contains(strTmp))
            {
                if(s.ContainsKey(strTmp))
                {
                    s[strTmp] ++;
                }
                else
                {
                    s[strTmp] = 1;
                }
            }
            else
            {
                list.Add(strTmp);
            }
        }
        string strKey;
        int mx=0,mxTmp;
        for(int i=0;i<count;i++)
        {
            r.Clear();
            mxTmp = 1;//坐标i自己
            for(int j=i+1;j<count;j++)
            {
                if(points[i][0] == points[j][0] && points[i][1] == points[j][1])
                {//节点重复跳过，放在最后计算
                    continue;
                }
                else if(points[i][0] == points[j][0])
                {//垂直Y轴直线
                    strKey = string.Format(strFormat,points[i][0],0);
                }
                else
                {//一般直线
                    strKey = getKey(points[i][1] - points[j][1],points[i][0] - points[j][0]);
                }
                if(r.ContainsKey(strKey))
                {//多个点在同一条线上
                    r[strKey]++;   
                }
                else
                {//两个点新建一条线
                    r[strKey] = 1;
                }
                if(r[strKey]+1>mxTmp)//+1表示坐标i自己
                {//记录最大值
                    mxTmp = r[strKey]+1;
                }
            }
            strTmp = string.Format(strFormat,points[i][0],points[i][1]);
            if(s.ContainsKey(strTmp))
            {//计算重复值
                mxTmp += s[strTmp];
            }
            mx = mx > mxTmp?mx:mxTmp;
        }
        return mx;
    }
}
```
