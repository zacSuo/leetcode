---
title: 3种解法 ： 计算使数组唯一的最小增量次数
categories:
  - 技术资料
  - 算法
tags:
  - 暴力法
  - 顺序查找
  - 贪心法
  - 唯一数组
date: 2020-03-22 19:30:29
updated: 2020-03-22 19:30:29
description: 通过（暴力法、顺序查找、贪心算法）3种方法，计算使数组唯一的最小增量次数...
---


## 题目
给定整数数组 A，每次 move 操作将会选择任意 A[i]，并将其递增 1。

返回使 A 中的每个值都是唯一的最少操作次数。

示例 1:

> 输入：[1,2,2]
> 输出：1
> 解释：经过一次 move 操作，数组将变为 [1, 2, 3]。

示例 2:

> 输入：[3,2,1,2,1,7]
> 输出：6
> 解释：经过 6 次 move 操作，数组将变为 [3, 4, 1, 2, 5, 7]。
可以看出 5 次或 5 次以下的 move 操作是不能让数组的每个值唯一的。

提示：

> 0 <= A.length <= 40000 
> 0 <= A[i] < 40000

***

## 解法一（暴力法）
思路：使用字典保存当前每个数值的个数，并为个数大于1的数值进行规则操作，直到数值个数等于原数组元素个数，累计每次操作总次数。
1. 将原数组分类为数值和对应个数的字典
2. 循环检查每个数值的个数，大于1则向前推进一个数值
3. 直到所有数值个数等于原数组中元素个数

* 时间复杂度：O(n^2^)
* 空间复杂度：O(n)

当前算法对于大部分测试用例通过了，在下面的测试用例超时了，该测试用例有40000（4万）个整数：
[https://www.zhenxiangsimple.com/files/tech/testCase20200322.txt](https://www.zhenxiangsimple.com/files/tech/testCase20200322.txt)
```python
class Solution:
    def minIncrementForUnique(self, A: List[int]) -> int:
        cnt,lenA = 0,len(A)
        dictA = collections.Counter(A)
        while len(dictA) < lenA:
            for k in list(dictA):
                v = dictA[k] - 1
                cnt += int((v+1)*v/2) # 等差数列求和
                dictA[k] = 1
                while v > 0:#依次向前递归
                    dictA[k+v] += 1
                    v -= 1
        return cnt
```

***

## 解法二（顺序查找）
思路：字典保存每个数值的个数，将数值从小到达进行排序，然后从小到达开始顺序访问，如果当前值个数大于1，则将向前寻找大于1部分数量的空位，直到遍历完原数组。

1. 获取数组中数值和对应个数，将数值对应的字典Key进行排序
2. 依次从小到大访问数值Key，遇到值大于1的键，向后寻找空位
3. 每次寻找到空位都累计当前行走的步长，并乘以当前所有剩余数值

* 时间复杂度：O(n^2^)
* 空间复杂度：O(n)

当前算法对于上面的测试用例通过了，但在下面的测试用例超时了，该测试用例也有40000（4万）个整数：
[https://www.zhenxiangsimple.com/files/tech/testCase202003221.txt](https://www.zhenxiangsimple.com/files/tech/testCase202003221.txt)

```python
class Solution:
    def minIncrementForUnique(self, A: List[int]) -> int:
        cnt,lenA = 0,len(A)
        dictA = collections.Counter(A)
        tmpKeys = sorted(list(dictA))# Key从小到大排序
        for k in tmpKeys:            
            v = dictA[k] - 1            
            dictA[k] = 1
            idx, iLen = 0,0
            while v > 0:
                iLen = 0
                while dictA.__contains__(k+idx):#顺序向前寻找空位
                    idx, iLen = idx + 1,iLen + 1
                cnt += iLen * v#剩余所有数值都递增了当前距离
                dictA[k+idx] = 1#填充最新的空位
                v -= 1
        return cnt
```
***
## 解法三（贪心算法）
思路：首先对原数据进行排序，然后记录每个位置允许存入的最小值，该值不小于上个位置的值加1，从小到达依次查看每个数据，直到遍历完所有数据
1. 对原数组排序
2. 记录上一个数加1作为表示当前最小值，记为preMax，使用当前值跟该值比较
3. 当前值最小需要move的距离就是到preMax，因此统计两数的差值

* 时间复杂度：O(nlogn)。这个复杂度花在排序上，执行其实是O(n)
* 空间复杂度：O(1)

```python
class Solution:
    def minIncrementForUnique(self, A: List[int]) -> int:
        cnt,preMax,lenA = 0,0,len(A)
        sortedA = sorted(A)# 原数组排序
        for i in range(0,lenA):
            if sortedA[i] <= preMax:# 当前值需要移动的距离
                cnt += preMax - sortedA[i]
            preMax = 1 + max(sortedA[i],preMax) # 向后推进
        return cnt
```
