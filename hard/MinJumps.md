---
title: 2种解法：实现跳跃游戏
categories:
  - 技术资料
  - 算法
tags:
  - 暴力法
  - 双指针法
  - 广度优先搜索
  - 队列
date: 2020-02-10 19:34:29
updated: 2020-02-10 19:34:29
description:  两种算法都使用了队列结构和广度优先搜索算法，分别使用双指针法和暴力法对跳跃的计数，来确定最小跳跃次数...
---

## 题目

[NO. 1345](https://leetcode-cn.com/problems/jump-game-iv)

你一个整数数组 arr ，你一开始在数组的第一个元素处（下标为 0）。

每一步，你可以从下标 i 跳到下标：

i + 1 满足：i + 1 < arr.length
i - 1 满足：i - 1 >= 0
j 满足：arr[i] == arr[j] 且 i != j
请你返回到达数组最后一个元素的下标处所需的 最少操作次数 。

注意：任何时候你都不能跳到数组外面。

 

**示例 1：**
输入：arr = [100,-23,-23,404,100,23,23,23,3,404]
输出：3
解释：那你需要跳跃 3 次，下标依次为 0 --> 4 --> 3 --> 9 。下标 9 为数组的最后一个元素的下标。

**示例 2：**
输入：arr = [7]
输出：0
解释：一开始就在最后一个元素处，所以你不需要跳跃。

**示例 3：**
输入：arr = [7,6,9,6,9,6,9,7]
输出：1
解释：你可以直接从下标 0 处跳到下标 7 处，也就是数组的最后一个元素处。 

**提示：**

1 <= arr.length <= 5 * 10^4
-10^8 <= arr[i] <= 10^8

## 解法一（双指针法）
思路：当前解法通过双指针来标识入度和出度，即通过出度表示当前是否完成，并开始进入下一层的迭代，直到有指针指向最后一个元素，完成任务。
1. inCount 入指针用于记录当前层入队数，outCount出指针记录上一层入队数（当前需要出队数）
2. 看起来有个三层循环，但每个数值只访问一次；不过，数值还是出现多访问，只是不修改，因此时间复杂度最坏可能是$n!$，在构建字典时候为了防止出现最坏情况，索引选择从大到小，即如果有重复值出现，优先查看后面的值。
* 时间复杂度：O(n^2)
* 空间复杂度：O(n)
```csharp
public class Solution {
    
    public int MinJumps(int[] arr) {
        Dictionary<int,List<int>> dict = new Dictionary<int,List<int>>();
        for(int i=arr.Length-1;i>=0;i--)
        {//记录重复数据，方便快速查询
            if(!dict.ContainsKey(arr[i]))
            {
                dict.Add(arr[i],new List<int>());
                
            }
            dict[arr[i]].Add(i);
        }
        bool []r = new bool[arr.Length];
        int count = 0,idx=0,len = arr.Length - 1;
        int outCount,inCount = 1;
        bool flag =　false;
        r[0] = true;
        Queue<int> q = new Queue<int>();
        q.Enqueue(0);
        while(r[len] == false)
        {
            outCount = inCount;
            inCount = 0;
            while(outCount > 0)
            {//出指针计数完
                idx = q.Dequeue();
                outCount--;
                if(idx<len && r[idx+1] == false)
                {
                    r[idx + 1] = true;
                    q.Enqueue(idx+1);
                    inCount++;
                    if(idx + 1 == len)
                    {
                        return count+1;
                    }
                }
                if(idx > 0 && r[idx-1] == false)
                {
                    r[idx - 1] = true;             
                    q.Enqueue(idx-1);
                    inCount++;
                    if(idx - 1 == len)
                    {
                        return count+1;
                    }
                }
                List<int> tmpList = dict[arr[idx]];
                foreach(int i in tmpList)
                {
                    if(arr[i] == arr[idx] && r[i] == false && i != idx)
                    {
                        r[i] = true;           
                        q.Enqueue(i);
                        inCount++;
                        if(i == len)
                        {
                            return count+1;
                        }
                    }
                }
            }
            count++;
        }
        return count;
    }
}
```

## 解法二（暴力法）
思路：使用数组记录每个元素访问时候的层次，由于每个元素首次被访问时候是最大的，下次再被访问的次数都不小于当前值，因此只需要付一个最大的初值，只要不等于初始值就表示已经访问过了。
1. 记录值访问次数的数组r的初始化，由于最差的结果就是一个一个向后访问，即等于数组长度减一，因此给除了第一个元素外的所有元素赋数组长度（任何一直值被访问到的路径都小于数组长度）
2. r元素一方面用于记录跳跃次数，另一方面也可以作为访问标识位。
3. 遇到结尾就要及时跳出，否则会由于队列中元素太多而超时（也可以使用一个List来记录是否在队列中，进行优化）。如果不跳出，对最后一个测试用例会超时，该测试用例及源码如下：
* 时间复杂度：O(n^2)
* 空间复杂度：O(n)
* 不跳出会超时的用例：[https://www.zhenxiangsimple.com/files/tech/testCase20200210.txt](https://www.zhenxiangsimple.com/files/tech/testCase20200210.txt)
```csharp
public class Solution {
    public int MinJumps(int[] arr) {
        Dictionary<int,List<int>> dict = new Dictionary<int,List<int>>();
        for(int i=arr.Length-1;i>=0;i--)
        {//记录重复数据，方便快速查询
            if(!dict.ContainsKey(arr[i]))            
                dict.Add(arr[i],new List<int>());                
            dict[arr[i]].Add(i);
        }
        int []r = new int[arr.Length];
        for(int i=1;i<r.Length;i++)
            r[i] = r.Length;//默认最大值
        Queue<int> q = new Queue<int>();
        int idx;
        q.Enqueue(0);
        while(q.Count > 0)
        {
            idx = q.Dequeue();
            foreach(int i in dict[arr[idx]])
            {
                if(r[i] == r.Length)
                {
                    r[i] = r[idx] + 1;
                    q.Enqueue(i);
                }
                
                if(i == arr.Length - 1) 
                    break;
            }
            if(idx + 1 < arr.Length && r[idx+1] == r.Length)
            {
                r[idx+1] = r[idx] + 1;
                q.Enqueue(idx + 1);
                
                if(idx+1 == arr.Length - 1) 
                    break;
            }
            if(idx - 1 >= 0 && r[idx - 1] == r.Length)
            {
                r[idx-1] = r[idx] + 1;
                q.Enqueue(idx - 1);
            }
        }
        return r[arr.Length - 1];
    }
}
```
