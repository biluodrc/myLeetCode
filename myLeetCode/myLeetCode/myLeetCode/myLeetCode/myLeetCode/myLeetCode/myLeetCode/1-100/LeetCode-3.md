---
title: LeetCode-3 官方题解的优化滑动窗口
body:
  - article
  - comments
date: 2020-01-26 11:39:48
tags: LeetCode
categories: 刷题
---
#### 一、子问题
解决这道题之前，可以先思考一个重要的问题，就是**给定的一段字符串，如何判断它是否有重复的字符。**  
法一、容易想到的是遍历每一个字符，判断在它之后是否出现相同字符，时间复杂度o(n^2)。  
法二、同LeetCode-1，**用哈希表进行搜索加速**：维护一个数组作为哈希表，初始化为0。遍历字符串，并将每个字符对应位置（eg：‘f’-‘a’ = 5，‘f’对应下标为5）置为1，如果该位置已经置1，那么就说明出现重复字符。这个方法只需要一次遍历，时间复杂度o(n)。  
#### 二、问题思路
解决子问题之后，我们知道可以用哈希表来进行加速。维护一个数组作为哈希表，**初始化内容为-1**（不初始化为0是因为之后哈希表中保存的会是字符下标），遍历整个字符串，将每一个字符对应位置保存当前字符的下标。遍历至下标n处，如果该位置对应哈希表内容为m（m != -1），则说明 **[m,n) 是无重复字符子串，[m,n]会出现重复字符**。计算当前无重复子串长度后，保存较大者。之后重置哈希表，再进行之后字符串的遍历。  
这个方法也就是官方题解中的优化滑动窗口。  
#### 三、代码
```cpp
int lengthOfLongestSubstring(char * s){
    int from , to;  //当前最长子串起止下标
    from = to = 0;
    int ans = 0;
    int hash[256];  //每个字符的键值对
    memset(hash , -1 , sizeof(hash));
    
    while (s[to] != '\0')
    {
        if (hash[s[to]] == -1)
        {
            hash[s[to]] = to;
            to++;
        }
        else if (hash[s[to]] < from)  //重复的不是当前最长子串
        {
            hash[s[to]] = to;
            to++;
        }
        else  //当前最长子串出现重复，更新from的值为重复字符（前面的那个）位置加1
        {
            ans = ans > (to - from) ? ans : (to - from);
            from = hash[s[to]] + 1;
            hash[s[to]] = to;
            to++;
        }
    }

    ans = ans > (to - from) ? ans : (to - from);
    
    return ans;
}

```
