---
title: 4种解法：查找最小的k个数
categories:
  - 技术资料
  - 算法
tags:
  - 最小k个数
  - 快排
  - 堆
  - 大顶堆
  - 排序查找
date: 2020-03-19 19:34:29
updated: 2020-03-19 19:34:29
description:  通过（快排、大顶堆、排序截取、查找排序）4种方法，查找数组或列表中的最小k个数...
---

## 题目
输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

 

示例 1：

输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
示例 2：

输入：arr = [0,1,2,1], k = 1
输出：[0]
 

限制：

0 <= k <= arr.length <= 10000
0 <= arr[i] <= 10000
***
## 解法一（查找排序）
思路：始终维持一个从小到大排列的有序列表，每次跟最后一个值（最大值）进行对比，如果比最大值小则替换，然后重新对列表排序保持有序
1. 结果列表未满之前，一直填入直到填满
2. 每次跟最后的值进行比较，如果比列表中值小，则替换
3. 每次操作对列表，都重新对列表进行排序

* 时间复杂度：O(N^2^logN)
*  空间复杂度：O(1)
```python
class Solution:
    # author: suoxd123@126.com
    def getLeastNumbers(self, arr: List[int], k: int) -> List[int]:        
        if k<= 0: #长度异常
            return []
        if k>=len(arr):
            return arr;
        idx = 0
        rst = list()
        for idx in range(0,k):#长度未填满
            rst.append(arr[idx])        
        rst = sorted(rst)
        for idx in range(k,len(arr)):
            flag = False
            if arr[idx] < rst[k-1]:#最后一个是最大值
                rst[k-1] = arr[idx]            
                flag = True
            if flag == True:
                rst = sorted(rst)
        return rst
```
***
## 解法二（排序截取）
思路：上面解法每次对结果排序浪费时间，当前优化通过首先对原始数据排序，然后取最小的k个。
1. 排序原数组
2. 提取前面k个值
* 时间复杂度：O(NlogN)
* 空间复杂度：O(N)

```python
class Solution:
    # author: suoxd123@126.com
    def getLeastNumbers(self, arr: List[int], k: int) -> List[int]:
        rst = sorted(arr)
        return rst[0:k]
```
***
## 解法三（大顶堆）
思路：大顶堆本身是一个从大到小排列的数据结构，因此维护一个仅有k个元素的大顶堆即可
1. python中的Heapq是一个小顶堆，因此对所有值取负值来表示大顶堆
2. 如果当前值大于堆顶元素，则替换
3. 将堆元素再取反，得到原值

* 时间复杂度：O(NlogN)
* 空间复杂度：O(1)
```python
class Solution:
    # author: suoxd123@126.com
    def getLeastNumbers(self, arr: List[int], k: int) -> List[int]:
        if k <= 0:
            return []   
        rst = [-v for v in arr[0:k]]
        heapq.heapify(rst)# 建堆
        for item in arr[k:len(arr)]:
            if -rst[0] > item:
                heapq.heapreplace(rst,-item) #更换堆顶元素
        return [-v for v in rst] #取反得到原值
```
***
## 解法四（快排）
思路：基于快排的思路，寻找分割坐标等于k的位置，则表示左边k个都小于右边，两边内部可能依旧是无序的。
1. 使用快排对列表进行初步的分割
2. 如果分割点小于k，则分割点在右边，继续对右边快排，大于k则在左边
3. 如果因为left>=right退出，说明k大于等于列表长度，因为left和right始终在向k方向移动

* 时间复杂度：O(n)，看起来是3层while循环，但数据只遍历了一遍
* 空间复杂度：O(1)
```python
class Solution:
    # author: suoxd123@126.com
    def getLeastNumbers(self, arr: List[int], k: int) -> List[int]:
        idx,left,right = 0,0,len(arr)-1
        while left<right and idx != k:
            val = arr[left]
            i,j = left,right
            while i < j: # 快排逻辑
                while i < j and arr[j] >= val:# 从右向左找小值 
                    j -= 1
                arr[i] = arr[j]
                while i < j and arr[i] <= val:# 从左向右找大值
                    i += 1
                arr[j] = arr[i]
            idx, arr[i] = i, val
            if idx < k: # 分界值在右边
                left = idx + 1
            elif idx > k: # k在左边
                right = idx - 1
        return arr[0:k]
```
