---
title: 3种解法 ：定位单链表的中间节点
categories:
  - 技术资料
  - 算法
tags:
  - 链表
  - 数组
  - 快慢指针
  - 单链表
date: 2020-03-23 19:30:29
updated: 2020-03-23 19:30:29
description: 通过（链表、数组、快慢指针）3种方法，查找单链表的中间节点...
---

## 题目

[NO. 876](https://leetcode-cn.com/problems/middle-of-the-linked-list)

给定一个带有头结点 head 的非空单链表，返回链表的中间结点。

如果有两个中间结点，则返回第二个中间结点。

 

示例 1：

> 输入：[1,2,3,4,5]
 输出：此列表中的结点 3 (序列化形式：[3,4,5])
返回的结点值为 3 。 (测评系统对该结点序列化表述是 [3,4,5])。
注意，我们返回了一个 ListNode 类型的对象 ans，这样：
ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.

示例 2：

> 输入：[1,2,3,4,5,6]
输出：此列表中的结点 4 (序列化形式：[4,5,6])
由于该列表有两个中间结点，值分别为 3 和 4，我们返回第二个结点。
 

提示：

> 给定链表的结点数介于 1 和 100 之间。

***
## 解法一（单链表法）
思路：基于单链表进行查找，先统计总个数，然后从头开始移动次数为个数的一半，即为中间位置
1. 从头移动到尾，统计链表中节点个数
2. 计算总个数的一半，即为链表长度的一半
3. 移动一半长度

* 时间复杂度：O(n)
* 空间复杂度：O(1)

```python
# author: suoxd123@126.com
class Solution:
    def middleNode(self, head: ListNode) -> ListNode:
        cnt, node = 0, head
        while node != None:#统计总数
            node = node.next
            cnt += 1
        cnt = int(cnt/2)#计算需要移动的次数
        print(cnt)
        node = head
        for i in range(0,cnt):#实际移动
            node = node.next
        return node
```
***
## 解法二（数组）
思路：基本原理跟解法一一样，需要先统计总个数，不过当前解法使用空间换时间，使用数组将所有节点存储起来，然后通过下标访问中间节点，而不用再执行第二次的查询。
 1. 统计链表中节点个数，并将每个访问到的节点存入数组
 2. 计算链表长度的一半，即为链表长度的一半
3. 直接通过数组返回长度一般对应的索引

* 时间复杂度：O(n)
* 空间复杂度：O(n)
```python
# author: suoxd123@126.com
class Solution:
    def middleNode(self, head: ListNode) -> ListNode:
        cnt, node,tmpList = 0, head, []
        while node != None:#统计总数
            tmpList.append(node)
            node = node.next
            cnt += 1            
        cnt = int(cnt/2)#计算需要移动的次数
        return tmpList[cnt] if len(tmpList)>0 else None
```
***
## 解法三（快慢指针）
思路：使用快慢两个指针，慢指针每走一步，快指针走两步，直到快指针走到链表尾部
1. 快慢指针指向单链表头
2. 开始同时向后移动

* 时间复杂度：O(n)
* 空间复杂度：O(1)

```python
# author: suoxd123@126.com
class Solution:
    def middleNode(self, head: ListNode) -> ListNode:
        slow, fast = head, head
        while fast != None and fast.next != None:
            fast = fast.next.next
            slow = slow.next
        return slow
```
