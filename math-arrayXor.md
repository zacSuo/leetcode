---
title: 3个解法：子数组异或查询
categories:
  - 技术资料
  - 算法
tags:
  - 算法
  - 异或
  - 前缀和
date: 2020-01-05 19:34:29
updated: 2020-01-05 19:34:29
description: 使用三种算法，实现子数组异或求和查询的问题...
---

# 题目
有一个正整数数组 arr，现给你一个对应的查询数组 queries，其中 queries[i] = [Li, Ri]。

对于每个查询 i，请你计算从 Li 到 Ri 的 XOR 值（即 arr[Li] xor arr[Li+1] xor ... xor arr[Ri]）作为本次查询的结果。

并返回一个包含给定查询 queries 所有结果的数组。

 

示例 1：

输入：arr = [1,3,4,8], queries = [[0,1],[1,2],[0,3],[3,3]]
输出：[2,7,14,8] 
解释：
数组中元素的二进制表示形式是：
1 = 0001 
3 = 0011 
4 = 0100 
8 = 1000 
查询的 XOR 值为：
[0,1] = 1 xor 3 = 2 
[1,2] = 3 xor 4 = 7 
[0,3] = 1 xor 3 xor 4 xor 8 = 14 
[3,3] = 8
示例 2：

输入：arr = [4,8,2,10], queries = [[2,3],[1,3],[0,0],[0,3]]
输出：[8,0,4,4]
 

提示：

1 <= arr.length <= 3 * 10^4
1 <= arr[i] <= 10^9
1 <= queries.length <= 3 * 10^4
queries[i].length == 2
0 <= queries[i][0] <= queries[i][1] < arr.length

# 解法一
思路：
最直接的思路，就是直接对区间执行两层循环，进行遍历，但实际执行最后一个测试用例时超时，代码及测试用例如下（测试用例有525kb，应该是一个29898个数据的数组和2万4千多个[0，29898]，太长了，文档中只放了一部分），可以通过这个地址查看这个极限测试用例：[/files/tech/testCase20200105.txt](/files/tech/testCase20200105.txt?_blank)：
空间复杂度：O(1)
时间复杂度：O(k*m)，k为queries数组个数，m为数据差值

```csharp
public class Solution {
    public int[] XorQueries(int[] arr, int[][] queries) {
        int [] r = new int[queries.Length];
        for(int i=0;i<queries.Length;i++)
        {
            int t = 0;
            for(int j=queries[i][0];j<=queries[i][1];j++)
            {
                t = t^ arr[j];
            }
            r[i] = t;
        }
        return r;
    }
}
```

```csharp
[64038788,905710003,961785464,588745940,342531091,30097968,737396012,134887383,51163244,155478113,866300423,933134785,734680393,698721082,581135646,988184934,232804274,641391582,......[0,29898],[0,29898],[0,29898],[0,29898],[0,29898],[0,29898],[0,29898],[0,29898],[0,29898],[0,29898],[0,29898],[0,29898],[0,29898],[0,29898]]
```
# 解法二

思路：
由于解法一超时，想办法看看能否减少重复计算，当前算法使用空间换时间多思路，先将所有值计算出来，然后查找时候直接索引查找。但是，这种做法对内存消耗太大，对解法一超时的测试用例，当前解法超出了内存空间。事实上，程序在提交的时候，还没运行到最后一个测试用例，已经内存超出了，测试用例有668Kb，点击链接查看：[/files/tech/testCase202001051.txt](/files/tech/testCase202001051.txt?_blank)。
1，行表示当前计算的数据个数
2，列表示当前计算多起始索引
因此，最终只需要获取数据数和索引即可，代码如下：

空间复杂度：O(N^2^)，N为arr数组数据
时间复杂度：O(N^2^ + k)，k为queries数组个数

```csharp
public class Solution {
    public int[] XorQueries(int[] arr, int[][] queries) {
        int len = queries.Length, lenA = arr.Length;
        int [,] t = new int[lenA,lenA];
        for(int i=0;i<lenA;i++)
        {//首行
            t[0,i] = arr[i];
        }
        for(int i=1;i<lenA;i++)
        {//之后行
            for(int j=0;j<lenA-i;j++)
            {
                t[i,j] = t[i-1,j] ^ arr[j+i];
            }
        }
        
        int [] r = new int[len];
        for(int i=0;i<len;i++)
        {
            int rIdx=queries[i][1] - queries[i][0];
            r[i] = t[rIdx,queries[i][0]];
        }
        return r;
    }
}
```
# 解法三
思路：
使用前缀和的思路，即首先通过前缀累积，然后通过索引对应第值进行相减得到区间值，不过本算是多和改为异或，即进行前缀异或算法，索引N存储前N个数的异或值，并通过两个位置的和相减得到区间值。
对于异或来讲，加法和减法都是执行异或操作，即A xor A = 0或者 A xor B xor A = B。
代码如下：
空间复杂度：O(N)，N为arr数组数据
时间复杂度：O(N+k)，k为queries数组个数

```csharp
public class Solution {
    public int[] XorQueries(int[] arr, int[][] queries) {
        int len=queries.Length, lenA = arr.Length;
        int [] t = new int[lenA+1];
        t[0] = 0;
        for(int i=1;i<=lenA;i++)
        {
            t[i] = t[i-1] ^ arr[i-1];
        }
        
        int [] r = new int[len];
        for(int i=0;i<len;i++)
        {
            r[i] = t[queries[i][1]+1] ^ t[queries[i][0]];
        }
        return r;
    }
}
```
<center><img src="/img/tech/20200105130424152.png" title="解法三"/></center>