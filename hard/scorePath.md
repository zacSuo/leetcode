---
title: 2个解法：最大得分的路径数目
categories:
  - 技术资料
  - 算法
tags:
  - 算法
  - 递归
  - 循环
  - 非递归
  - 数组
  - 泛型集合
date: 2019-12-29 19:34:29
updated: 2019-12-29 19:34:29
description: 通过循环和递归两种方式实现算法，用于计算通过一个带障碍的二维数组后，最大得分路径和路径条数...
---

# 题目

[NO. 1301](https://leetcode-cn.com/problems/number-of-paths-with-max-score/)

给你一个正方形字符数组 board ，你从数组最右下方的字符 'S' 出发。

你的目标是到达数组最左上角的字符 'E' ，数组剩余的部分为数字字符 1, 2, ..., 9 或者障碍 'X'。在每一步移动中，你可以向上、向左或者左上方移动，可以移动的前提是到达的格子没有障碍。

一条路径的 「得分」 定义为：路径上所有数字的和。

请你返回一个列表，包含两个整数：第一个整数是 「得分」 的最大值，第二个整数是得到最大得分的方案数，请把结果对 10^9 + 7 取余。

如果没有任何路径可以到达终点，请返回 [0, 0] 。

 

示例 1：

输入：board = ["E23","2X2","12S"]
输出：[7,1]
示例 2：

输入：board = ["E12","1X1","21S"]
输出：[4,2]
示例 3：

输入：board = ["E11","XXX","11S"]
输出：[0,0]
 

提示：

2 <= board.length == board[i].length <= 100



# 解法一（循环）
解法思路：直接使用循环，因为每个节点依赖于上一个节点，因此从最右下角的节点分别沿着横纵两个方向，一步一步移动到数组开始的左上角。

本算法使用三维数组，在第三维保存对应索引位置的初始值、得分、路径数量和是否可达四个信息，实现算法如下（比赛之后实现的）：

时间复杂度为O(n^2)，空间复杂度O(n^2)（由于使用三维数组存储）

```csharp
public class Solution {
    long [,,] intBoard;
    public int[] PathsWithMaxScore(IList<string> board) {
        //1. 链表转存到数组中
        int row=board.Count,column=board[0].Length;
        intBoard = new long [row,column,4];
        for(int i=0;i<row;i++)
        {
            for(int j=0;j<column;j++)
            {
                if(board[i][j] >= '0' && board[i][j] <= '9')
                {
                    intBoard[i,j,0] = Convert.ToInt64(board[i][j] - '0');
                }
                else if(board[i][j] == 'E' || board[i][j] == 'S')
                {
                    intBoard[i,j,0] = 0;
                }
                else
                {
                    intBoard[i,j,0] = -1;
                }
                intBoard[i,j,1] = 0;
                intBoard[i,j,2] = 0;
            }
        }

        intBoard[row-1,column-1,2] = 1;//第一条路径
        intBoard[row-1,column-1,3] = 1;//起点可达
        for(int i=row-1;i>=0;i--)
        {
            for(int j=column-1;j>=0;j--)
            {
                if(intBoard[i,j,0] == -1) continue;//不能访问
                if(intBoard[i,j,3] == 0)  continue;//不可达
                if(i > 0)
                {
                    updateData(i-1,j,intBoard[i,j,1],intBoard[i,j,2]);
                }
                if(j > 0)
                {
                    updateData(i,j-1,intBoard[i,j,1],intBoard[i,j,2]);
                }
                if(i > 0&&j > 0)
                {
                    updateData(i-1,j-1,intBoard[i,j,1],intBoard[i,j,2]);
                }
            }
        }
        return new int[]{(int)intBoard[0,0,1],(int)intBoard[0,0,2]};
    }


    public void updateData(int r,int c,long lastMaxVal,long lastMaxCount)
    {
        
        if(intBoard[r,c,3] == 0 || intBoard[r,c,1] < intBoard[r,c,0] + lastMaxVal)
        {
            intBoard[r,c,1] = intBoard[r,c,0] + lastMaxVal;        
            intBoard[r,c,2] = lastMaxCount;
            intBoard[r,c,3] = 1;
        }
        else if(intBoard[r,c,1] == intBoard[r,c,0] + lastMaxVal)
        {
            intBoard[r,c,1] = intBoard[r,c,0] + lastMaxVal; 
            intBoard[r,c,2] += lastMaxCount;
        }
        if(intBoard[r,c,2] >= 1000000007)
        {
            intBoard[r,c,2] %= 1000000007;
        }
    }
}
```
# 解法二（递归）
解法思路：由于满足递归三要素：1.任务可以分解（可以向左上遍历），2.分解后和原任务实现原理一样（都是向左上遍历），3.结束条件明确（到[0,0]位置），因此选择通过递归实现。

1. 为了增加访问效率，最开始申请一个数组，代替使用链表索引的方式访问
2. 通过递归将所有通路的得分，存储到一个新的结果链表中
3. 对结果链表中的得分进行统计，最终输出结果

问题：方案本身可行，但时间复杂度有点高，有优化的空间，在参加比赛过程中，通过了一多半的测试用例，最终卡在了一个测试用例上，因为超时了，超时的测试用例及实现代码如下：

> ["E4789338X943596124X2676X552X587877X456943458X29735","611684759486631913932337237231351921X2152919376427","4499519117827344997451XX34X46693XX7181343557483669","414951X685152X89829782685X4912581351X3216914721551","X387271851925X3629265X99195X5897581179XX369637813X","1X8X2682518937289551X98X7983XX34993116413343558825","X92X12119593186X675113X682143777XX8981619298251984","X671798198463X5314971262X9392393XXX544537813812728","81856146535454X3678775784456289257XX8221X2488XXX68","77X3592XX94844399282X2X6336122XX7X18244862821X26XX","28885X948512X3585X27824186222X73X9X56441X9X4689517","344X495X682875968X82X9877379XX386748175X6293X44159","1924352186149295919715X27X555626X17798524189528625","3435681879X49727366745492X648X5952772978787143263X","412933788234154913356X2X9X144818X21XX5629259785133","489644765X456XX44XX2X5387637879X662941398337817381","7617826679176XX173173537173164967296764519X3427693","7X69X5466277665871135253486758156766536X5436X16728","318152574426X696X18X833396113X31862234511611X89691","X147492241256344555237X94772X9X5136226469551942X29","X29846X49419853778154XX636X35XX5232391787617416258","8885851X996538X163323347235993741926591X72X1761X14","226X8136988863232963682217521777419144333838517835","3197757518X241117949451X423XX55861XX6161938551X752","49X149355329X617X51X21965452X962X42762X25968X73754","X1176483834957794897816132X5X942366794665183797399","48294674492176X1X663644X58X17729X4482638X92X482422","51X16X157889846864X1436338776687677X44895154219626","782XX82XX432848434721692X55564975938X2649681569663","26792517214X8863X9896XX64619817916123168945761X526","X7XX319393797X184679X421943743279X1486126364341595","11425763X68563511X2496983325426X151456X5X459542989","89872654932254X5525587692326476X65562X59X635129314","6X944371656471737X9673645X4145X821X942995885X86522","64XX328X8243XX3445X6412955X87X42355234X73223243421","944391299X158X962X9X259552884441434XX9349X5XX79855","98422711429356771336494176X56X2584376X2354X72X5416","7135X7X9X3X699929714X61664916968X1896X5X1985386642","22969X77414X2154167176388X3313X918X1558161XX413862","4X141958614412616921588565488847635X837996835937X6","X416X64649X955369739187781488XX77129X6966899351X74","9X2349175X345X7469265842X591X4748167996963X63X7211","3117349374592412365636726147X52469X8921X1888159627","254513X73629359514989286822X7X89391797X357546X9145","8882877128738295914941X56995X243888XX595873XX99217","X947591X382X19613453263544415821689719794546655278","51X4565271954965486X492693X731844471486X149X7166X5","731X1816268181645946X2123376981X13X834338226X28X36","6X72X3599546159X378191718821X746789239488712584143","5982153336X61729X66339596838X77751396645929982913S"]

```csharp
public class Solution {
    List<int> scoreList;
    int idx =0;
    public int[] PathsWithMaxScore(IList<string> board) {
        scoreList = new List<int>();
        //1. 链表转存到数组中
        int row=board.Count,column=board[0].Length;
        int [,] intBoard = new int [row,column];
        for(int i=0;i<row;i++)
        {
            for(int j=0;j<column;j++)
            {
                intBoard[i,j] = Convert.ToInt32(board[i][j] - '0');
            }
        }
        getPaths(intBoard, row- 1, column- 1,0);
        if(scoreList.Count == 0)
            return new int []{0,0};
        // 3. 对结果进行统计
        int maxScore = scoreList[0];
        int count = 0;
        for(int i=1;i<scoreList.Count;i++)
        {
            if(scoreList[i] > maxScore)
            {
                maxScore = scoreList[i];
            }
        }
        for(int i=0;i<scoreList.Count;i++)
        {
            if(scoreList[i] == maxScore)
            {
               count++;
            }
        }
        return new int []{maxScore,count};
    }
    
    public void getPaths(int [,] board,int row,int colum,int count)
    {
        if(row == 0 && colum == 0)
        {
            scoreList.Add(count);
            
            return;
        }
        if(board[row,colum] == 40)//遇到X即退出
            return;
        if(board[row,colum] != 35){//不是起点S则累计
            count += board[row,colum];
        }
        if(row > 0)
        {//向左递归
            getPaths(board,row-1,colum,count);
        }
        if(colum > 0)
        {//向上递归
            getPaths(board,row,colum - 1,count);
        }
        if(row >0 && colum > 0)
        {//向左上递归
            getPaths(board,row-1,colum - 1,count);
        }
    }
}
```