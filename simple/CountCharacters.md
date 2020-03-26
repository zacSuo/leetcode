---
title: 3种解法： 查看拼写单词
categories:
  - 技术资料
  - 算法
tags:
  - hash
  - 单词拼写
  - 暴力法
  - python
date: 2020-03-16 19:34:29
updated: 2020-03-16 19:34:29
description: 使用（固定数组(hash字典)、暴力法、Python库函数）3种解法，实现查看单词的拼写...
---

## 题目


[NO. 1160](https://leetcode-cn.com/problems/find-words-that-can-be-formed-by-characters)

给你一份『词汇表』（字符串数组） words 和一张『字母表』（字符串） chars。

假如你可以用 chars 中的『字母』（字符）拼写出 words 中的某个『单词』（字符串），那么我们就认为你掌握了这个单词。

注意：每次拼写时，chars 中的每个字母都只能用一次。
每个字典中的每个字符
返回词汇表 words 中你掌握的所有单词的 长度之和。

 

示例 1：

输入：words = ["cat","bt","hat","tree"], chars = "atach"
输出：6
解释： 
可以形成字符串 "cat" 和 "hat"，所以答案是 3 + 3 = 6。
示例 2：

输入：words = ["hello","world","leetcode"], chars = "welldonehoneyr"
输出：10
解释：
可以形成字符串 "hello" 和 "world"，所以答案是 5 + 5 = 10。
 

提示：

1 <= words.length <= 1000
1 <= words[i].length, chars.length <= 100
所有字符串中都仅包含小写英文字母


***
## 解法一（固定数组）
思路：使用长度为26的数组记录各字符数量，依次对字符进行对比，使用26的前提是字符串仅包含26个英文字符，如果有其它字符，需要扩大长度，比如256（全部ascii）。相同的思路也可以使用HashTable（字典）的方式实现，即使用字符作为Key，字符个数作为Value，只是在对比前使用ContainsKey进行预判断。
1. 通过字符减去'a'表示字符的索引，首先获取字典中每个字符个数
2. 依次统计每个待检查字符串每个字符的个数
3. 对每个字符个数进行验证，满足条件的字符串进行长度累计

* 时间复杂度：O(n)
* 空间复杂度：O(1)

```csharp
public class Solution {
// author: suoxd123@126.com
    public void CounterString(string str, int[] arr)
    {//统计字符串中每个字符的个数
        Array.Clear(arr,0,arr.Length);
        foreach(char item in str)
        {
            arr[item - 'a'] ++;
        }
    }
    public int CountCharacters(string[] words, string chars) {
        int result = 0;
        int[] arrChar = new int[26], arrWord = new int[26];
        CounterString(chars,arrChar);
        foreach(string word in words)
        {
            bool flag = true;
            CounterString(word,arrWord);
            for(int i=0;i<26;i++)
            {//对每个字符进行对比
                if(arrWord[i] > arrChar[i])
                {
                    flag = false;
                    break;
                }
            }
            if(flag)
            {//如果全部字符满足条件，则累加
                result += word.Length;
            }
        }
        return result;
    }
}
```

***
## 解法二（Pythonic)
思路：原理跟上面解法一一样，不过利用python的库函数Counter，来对原字符串进行统计：
1. 获取字典中各字符个数
2. 依次获取各单词中字符个数
3. 对所有单词中所有字符个数不大于字典中对应个数时，累加单词长度

```python
class Solution:
#   author: suoxd123@126.com
    def countCharacters(self, words: List[str], chars: str) -> int:
        dc = collections.Counter(chars)
        cnt = 0
        for w in words:
            dw = collections.Counter(w)
            cnt += len(w) if all([v<=dc[k] for k,v in dw.items()]) else 0
        return cnt
```


***
## 解法三（暴力法）
思路：暴力法就是最直接的思路，直接使用words去跟字典中元素进行对比，每次对比完清除对比字符。
1. 将每个单词转换为字符数组，用于单个字符进行对比
2. 将字典转换为字符数组，如果内存吃紧或字典很大，也可以在第一次获取后之后使用时使用浅拷贝，来节约临时变量申请。
3. 依次对单词进行对比，如果满足要求，则进行累计计数
* 时间复杂度：O(n)
* 空间复杂度：O(n)
```csharp
public class Solution {
// author: suoxd123@126.com
    public int CountCharacters(string[] words, string chars) {
        int result = 0;
        foreach(string word in words)
        {
            char[] arrWord = word.ToCharArray();
            char[] arrChar = chars.ToCharArray();
            bool flag = true;
            foreach(char item in arrWord)
            {
                int idx = Array.IndexOf(arrChar,item);
                if(idx < 0)
                {//不存在时候退出
                    flag = false;
                    break;
                }
                arrChar[idx] = '#';//清除原字符
            }
            if(flag)
            {//如果全部字符满足条件，则累加
                result += word.Length;
            }
        }
        return result;
    }
}
```
