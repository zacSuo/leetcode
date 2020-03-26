---
title: 3种方法：计算三维形体的表面积
categories:
  - 技术资料
  - 算法
tags:
  - 立方体
  - 表面积
  - 几何
  - 面积累加
  - 重叠相减
date: 2020-03-25 19:30:29
updated: 2020-03-25 19:30:29
description: 通过（按立方体加、按立方体减、按坐标减）3种方法，分别计算三维形体的表面积...
---

## 题目

[NO. 392](https://leetcode-cn.com/problems/surface-area-of-3d-shapes/)

在 N * N 的网格上，我们放置一些 1 * 1 * 1  的立方体。

每个值 v = grid[i][j] 表示 v 个正方体叠放在对应单元格 (i, j) 上。

请你返回最终形体的表面积。

 

示例 1：

>输入：[[2]]
输出：10

示例 2：

>输入：[[1,2],[3,4]]
输出：34

示例 3：

>输入：[[1,0],[0,2]]
输出：16

示例 4：
>输入：[[1,1,1],[1,0,1],[1,1,1]]
输出：32

示例 5：
>输入：[[2,2,2],[2,1,2],[2,2,2]]
输出：46
 

提示：

> 1 <= N <= 50
0 <= grid[i][j] <= 50
***

## 题解
题目本身从描述看理解不太容易，我也是看了10几遍才理解，简单解释下：

#### 示例一：[[2]]
表示在二维平面上，坐标为（0,0）的位置上，有一个高度为2的立方体
#### 示例 2：[[1,2],[3,4]]
以左下角为原点（0,0），不同位置表示立方体高度，立方体的排列方式如下：
3 4
1 2
#### 示例 3：[[1,0],[0,2]]
立方体的排列方式如下：
0 2
1 0
#### 示例 4：[[1,1,1],[1,0,1],[1,1,1]]
立方体的排列方式如下：
1 1 1
1 0 1
1 1 1


 <center>
    <img src="/img/tech/20200325130312830.jpg" title="示例4"/>
    </center>

#### 示例 5：[[2,2,2],[2,1,2],[2,2,2]]
立方体的排列方式如下：
2 2 2
2 1 2
2 2 2
***
## 解法一（按立方体加）
思路：对每个坐标位置的立方体，对6个面暴露的部分累加求和，分别站在每个面前面，从前到后进行求解，当然从算法层面可以对程序进行优化，但为了代码可读性依然保留了写作初始思路。
1. 只要有立方体（值大于0），则上下两个面的值都会暴露
2. 对于第一排的立方体，暴露的面等于立方体高度（值大小）
3. 对于后排的立方体，暴露的面等于高出前面立方体的高度（后面值减去当前值）

* 时间复杂度：O（M*N）
* 空间复杂度：O（1）

```python
# author： suoxd123@126.com
class Solution:
    def surfaceArea(self, grid: List[List[int]]) -> int:
        cnt, m,n = 0, len(grid),len(grid[0])        
        for i in range(0,m):
            for j in range(0,n):
                # 上下面
                if grid[i][j] > 0:
                    cnt += 2
                # 前面
                if i == 0: # 第一排
                    cnt += grid[i][j]
                elif grid[i][j] > grid[i-1][j]:# 后排
                    cnt += grid[i][j] - grid[i-1][j]
                # 后面
                if i == m - 1: # 第一排
                    cnt += grid[i][j]
                elif grid[i][j] > grid[i+1][j]: # 后排
                    cnt += grid[i][j] - grid[i+1][j]
                # 左面
                if j == 0: # 第一排
                    cnt += grid[i][j]
                elif  grid[i][j] > grid[i][j-1]: # 后排
                    cnt += grid[i][j] - grid[i][j-1]
                # 右面
                if j == n - 1: # 第一排
                    cnt += grid[i][j]
                elif grid[i][j] > grid[i][j+1]: # 后排
                    cnt += grid[i][j] - grid[i][j+1]
        return cnt
```

***
## 解法二（按立方体减）
思路：对每个坐标位置的立方体，减去6个面中被隐藏的部分。

1. 上下两个面的值，仅当立方体大于一个时，在中间的部分才被隐藏了
2. 第一排的立方体不会被隐藏，对于后排的立方体，隐藏的面等于相交的最小值
3. 相交的面会减少两个立方体的暴露，因此一个方向仅需要遍历1次

* 时间复杂度：O（M*N）
* 空间复杂度：O（1）

当然，解法一也可以按照当前的写法进行改造，相邻两个立方体高度差即为暴露的部分，即前后方向使用：cnt += abs(grid[i][j] - grid[i-1][j])代替解法一中分别对前面和后面的计算。

```python
# author： suoxd123@126.com
class Solution:
    def surfaceArea(self, grid: List[List[int]]) -> int:
        cnt, ov = 0, 0 # cnt：立方体总个数， ov：相交次数
        m,n = len(grid),len(grid[0])
        for i in range(0,m):
            for j in range(0,n):
                cnt += grid[i][j]
                # 上下面
                if grid[i][j] > 1:
                    ov += grid[i][j] - 1
                # 前后 方向
                if i > 0:# 后排
                    ov += min(grid[i][j], grid[i-1][j])
                # 左右 方向
                if j > 0: # 后排
                    ov += min(grid[i][j] , grid[i][j-1])
        return cnt * 6 - ov * 2
```
***
## 解法三（按坐标减）
思路：跟解法二类似，都是总体面积减去隐藏面积，不过是将坐标对应立方体看做一个整体单元，而非解法二中组成立方体的单个小立方体，当然也可以类推，基于解法一思路实现“按坐标加”。
1. 每个坐标位置如果没有任何遮挡，暴露面积为 $4h+2 = 6*h - 2 (h - 1)$，h为立方体高度，即grid[i][j]的值
2. 减去四周被遮挡的面积，每个面被遮挡的面积等于较小的值
3. 对于等于0的坐标，面积不存在，直接跳过
* 时间复杂度：O（M*N）
* 空间复杂度：O（1）
```python
# author： suoxd123@126.com
class Solution:
    def surfaceArea(self, grid: List[List[int]]) -> int:
        cnt, m,n = 0, len(grid),len(grid[0])        
        for i in range(0,m):
            for j in range(0,n):
                if grid[i][j] <= 0:#当前坐标不存在立方体
                    continue
                cnt += 4 * grid[i][j] + 2                
                if i > 0:# 前面
                    cnt -= min(grid[i][j], grid[i-1][j])
                if i < m - 1:# 后面
                    cnt -= min(grid[i][j], grid[i+1][j])
                if j > 0:# 左面
                    cnt -= min(grid[i][j], grid[i][j-1])
                if j < n - 1:# 右面
                    cnt -= min(grid[i][j], grid[i][j+1])
        return cnt
```
