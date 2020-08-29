---
title: LeetCode-51-52 经典回溯问题
body:
  - article
  - comments
date: 2020-02-08 14:51:13
tags: LeetCode
categories: 刷题
---

##### 一、思路
N皇后问题可以说是最经典的回溯问题，经典到刚接触C语言的时候，就有讲到这个问题。
现在重新看这个问题，就会比较熟练：问题是在**模拟摆放皇后**，使他们满足皇后之间不可互相攻击这个条件。

> 国际象棋中，皇后可以横、直、斜着走，步数不受限制，但不能越子。
>如对国际象棋感兴趣可以自己在 [https://lichess.org/](https://lichess.org/) 这个网站上自己玩。

如果将棋盘分成**行、列、斜三种情况（斜这种情况其实还包含了45°和-45°两种）**，那么就需要我们对已经存在皇后的行、列、斜进行标记，标记的方法再好不过就是对他们编号，然后某一个位置有皇后之后，就将其对应行、列、斜编号的位置置为1（表示这些行、列、斜已经放置过皇后，不应该再放），用于之后新皇后是否能放在某个位置的判断。
行列的编号很简单，重点是在斜的情况编号，我使用的是找到他们行列号与斜编号的关系。易知，一个方向的斜应该有2*N-1个，我是假想了一种映射，将同一个斜上的所有皇后，都映射到行号为0或者是列号为零的行、列上。图示如下： ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200208145343679.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNTc1OTI2,size_16,color_FFFFFF,t_70)
下面就是找规律的游戏，最终我得到的斜编号与行列编号之间的关系是：
**45°斜编号 = c+r;	-45°斜编号 = n+r-c;（r为行编号，c为列编号，n为棋盘大小）**

##### 二、代码
```cpp
// 51题代码
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        vector< vector<string> > ans;
        vector<bool> flagR(n , 0);
        vector<bool> flagX1(2*n , 0);
        vector<bool> flagX2(2*n , 0);
        vector<string> tem(n , string(n , '.'));
    
        helper(ans , tem , 0 , n , flagR , flagX1 , flagX2);

        return ans;
    }

    void helper(vector<vector<string>> &ans , vector<string> tem , 
                int c , int n , vector<bool> &flagR , 
                vector<bool> &flagX1 , vector<bool> &flagX2)
    {
        if (c == n)  // 递归终点，所有列都遍历完了
        {
            ans.push_back(tem);
            return;
        }
        for (int r = 0 ; r < n ; ++r)  // 遍历行号
        {
            int x1 = c+r;
            int x2 = n+r-c;
            if (flagR[r] == 0 && flagX1[x1] == 0 && flagX2[x2] == 0)
            {
                flagR[r] = flagX1[x1] = flagX2[x2] = 1;
                tem[r][c] = 'Q';
                helper(ans , tem , c+1 , n , flagR , flagX1 , flagX2);
                flagR[r] = flagX1[x1] = flagX2[x2] = 0;
                tem[r][c] = '.';
            }
        }
    }
};
```

```cpp
// 52题代码
class Solution {
public:
    int totalNQueens(int n) {
        int ans = 0;
        vector<bool> flagR(n , 0);
        vector<bool> flagX1(2*n , 0);
        vector<bool> flagX2(2*n , 0);
    
        helper(ans , 0 , n , flagR , flagX1 , flagX2);

        return ans;
    }

    void helper(int &ans , int c , int n , vector<bool> &flagR , 
                vector<bool> &flagX1 , vector<bool> &flagX2)
    {
        if (c == n)  // 递归终点，所有列都遍历完了
        {
            ans++;
            return;
        }
        for (int r = 0 ; r < n ; ++r)  // 遍历行号
        {
            int x1 = c+r;
            int x2 = n+r-c;
            if (flagR[r] == 0 && flagX1[x1] == 0 && flagX2[x2] == 0)
            {
                flagR[r] = flagX1[x1] = flagX2[x2] = 1;
                helper(ans , c+1 , n , flagR , flagX1 , flagX2);
                flagR[r] = flagX1[x1] = flagX2[x2] = 0;
            }
        }
    }
};
```
