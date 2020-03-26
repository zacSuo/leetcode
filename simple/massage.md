---
title: 2种解法：斐波那列马甲问题
categories:
  - 技术资料
  - 算法
tags:
  - 斐波那列
  - 递归
  - 动态规划
  - 单链表
date: 2020-03-24 19:30:29
updated: 2020-03-24 19:30:29
description: 通过（递归、动态规划）2种方法，计算斐波那列马甲问题（数组跳跃计算问题）...
---


## 题目

[NO. 1496](https://leetcode-cn.com/problems/the-masseuse-lcci/)

一个有名的按摩师会收到源源不断的预约请求，每个预约都可以选择接或不接。在每次预约服务之间要有休息时间，因此她不能接受相邻的预约。给定一个预约请求序列，替按摩师找到最优的预约集合（总预约时间最长），返回总的分钟数。

注意：本题相对原题稍作改动 

示例 1：

>输入： [1,2,3,1]
输出： 4
解释： 选择 1 号预约和 3 号预约，总时长 = 1 + 3 = 4。

示例 2：

>输入： [2,7,9,3,1]
输出： 12
解释： 选择 1 号预约、 3 号预约和 5 号预约，总时长 = 2 + 9 + 1 = 12。

示例 3：

>输入： [2,1,4,5,3,1,1,3]
输出： 12
解释： 选择 1 号预约、 3 号预约、 5 号预约和 8 号预约，总时长 = 2 + 4 + 3 + 3 = 12。
***
## 解法一（递归）
思路：类似斐波那列的思想，即索引k取值结果等于k-1的结果 与 k-2加当前值的和 的最大值，即如下公式，使用递归实现：$S(i) = max(S(i-1), nums[i] + S(i-2))$
1. 对于数组长度为0的情况，进行特殊判断
2. 在递归中实现对应索引的求解

* 时间复杂度：O（2^n）
* 空间复杂度：O（n）

```python
# author: suoxd123@126.com
class Solution:
    def fib(self,arr, n):
        if n == 1:
            return arr[0]
        elif n == 2:
            return max(arr[0],arr[1])
        # 递归公式
        return max(arr[n-1] + self.fib(arr,n-2), self.fib(arr,n-1))

    def massage(self, nums: List[int]) -> int:
        numLen = len(nums) 
        if numLen == 0:
            return 0
        return self.fib(nums, numLen)
```
***
## 解法二（动态规划）
思路：原理还是斐波那列公式，使用动态规划思想实现，使用循环代替递归，使用rst列表保存每个索引位置的状态结果，当然也可以不用列表，选择三个变量来存储临时值，是的空间复杂度变为1.

1. 特殊情况处理
2. 使用rst存储每个索引处的值，从前到后递归
3. 返回结果的最后一个值

* 时间复杂度：O（n）
* 空间复杂度：O(n) 或 O(1)
```python
# author: suoxd123@126.com
class Solution:
    def massage(self, nums: List[int]) -> int:
        numLen = len(nums) 
        if numLen == 0:
            return 0
        elif numLen == 1:
            return nums[0]
        # 这里空间复杂度改为O(n)    
        rst = [nums[0],max(nums[0],nums[1])]
        for i in range(2,numLen):
            rst.append(max(rst[i-1],rst[i-2] + nums[i]))
        return rst[-1]
        # 下面空间复杂度为O(1)
        #rst1, rst2 = nums[0],max(nums[0],nums[1])
        #for i in range(2,numLen):
        #    rst1, rst2=  rst2, max(rst2,rst1 + nums[i])
        #return rst2
```
