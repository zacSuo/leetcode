---
title: 3种解法 ： 计算两水壶拼水问题
categories:
  - 技术资料
  - 算法
tags:
  - 暴力法
  - 深度优先搜索
  - 广度优先搜索
  - 直线方程
date: 2020-03-21 19:30:29
updated: 2020-03-21 19:30:29
description: 通过（暴力法、深度优先搜索、直线方程）3种方法，判断两水壶拼凑水量的问题...
---

## 题目

[NO. 365](https://leetcode-cn.com/problems/water-and-jug-problem)

有两个容量分别为 x升 和 y升 的水壶以及无限多的水。请判断能否通过使用这两个水壶，从而可以得到恰好 z升 的水？

如果可以，最后请用以上水壶中的一或两个来盛放取得的 z升 水。

你允许：

 - 装满任意一个水壶 
 - 清空任意一个水壶 
 - 从一个水壶向另外一个水壶倒水，直到装满或者倒空

示例 1: (From the famous "Die Hard" example)
> 输入: x = 3, y = 5, z = 4 
> 输出: True

示例 2:
> 输入: x = 2, y = 6, z = 5 
> 输出: False

***
## 解法一（暴力法）
思路：目标容量是通过当前两个瓶子相互减，通过递归相减遍历出所有可能组合的，没去探寻规律，因此称为暴力法，不过其搜索的方式也可以称为深度优先搜索。如果递归找到返回True，如果遍历所有可能后没找到，则返回False
1. 如果目标大于两个瓶子容量和或目标为0，特殊处理
2. 如果目标大于两个瓶子容量，则减去大容量作为新目标容量，即原目标容量是新目标容量加上大容量
3. 递归里面不断通过x和y对当前差值进行处理

* 时间复杂度：O(x*y)
* 空间复杂度：O(x*y)

递归的写法，在测试用例数值比较大的时候会超出递归深度，因此最前面两行是修改了Python默认的递归深度。（如果不修改深度，超出深度的测试用例为：104597，104623，123）。
```python
# author: suoxd123@126.com
import sys 
sys.setrecursionlimit(1000000) 
class Solution:
    def deIn(self,s,x,y,z,t):
        if z == t:# 找到
            return True
        if s.__contains__(t):# 已经出现过，则不再递归
            return False
        s.add(t)
        return self.deIn(s,x,y,z,abs(x-t)) or self.deIn(s,x,y,z,abs(y-t))

    def canMeasureWater(self, x: int, y: int, z: int) -> bool:
        if z > x + y:
            return False
        if z in (0,x,y):#简单特例
            return True
        if z > max(x,y):
            z = z - max(x,y)
        s = set()
        return self.deIn(s,x,y,z,max(x,y)-min(x,y))
```
***

## 解法二（直线方程）
思路：由于每次倒水操作都是全部倒掉或者倒满，都可以看做是对x，y的整数次组合操作，即使用一个二元一次方程进行求解判断，将题目抽象为数学问题，可以理解为：
1. 已知x，y，z三个正整数
2. 判断是否存在两个整数a，b，使得线性方程可以满足：$ax + by = z$，即在以ab为变量构成的二维坐标系中，直线方程是否经过整数坐标
3. a，b的约束条件为：不能同时大于等于1（同时等于1除外），即要检查的坐标点不考虑第一象限中（1,b)（a,1)的那一部分

在数论中有个**贝祖定理**，是这么说的：

1. 若x，y是整数，且x和y的最大公约数为k，一定存在整数a和b，使得$ax+by=k$成立
2. 进一步，等式$ax+by=z$有解的充要条件是：z是k的整数倍
3. 另外，等式有解时必然有无穷多个整数解

从贝祖定理知道，本题目可以转换为判断，z是否能够整除x和y的最大公约数。

1. ab的约束条件首先剔除（思路中的第3点）
2. 辗转相除得到最大公约数
3. 判断是否能整除最大公约数

```python
class Solution:
# author: suoxd123@126.com
    def canMeasureWater(self, x: int, y: int, z: int) -> bool:
        if z > x + y:#剔除约束条件
            return False
        if z in (0,x,y):#简单特例
            return True
        x, y = max(x,y), min(x,y)
        while y > 0:#辗转相除 得到 最大公约数
            y, x = x % y, y        
        return z % x == 0
```
***
## 解法三（深度优先搜索）
思路：每次把每种可能的操作都执行一遍，如果满足条件则返回True，否则每次把操作结果存入栈，每次取栈顶元素后持续查找，直到将每次操作的可能性都遍历完，本算法使用栈来达到深度优先搜索算法，也可以使用队列来达到广度优先搜索算法。

每次操作最多有以下6种操作方式：
1. 把X倒满
2. 把Y倒满
3. 把X倒空
4. 把Y倒空
5. 把X倒入Y，可能把X倒空，也可能把Y倒满
6. 把Y倒入X，可能把Y倒空，也可能把X倒满

为了减少重复遍历，引入visited集合进行过滤来判断，这种方式跟解法一类似，也可以理解成暴力法，即基于具体规则的暴力破解。

本解法使用循环来代替递归，用于避免超出Python的默认最大递归深度，当然也可以类似解法一使用递归的方式实现，其时间和空间复杂度跟解法一一样。。
```python
class Solution:
# author: suoxd123@126.com
    def canMeasureWater(self, x: int, y: int, z: int) -> bool:
        stack = [(0,0)]
        visited = set()
        while stack:
            cx,cy = stack.pop()
            if z in (cx,cy,cx+cy):
                return True
            if (cx,cy) in visited:
                continue
            visited.add((cx,cy)) #加入标签减少重复遍历
            stack.append((x,cy))#1. 把X倒满
            stack.append((cx,y))#2. 把Y倒满
            stack.append((0,cy))#3. 把X倒空
            stack.append((cx,0))#4. 把Y倒空
            stack.append((cx-min(cx,y-cy),cy+min(y-cy,cx)))#5. 把X倒入Y，可能把X倒空，也可能把Y倒满
            stack.append((cx+min(x-cx,cy),cy-min(cy,x-cx)))#6. 把Y倒入X，可能把Y倒空，也可能把X倒满
        return False

```
