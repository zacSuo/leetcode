---
title: 3种解法： 灌溉花园的最少水龙头数目
categories:
  - 技术资料
  - 算法
tags:
  - 深度优先搜索
  - 广度优先搜索
  - 贪心算法
  - 最大覆盖
date: 2020-01-19 19:34:29
updated: 2020-01-19 19:34:29
description: 使用（深度优先搜索、广度优先搜索、贪心算法）3种解法，对时限灌溉花园的最少水龙头数目计算...
---

## 题目
在 x 轴上有一个一维的花园。花园长度为 n，从点 0 开始，到点 n 结束。

花园里总共有 n + 1 个水龙头，分别位于 [0, 1, ..., n] 。

给你一个整数 n 和一个长度为 n + 1 的整数数组 ranges ，其中 ranges[i] （下标从 0 开始）表示：如果打开点 i 处的水龙头，可以灌溉的区域为 [i -  ranges[i], i + ranges[i]] 。

请你返回可以灌溉整个花园的 最少水龙头数目 。如果花园始终存在无法灌溉到的地方，请你返回 -1 。

 

示例 1：

<center><img src="/img/tech/20200119123658920.png" title="示意图"/></center>

输入：n = 5, ranges = [3,4,1,1,0,0]
输出：1
解释：
点 0 处的水龙头可以灌溉区间 [-3,3]
点 1 处的水龙头可以灌溉区间 [-3,5]
点 2 处的水龙头可以灌溉区间 [1,3]
点 3 处的水龙头可以灌溉区间 [2,4]
点 4 处的水龙头可以灌溉区间 [4,4]
点 5 处的水龙头可以灌溉区间 [5,5]
只需要打开点 1 处的水龙头即可灌溉整个花园 [0,5] 。
示例 2：

输入：n = 3, ranges = [0,0,0,0]
输出：-1
解释：即使打开所有水龙头，你也无法灌溉整个花园。
示例 3：

输入：n = 7, ranges = [1,2,1,0,2,1,0,1]
输出：3
示例 4：

输入：n = 8, ranges = [4,0,0,0,0,0,0,0,4]
输出：2
示例 5：

输入：n = 8, ranges = [4,0,0,0,4,0,0,0,4]
输出：1
 

提示：

1 <= n <= 10^4
ranges.length == n + 1
0 <= ranges[i] <= 100
***
## 解法一（深度优先搜索）
思路：核心就是找出可以覆盖[0,n]的最小数组个数，那就把数组的头按递归排序，通过头尾交集来检查区域之间是否连续
1. 求出各个数据覆盖的区域
2. 找出无效或者其它区域的子域，仅对有效区域进行计算
3. 为了方便递归，先对所有区域的开端进行递增排序
4. 对每个区域开始，从前到后依次进行递归查询，如果满足条件，则记录最小的值，是深度优先算法的思想。

* 时间复杂度：O(n^2^+n!)，n^2^是主函数的前三步，n!是第四步的递归函数
* 空间复杂度：O(n)，存储区域的数组r
    
这是个顺序思维，可以满足功能要求成功执行，但时间复杂度太高了，结果就是仅通过了13/31个测试用例，未通过的超时了，超时测试用例及代码如下：

```csharp
68
[0,0,0,1,4,2,2,2,2,4,0,0,0,5,4,0,0,5,3,0,1,1,5,1,1,2,4,1,0,4,3,5,1,0,3,3,4,2,2,4,3,1,1,0,4,0,2,1,4,0,0,3,3,1,1,4,4,2,0,3,4,0,1,5,3,0,1,0,2]
```

```csharp
public class Solution {
        public int getLinkCount(int[,] r,List<int> rList,int count,int len)
        {
            int minTemp = len;
            bool flag = true;
            for (int i = 1; i < rList.Count; i++)
            {//区域不连续
                if (r[rList[i - 1], 1] < r[rList[i], 0])
                {
                    flag = false;
                    break;
                }
            }
            if (flag && r[rList[0], 0] <= 0 && r[rList[rList.Count - 1], 1] >= len-1)
                return rList.Count;

            for(int i=count+1;i<len;i++)
            {//从前到后做深度优先搜索的递归
                if(r[i,2] == 1)
                {
                    rList.Add(i);
                    int tmp = getLinkCount(r, rList, i, len);
                    if (tmp > 0 && tmp < minTemp) 
                        minTemp = tmp; ;
                    rList.Remove(i);
                }
            }
            return minTemp;
        }

        public int MinTaps(int n, int[] ranges)
        {
            //求区域
            int len = ranges.Length;
            int[,] r = new int[len,3];
            for(int i=0;i<len;i++)
            {
                r[i,0] = i - ranges[i];
                r[i, 1] = i + ranges[i];
                r[i, 2] = 1;//有效性
            }
            //求有效数据
            for (int i = 0; i < len; i++)
            {
                if(r[i,2] == 0) continue;
                for (int j = i+1; j < len; j++)
                {
                    if(r[j,2] == 0) continue;
                    if (r[i, 0] <= r[j, 0] && r[i, 1] >= r[j, 1])
                    {
                        r[j, 2] = 0;
                    }
                    else if (r[i, 0] >= r[j, 0] && r[i, 1] <= r[j, 1])
                    {
                        r[i, 2] = 0;
                    }
                }                
            }
            //对数组排序
            int t;
            for (int i = 0; i < len; i++)
            {
                if (r[i, 2] == 0) continue;
                for (int j = i+1; j < len; j++)
                {
                    if (r[j, 2] == 0) continue;
                    if (r[i, 0] > r[j, 0])
                    {
                        t = r[i, 0];
                        r[i, 0] = r[j,0];
                        r[j, 0] = t;

                        t = r[i, 1];
                        r[i, 1] = r[j, 1];
                        r[j, 1] = t;
                    }
                }
            }
            //求并集
            int rInt = len;
            for(int i=0;i<len;i++)
            {//对每个区域依次进行查找
                if(r[i,2] == 0) continue;
                List<int> tList = new List<int> ();
                tList.Add(i);
                t = getLinkCount(r,tList,1,len);
                if (t > 0 && t < rInt)
                    rInt = t;
            }
            return rInt == len?-1:rInt;
        }
}
```

***
## 解法二（广度优先搜索）
思路：基于解法一做了以下优化
1. 使用空间换时间，使用另一个数组保存有效数组，不用每次都判断
2. continue改为break，因为本身是有序数组，跟当前值不连续也一定跟后面的不连续
3. 第一种的深度优先搜索改为广度优先搜索，即一个区域查到底改为一层一层查，先查出的结果就是最小的，后面就不用查了
4. 广度搜索时候，为了节省时间，使用List<dataSearch> rList存储上一次计算的区间结果，为了节省空间，仅保留一层计算结果。比如计算第三层时，直接使用第二层的结果，并在计算得到第三层时，删除第二层的结果。

* 时间复杂度：O（n^3^）
* 空间复杂度：O（n!）
* 本身由于顺序原因得到结果会快不少；对于上面超时用例通过了，但是遇到79时还是超时了，超时用例及代码如下：

```csharp
79
[5,0,2,5,5,0,2,5,0,2,5,2,3,1,3,4,0,1,2,5,4,4,4,5,4,2,5,1,3,2,1,4,3,1,5,5,2,4,2,4,5,1,0,1,1,5,1,3,5,5,1,3,0,1,3,1,3,0,5,0,3,4,3,3,5,2,4,0,5,5,1,4,2,2,3,0,2,1,1,4]
```

```csharp
public class Solution {
        public struct dataSearch
        {
            public int minValue;
            public int maxValue;
            public int maxIdx;
            public int count;
            public dataSearch(dataSearch data)
            {
                minValue = data.minValue;
                maxValue = data.maxValue;
                maxIdx = data.maxIdx;
                count = data.count;
            }
            public dataSearch(int m,int n,int i,int c)
            {
                minValue = m;
                maxValue = n;
                maxIdx = i;
                count = c;
            }
        }

        public int getLinkCount(int [,] r,int count,int n,List<dataSearch> rList)
        {
            int rCount = rList.Count;
            for(int i=0;i<rCount;i++)
            {
                for (int j = rList[i].maxIdx+1; j < count; j++)
                {
                    if (rList[i].maxValue < r[j, 0])
                    {//不连续
                        break;//不用continue，是因为数组是有序的，r[j,0]是后面里最小的，即r[j,0] <= r[j+k,0]
                    }
                    dataSearch data = new dataSearch(rList[i]);
                    data.count++;
                    data.minValue = data.minValue > r[j, 0]? r[j, 0]:data.minValue ;
                    data.maxValue = data.maxValue > r[j, 1] ? data.maxValue : r[j, 1];
                    data.maxIdx = j;
                    rList.Add(data);
                    if (data.minValue <= 0 && data.maxValue >= n)
                    {
                        return data.count;
                    }
                }
                rList.RemoveAt(i);
                i--;
                rCount--;
            }
            return -1;
        }

        public int MinTaps(int n, int[] ranges)
        {
            //求区域
            int len = ranges.Length;
            int[,] r = new int[len,3];
            for(int i=0;i<len;i++)
            {
                r[i,0] = i - ranges[i];
                r[i, 1] = i + ranges[i];
                r[i, 2] = r[i,1] - r[i,0];//有效性
            }
            //求有效数据
            for (int i = 0; i < len; i++)
            {
                if(r[i,2] == 0) continue;
                for (int j = i+1; j < len; j++)
                {
                    if(r[j,2] == 0) continue;
                    if (r[i, 0] <= r[j, 0] && r[i, 1] >= r[j, 1])
                    {
                        r[j, 2] = 0;
                    }
                    else if (r[i, 0] >= r[j, 0] && r[i, 1] <= r[j, 1])
                    {
                        r[i, 2] = 0;
                    }
                }                
            }
            //存储有效数据
            int vCount = 0;
            for (int i = 0; i < len; i++)
            {
                if (r[i, 2] > 0)
                    vCount++;
            }
            int[,] rv = new int[vCount, 2];
            int idx = 0;
            for (int i = 0; i < len; i++)
            {
                if (r[i, 2] > 0)
                {
                    rv[idx, 0] = r[i, 0];
                    rv[idx, 1] = r[i, 1];
                    idx++;
                }
            }
            if (vCount == 0) return -1;
            else if (vCount == 1)
            {//特殊情况处理
                if (rv[0, 0] <= 0 && rv[0, 1] >= n) return 1;
                else return -1;
            }
            //对数组排序
            int t;
            for (int i = 0; i < vCount; i++)
            {
                for (int j = i + 1; j < vCount; j++)
                {
                    if (rv[i, 0] > rv[j, 0])
                    {
                        t = rv[i, 0];
                        rv[i, 0] = rv[j,0];
                        rv[j, 0] = t;

                        t = rv[i, 1];
                        rv[i, 1] = rv[j, 1];
                        rv[j, 1] = t;
                    }
                }
            }
            //求并集
            List<dataSearch> rList = new List<dataSearch>();
            for(int i=0;i<vCount;i++)
            {//单个元素初始
                if (rv[i, 0] <= 0 && rv[i, 1] >= n) 
                    return 1;
                rList.Add(new dataSearch(rv[i,0],rv[i,1],i,1));
            }
            for (int i = 1; i < len; i++)
            {//多个元素迭代遍历
                t = getLinkCount(rv,vCount, n, rList);
                if (t > 0) return t;
            }
            return -1;
        }
}
```
***
## 解法三（贪心算法）
思路：基于解法二的搜索算法改为贪心算法，来减少List不断的装箱拆箱的耗时，即不用顺序搜索，直接每次从开始找可以找到的最大结束位置，直到到达终点或搜索结束。
1. 左边界起点是0，右边界终点是n
2. 从左边界开始，每次找当前循环的最大右边界，直到终点
3. 需要补充的是，由于始终在向右遍历，新找到的一定不会被访问过，所以不需要做是否被访问的标记

* 时间复杂度：O(n^2^)
* 空间复杂度：O(n)

```csharp
public class Solution {
        public int MinTaps(int n, int[] ranges)
        {
            //求区域
            int len = ranges.Length;
            int[,] r = new int[len,3];
            for(int i=0;i<len;i++)
            {
                r[i,0] = i - ranges[i];
                r[i, 1] = i + ranges[i];
                r[i, 2] = r[i,1] - r[i,0];//有效性
            }
            //求有效数据
            for (int i = 0; i < len; i++)
            {
                if(r[i,2] == 0) continue;
                for (int j = i+1; j < len; j++)
                {
                    if(r[j,2] == 0) continue;
                    if (r[i, 0] <= r[j, 0] && r[i, 1] >= r[j, 1])
                    {
                        r[j, 2] = 0;
                    }
                    else if (r[i, 0] >= r[j, 0] && r[i, 1] <= r[j, 1])
                    {
                        r[i, 2] = 0;
                    }
                }                
            }
            //存储有效数据
            int vCount = 0;
            for (int i = 0; i < len; i++)
            {
                if (r[i, 2] > 0)
                    vCount++;
            }
            int[,] rv = new int[vCount, 2];
            int idx = 0;
            for (int i = 0; i < len; i++)
            {
                if (r[i, 2] > 0)
                {
                    rv[idx, 0] = r[i, 0];
                    rv[idx, 1] = r[i, 1];
                    idx++;
                }
            }
            if (vCount == 0) return -1;
            else if (vCount == 1)
            {//特殊情况处理
                if (rv[0, 0] <= 0 && rv[0, 1] >= n) return 1;
                else return -1;
            }
            //对数组排序
            int t;
            for (int i = 0; i < vCount; i++)
            {
                for (int j = i + 1; j < vCount; j++)
                {
                    if (rv[i, 0] > rv[j, 0])
                    {
                        t = rv[i, 0];
                        rv[i, 0] = rv[j,0];
                        rv[j, 0] = t;

                        t = rv[i, 1];
                        rv[i, 1] = rv[j, 1];
                        rv[j, 1] = t;
                    }
                }
            }
            int minValue = 0, maxValue = 0;
            for (int i = 0; i < vCount; i++)
            {
                maxValue = minValue;
                for (int j = i; j < vCount; j++)
                {
                    if(rv[j,0] <= minValue && rv[j,1] >= maxValue)
                    {//找最大值边界
                        maxValue = rv[j,1];
                    }
                }
                if(maxValue == minValue) return -1;//没找到
                if(maxValue  >= n)return i+1; //到达最大值边界
                minValue = maxValue;
            }

            return -1;
        }
}
```
