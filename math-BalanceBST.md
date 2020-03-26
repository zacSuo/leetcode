---
title: 2种解法：将二叉搜索树变平衡
categories:
  - 技术资料
  - 算法
tags:
  - 二叉树
  - 平衡二叉树
  - 搜索二叉树
  - 二分法
  - 中序遍历
date: 2020-03-14 19:34:29
updated: 2020-03-14 19:34:29
description: 基于二叉树搜索中的中序排序、二分法查找两种方法实现将二叉搜索树变平衡...
---

@[toc]
***
## 题目
给你一棵二叉搜索树，请你返回一棵 平衡后 的二叉搜索树，新生成的树应该与原来的树有着相同的节点值。

如果一棵二叉搜索树中，每个节点的两棵子树高度差不超过 1 ，我们就称这棵二叉搜索树是 平衡的 。

如果有多种构造方法，请你返回任意一种。

示例：
<center><img src="/img/tech/20200315155222176.png" title="初始结构"/><img src="/img/tech/20200315155235924.png" title="最终结构"/></center>


输入：root = [1,null,2,null,3,null,4,null,null]
输出：[2,1,3,null,null,null,4]
解释：这不是唯一的正确答案，[3,1,4,null,2,null,null] 也是一个可行的构造方案。
***

## 调试工具
为了方便代码调试，写了2个调试工具，分别为：
1. 将输入的数组转为树结构（用于直接使用数组作为输入）
2. 将一个树结构转换为字符串（用于打印查看树结构）

```csharp
//调用方式
//TreeNode tree = null;
//GenerateTreeByOrder(new int?[]{1,null,15,14,17,7,null,null,null,2,12,null,3,9,null,null,null,null,11},out tree);
static void GenerateTreeByOrder(int?[] arr, out TreeNode tree)
{//基于顺序访问，使用数组创建二叉树
    int count = arr.Length;
    Queue<TreeNode> queue = new Queue<TreeNode>();
    tree = new TreeNode(arr[0].Value);
    queue.Enqueue(tree);
    
    int idx = 1;            
    while(queue.Count > 0 && idx < count)
    {
        TreeNode tmpTree = queue.Dequeue();
        if(!(arr[idx] is null))
        {//第一个是左子树
            tmpTree.left = new TreeNode(arr[idx].Value);
            queue.Enqueue(tmpTree.left);
        }
        if(!(arr[idx + 1] is null))
        {//第二个是右子树
            tmpTree.right = new TreeNode(arr[idx + 1].Value);
            queue.Enqueue(tmpTree.right);
        }
        idx += 2;
    }
}
//调用方式
//string str = PrintTreeByOrder(tree);
//Console.WriteLine(str);
static string PrintTreeByOrder(TreeNode tree)
{//基于顺序访问，将树结构转为字符串
    StringBuilder sb = new StringBuilder(tree.val.ToString ());
    Queue<TreeNode> queue = new Queue<TreeNode>();
    queue.Enqueue(tree);
    while(queue.Count > 0)
    {
        TreeNode tmpTree = queue.Dequeue();
        sb.Append(',');
        if(tmpTree.left is null)
        {//遍历左子树
            sb.Append("null");
        }
        else
        {
            sb.Append(tmpTree.left.val.ToString ());
            queue.Enqueue(tmpTree.left);
        }

        sb.Append(',');
        if(tmpTree.right is null)
        {//遍历右子树
            sb.Append("null");
        }
        else
        {
            sb.Append(tmpTree.right.val.ToString ());
            queue.Enqueue(tmpTree.right);
        }
    }

    //删除末尾null
    string sourceStr = sb.ToString ();
    Regex regex = new Regex("(,null)+$");
    if(regex.IsMatch(sourceStr))
    {
        sourceStr = regex.Replace(sourceStr,"");
    }
   
    return sourceStr;
}
```
***

## 解法一（二分法构建平衡树）
思路：
首先将原搜索树整理为一个有序数组，然后基于有序数组通过二分法的思想，将中间元素作为树头，依次递归左右数组直至遍历完，构建平衡二叉树。
1. 遍历原始树，生成一个有序数组，由于原始结构有序，所以生成的数组本身有序，要先遍历右子树，是为了避免左子树改变索引值，也可以使用中序遍历。
2. 基于有序数组，通过递归，实现平衡二叉树
* 时间复杂度：O(n)
* 空间复杂度：O(n)

```csharp
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     public int val;
 *     public TreeNode left;
 *     public TreeNode right;
 *     public TreeNode(int x) { val = x; }
 * }
 */


public class Solution {
    public void GetValues(TreeNode root, List<int> list, int currentIdx)
    {//基于搜索树，生成有序列表
        list.Insert(currentIdx,root.val);        
        if(root.right != null)
        {
            GetValues(root.right, list, currentIdx + 1);
        }
        if(root.left != null)
        {
            GetValues(root.left, list, currentIdx);
        }        
    }
    
    public void CreateTree(TreeNode root, List<int> list, int left, int right,bool isLeft)
    {//基于有序列表，构建平衡树
        int mid = (left + right)/2;
        if(list[mid] == -1) return;
        
        TreeNode t = new TreeNode(list[mid]);
        list[mid] = -1;//防止重复访问
        if(isLeft == true)
        {
            root.left = t;
        }
        else
        {
            root.right = t;
        }
        
        if(left < mid)
        {
            CreateTree(t,list,left,mid,true);
        }
        else
        {
            t.left = null;
        }
        if(right > mid)
        {
            CreateTree(t,list,mid,right,false);
        }
        else
        {
            t.right = null;
        }
    }
    
    public TreeNode BalanceBST(TreeNode root) {
        List<int> r = new List<int>();
        GetValues(root,r,0);
        
        int count = r.Count();
        int mid = count/2;
        TreeNode result = new TreeNode(r[mid]);
        r[mid] = -1;
        CreateTree(result, r, 0, mid, true);
        CreateTree(result, r, mid, count,false);
        
        return result;
    }
}
```
***
## 解法二（中序遍历解析树结构）
思路：核心原理跟解法一类似，使用经典的中序遍历代替插入模式，依次将数据存储为有序数组
1. 使用中序遍历读取原始树的数据
2. 直接使用递归遍历有序数组
* 时间和空间复杂度跟解法一完全一致，仅实现方式上更简洁，两种解法的结果不一定完全一样，由CreateTree中左右位置索引来决定，如果对应一致则答案也会一致。
* 使用测试用例：[1,null,15,14,17,7,null,null,null,2,12,null,3,9,null,null,null,null,11]
* 解法一输出：11,3,14,2,7,12,15,1,null,null,9,null,null,null,17
* 解法二输出：9,2,14,1,3,11,15,null,null,null,7,null,12,null,17

```csharp
public void GetValues(TreeNode root, List<int> list)
{//中序遍历
    if(root == null) return;
    GetValues(root.left,list);
    list.Add(root.val);
    GetValues(root.right,list);            
}

public TreeNode CreateTree(List<int> list, int left, int right)
{//直接构建
    if(left > right) return null;
    int mid = (left + right)/2;
    
    TreeNode t = new TreeNode(list[mid]);
    t.left = CreateTree(list,left,mid-1);
    t.right = CreateTree(list,mid+1,right);

    return t;
}

public TreeNode BalanceBST(TreeNode root) {
    List<int> r = new List<int>();
    GetValues(root,r);
    
    int count = r.Count;
    TreeNode result = CreateTree( r, 0, count - 1);
    
    return result;
}
```
## 解法三（二叉树旋转）
思路：直接基于原始树进行左右旋转，思路类似建堆过程，不过，目前还没写出来，之后如果有机会完善，到时候再更新为3种解法