---
title: 3种解法：利用历史数据实现幂函数
categories:
  - 技术资料
  - 算法
tags:
  - 幂函数
  - 历史数据
  - 递归
date: 2020-01-10 16:34:29
updated: 2020-01-10 16:34:29
description: 使用3种算法，实现利用历史数据计算幂函数，而不是暴力循环...
---

## 解法一

思路：直接暴力法x求积，n很大时肯定会超时，那么使用多重幂次进行减少运算，即，使用上一次的幂次运算的结果，执行步骤和代码如下：（实现思路跟【[2种解法：实现两数相除](https://www.zhenxiangsimple.com/2020/01/05/tech/math-Divide/)】完全一样）

1. 将幂次分解：$n = 2^a + 2^b + 2^c +...+ 1$

2. 幂次递归：$(x)^n = (x)^{ 2^a + 2^b + 2^c +...+ 1} = (x)^{ 2^a} * (x)^{ 2^b} * (x)^{ 2^c} * ... * x$

3. 单次递归的循环：$(x)^{ 2^a} = ((((((((x)^2)^2)^2)^2)^2)^2)...)^2$，共嵌套a层


```csharp
public class Solution {
    public double search(double x,int n)
    {
        if(n == 1) return x;
        if(n == 0) return 1;
        int t = 1;
        double r = x;
        while(n - t >= t)
        {//使用幂次运算
            t <<= 1;
            r *= r;
        }
        return r * search(x,n - t);
    }
    public double MyPow(double x, int n) {
        double nx = 1;
        if(n == int.MinValue)
        {//防止负值转化为正值时越界
            n = n+1;
            nx = x;
        }
        int  flag = n > 0?1:-1;
        int un = flag * n;
        double r = search(x,un) * nx;
        if(flag < 0)
        {//负值时候求导
            r = 1/r;
        }
        return r;
    }
}
```
<center><img src="/img/tech/20200110143130821.png" title="执行结果"/></center>

## 解法二

思路：基于解法一的优化，优化有下面3点，代码如下：

1. 不关心n的正负，只是用其值本身，使用0来判断结束条件
2. 利用每次计算的幂次结果，而不用每次在递归中从头开始计算
3. 使用最低位跟1的与运算，来代替求余运算，减少负数求余后再求绝对值



```csharp
public class Solution {
    public double MyPow(double x, int n) {
        bool  flag = n > 0?true:false;
        double r = 1;
        while(n != 0)
        {
            if((n & 1) == 1)
            {//最后一次(n==1)一定会进来
                r *= x;
            }
            x *= x;
            n /= 2;
        }
        return flag?r:(1/r);
    }
}
```

<center><img src="/img/tech/20200110145107484.png" title="执行结果"/></center>

## 解法三

思路：基于解法二，使用递归代替循环来实现，看起来更简洁，代码如下：

```csharp
public class Solution {
    public double MyPow(double x, int n) {
        if(n == 0) return 1;
        if(n == 1) return x;
        if(n == -1) return 1/x;

        double r = MyPow(x,n/2);
        double t = MyPow(x,n%2);

        return  r * r *t;
    }
}
```
<center><img src="/img/tech/20200110153415204.png" title="执行结果"/></center>