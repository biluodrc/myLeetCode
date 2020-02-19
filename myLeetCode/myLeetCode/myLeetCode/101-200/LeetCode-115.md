---
title: LeetCode-115 真的没想出来状态转移方程
body:
  - article
  - comments
date: 2020-02-10 20:44:29
tags: LeetCode
categories: 刷题
---

##### 一、心路历程
这道题是有关字符串的问题，与之前字符串匹配的问题不同，它要求的**不是子串匹配，也不是模式串匹配，而是序列匹配。**
问题发生了变化，但不变的思路是：~~暴力！暴力就完了！~~ ，这道题如果使用暴力法可就真的“完了”。暴力法是使用类似bfs的思路，每一层递归遍历两个字符串，当某一位字符相同时，就递归到下一层，判断这一位之后的子串的匹配情况，当递归到 t字符串尾时，就将匹配方案数加一。还可以有剪枝的操作：如果 s字符串子串的长度比 t字符串子串的长度还要小，那就说明不可能完成匹配，所以直接返回到上一层即可。
但是**这个方法的时间复杂度爆炸**，是指数级别。然后就没有AC。
这时候就会想到总会在关键时刻拯救我们的动态规划。想到这，我以为只要写出状态转移方程不就好了吗！但是，现在我的问题又变成了：*写不出状态转移方程/(ㄒoㄒ)/~~*，然后还是看了题解。。。在**主要思路**之后我会说到我在写这个问题时对写状态转移方程时产生的灵感。
##### 二、主要思路
第一步：完成状态转移方程之前很关键的一步，就是**明确 dp[i][j] 到底表示的是什么**。这个问题中表示的是 t 的前 i+1 个字符和 s 的前 j+1 个字符有dp[i][j]种匹配方案。
第二步：**状态转移方程：**

>当 s[j] != t[i] 时：dp[i][j] = dp[i][j-1]

这里表示的是，**反正 s[j] 和 t[i] 都不一样，那么我就不要 s[j]，用 t 的前 i+1个字符去匹配 s 的前 j 个字符**，故赋值为dp[i][j-1]

>当 s[j] == t[i] 时：dp[i][j] = dp[i][j-1]+dp[i-1][j-1]

这里表示的是两种情况
1、用到 s[j] ，也就是用 t 的前 i+1 个字符去匹配 s 的前 j+1个字符，方案的数目等于用 t 的前 i 个字符去匹配 s 的前 j 个字符，也就是dp[i-1][j-1]；
2、不用 s[j] ，这时就变成了用 t 的前 i+1个字符去匹配 s 的前 j 个字符，也就是dp[i][j-1]；

这个状态转移方程其实并不是显而易见的，对这个方程的解释，也是我在做完题之后才能够叙述的。但是最关键的还是掌握 dp[i][j] 指的是什么。我产生的灵感也就是来源于此：**写出一个一般性的例子，将 dp 这个二维数组画出来，自己去填每一个空，然后找 dp[i][j]、dp[i-1][j-1]、dp[i][j-1]、dp[i-1][j] 之间的关系，找到后看是否能对其进行解释**。这种方法可能就是取巧，但是可以做题的方法了。
##### 三、代码

```cpp
// 动态规划
class Solution {
public:
    int numDistinct(string s, string t) {
        int lenS = s.size();
        int lenT = t.size();
        vector<vector<long long>> dp(t.size()+1 , vector<long long>(s.size()+1,0));  // 行列数各加一是为了添加空字符串匹配的dp[0][0]
        
        // 初始化dp数组
        for (int i = 0 ; i <= lenS ; ++i)
            dp[0][i] = 1;
        
        for (int i = 1 ; i <= lenT ; ++i)
            for (int j = 1 ; j <= lenS ; ++j)
            {
                if (t[i-1] == s[j-1])
                    dp[i][j] = dp[i][j-1]+dp[i-1][j-1];
                else
                    dp[i][j] = dp[i][j-1];
            }

        return dp[lenT][lenS];
    }
};
```
时间复杂度：**o(m*n)**（m、n分别为s、t字符串的长度）

```cpp
// 暴力法——没有AC
class Solution {
public:
    int numDistinct(string s, string t) {
        int ans = 0;
        helper(s , 0 , t , 0 , ans);
        return ans;
    }

    void helper(string s , int indS , string t , int indT , int &ans)
    {
        int lenS = s.size();
        int lenT = t.size();
        if (indT == lenT)
        {
            ans++;
            return;
        }
        for (int i = indS ; i < lenS ; ++i)
        {
            if (lenS-i < lenT-indT)
                break;
            if (s[i] == t[indT])
                helper(s , i+1 , t , indT+1 , ans);
        }
    }
};
```
时间复杂度：
$$
o(2^n)（我的想法是s字符串中每一个字符都有选择与不选两种情况，组合起来就是指数级别）
$$
