---
title: LeetCode-97 指数型增长的暴力果然不可行
body:
  - article
  - comments
date: 2020-01-28 10:41:56
tags: LeetCode
categories: 刷题
---
#### 题目分析
这个题要求判断两个字符串s1、s2，是否能通过某种方式，将他们的**字符任意排列**，从而组成字符串s3，也就是题目所说的s1、s2交错组成s3。
#### 思路分析
##### 1、暴力法
首先想到的是简单的暴力法，对问题进行递归。按字符顺序递归。假设某一层递归s1，s2，s3分别到达下标p1，p2，p3，问题变成分别尝试s3[p3]取s1[p1]和s2[p2]（取的规则是字符要求相同）并继续递归。

```cpp
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        if (s1.size() + s2.size() != s3.size())
            return false;
        return helper(s1 , 0 , s2 , 0 , s3 , 0);
    }

    bool helper(string s1 , int p1 , string s2 , int p2 , string s3 , int p3)
    {
        if (p3 == s3.size())
            return true;
        bool flag1 , flag2;
        flag1 = flag2 = 0;
        // 用s1[p1]作为s3[p3]
        if (p1 < s1.size() && s1[p1] == s3[p3])
            flag1 = helper(s1 , p1+1 , s2 , p2 , s3 , p3+1);
        // 用s2[p2]作为s3[p3]
        if (p2 < s2.size() && s2[p2] == s3[p3])
            flag2 = helper(s1 , p1 , s2 , p2+1 , s3 , p3+1);
        if (flag1 == true || flag2 == true)
            return true;
        else
            return false;
    }
};
```

时间复杂度：**o(2^m * (m-n))**（m为s1、s2中长度较长者）
注意：由于这个方法是指数爆炸增长的复杂度，**cpp代码会超时**。
##### 2、动态规划
使用二维布尔数组dp用于表示两数组某一位的交错情况：**dp[i][j]==1表示s1的前i位和s2的前j位可以交错组成s3的前i+j位**。知道dp数组表示的含义，问题也就迎刃而解。

```cpp
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int len1 = s1.size() , len2 = s2.size() , len3 = s3.size();
        if (len1+len2 != len3)
            return false;
        vector<vector<bool>> dp(len1+1 , vector<bool>(len2+1 , false));
        dp[0][0] = 1;
        // 初始化第一行
        for (int i = 1 ; i <= len2 ; ++i)
        {
            if (dp[0][i-1] == true && s2[i-1] == s3[i-1])
                dp[0][i] = true;
        }
        // 初始化第一列
        for (int i = 1 ; i <= len1 ; ++i)
        {
            if (dp[i-1][0] == true && s1[i-1] == s3[i-1])
                dp[i][0] = true;
        }
        for (int i = 1 ; i <= len1 ; ++i)
        {
            for (int j = 1 ; j <= len2 ; ++j)
            {
                if ((dp[i-1][j] == true && s1[i-1] == s3[i+j-1]) || (dp[i][j-1] == true && s2[j-1] == s3[i+j-1]))
                    dp[i][j] = true;
                else
                    dp[i][j] = false;
            }
        }
        // for (int i = 0 ; i < len1 ; ++i)  // 显示dp数组
        // {
        //     copy(dp[i].begin() , dp[i].end() , ostream_iterator<bool>(cout , " "));
        //     cout << endl;
        // }
        return dp[len1][len2];
    }
};
```

时间复杂度：**o(m*n)**（m，n为s1，s2数组的长度）
