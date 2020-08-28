这一道题是我遇到的第一道让我很烦心的题：多种解法、第二次再做用中心扩展总是会出错。一切的一切使这道题给我留下很深刻的记忆。首先指出第二次做一直出错的原因：

> string中的substr方法，substr(begin , n)完成的是从begin开始（包括begin），向后截取n个字符，而不是从begin到n的左开右闭区间！！！

#### 一、思路

##### 1、中心扩展算法

基本的暴力方法我就不再赘述，中心扩展算法是暴力法的一种优化，遍历所有可能的回文子串的中心位置，向两边扩展，找到该中心所对应的最长的回文子串。

回文子串有奇数长度和偶数长度两种情况，对应的中心位置也就有两种，遍历时需要考虑到这两种中心位置。

##### 2、动态规划算法

动态规划算法最重要的是**明确动态数组表示的是什么含义**。我们使用二维布尔数组dp：

> dp[i] [j] == true表示的是字符串s( i , j+1 )的切片是回文子串；
>
> dp[i] [j] == false表示的是字符串s( i , j+1 )的切片不是回文子串；

明确动态数组的含义之后，下一步就是**完成状态转移方程**：

> 假设我们已经知道dp[i] [j] == true，也就是s( i , j+1 )是回文子串，
>
> 以s( i , j+1 )为中心的回文子串一定是继续向两边扩展，
>
> 如果s[i-1] == s[j+1]，那么可以确定s( i-1 , j+2 )一定是回文子串，
>
> 也就是dp[i-1] [j+1] == true，

转换成标准的状态转移方程：**dp[i-1] [j+1] = ( dp[i] [j] == true and s[i-1] == s[j+1] )**

#### 二、代码

```
// 中心扩展
class Solution {
public:
    int extend(string s , int idx1 , int idx2)
    {
        while (idx1 >= 0 && idx2 < s.size() && s[idx1] == s[idx2])
        {
            idx1--;
            idx2++;
        }
        return idx2-idx1-1;
    }

    string longestPalindrome(string s) {
        if (s.size() < 1)
            return string();

        string ans = s.substr(0,1);
        int max = 1;
        int tem = -1;
        for (int i = 1 ; i < s.size() ; ++i)
        {
            // 一个字符向两边扩展
            tem = extend(s , i , i);
            if (tem > max)
            {
                max = tem;
                ans = s.substr(i-tem/2 , tem);
            }
            
            // 两个字符向两边扩展
            if (s[i] == s[i-1])
                tem = extend(s , i-1 , i);
            if (tem > max)
            {
                max = tem;
                ans = s.substr(i-tem/2 , tem);
            }
        }
        return ans;
    }
};
```

时间复杂度：**O(n^2)**

空间复杂度：**O(1)**

```
// 只遍历各种下标的可能的动态规划
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.size() < 1)
            return string();

        vector<vector<bool>> dp(s.size() , vector<bool>(s.size() , false));
        int n = s.size();

        // 初始化
        int ansX , ansY;
        ansX = ansY = 0;
        for (int i = 0 ; i < n ; ++i)
        {
            dp[i][i] = true;
            if (i+1 < n && s[i] == s[i+1])
            {
                dp[i][i+1] = true;
                ansX = i;
                ansY = i+1;
            }
        }

        for (int i = n-1 ; i > 0 ; --i)
        {
            for (int j = i+1 ; j < n ; ++j)
            {
                if (dp[i][j-1] == true && s[i-1] == s[j])
                {
                    dp[i-1][j] = true;
                    if (j-i+1 > ansY-ansX)
                    {
                        ansX = i-1;
                        ansY = j;
                    }
                }
            }
        }

        return s.substr(ansX , ansY-ansX+1);
    }
};
```

```
// 遍历所有子串长度及对应下标的动态规划法
int dp[1000][1000];

char * longestPalindrome(char * s){
    memset(dp , 0 , sizeof(dp));
    int len = strlen(s);
    if (len == 0)
		return s; 
    int max = 1;  //当前最长回文串长度
    //dp初始化
    for (int i = 0 ; i < len ; i++)
    {
        dp[i][i] = 1;
        if (i < len - 1 && s[i] == s[i + 1])
        {
            dp[i][i + 1] = 1;
            max = 2;
		}
    }
    
    //动态规划
    for (int i = 3 ; i <= len ; i++)  //检索所有长度的子串  !!注意i可以等于len 
    {
        for (int j = 0 ; j + i - 1 < len ; j++)
        {
            if (dp[j + 1][j + i - 2] && s[j] == s[j + i - 1])
            {
                dp[j][j + i - 1] = 1;
                if (i > max)
                    max = i;
			}
        }
    }
    
    //得出结果
    int u;
    for (int i = 0 ; i + max - 1 < len ; i++)
        if (dp[i][i + max - 1])
            u = i;
    s[u + max] = '\0';
    printf("%d" , u);
    return &s[u];
} 
```

时间复杂度：**O(n^2)**

（虽然和中心扩展算法时间复杂度的相同，但实际用时动态规划更长，这是因为中心扩展算法的最坏时间复杂度为O(n^2)（eg：s = “aaaaaaaaaa”），但是一般情况下，从中心向两边扩展，实际并不会扩展多少步；而动态规划算法，固定需要 1 + 2 + ... + n-1 步来进行转移的过程。所以平均用时会更长一些）

空间复杂度：**O(n^2)**