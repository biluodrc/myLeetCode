#### 一、前言

方法一共有两种大致的方向，① **深搜**，但是会超时，然后思考深搜的优化方法：**记忆化搜索**；② 动态规划，与记忆化搜索的方法思路相似，实现方法不同；

#### 二、深搜

深搜的方法，每一层搜索目标字符串 s 的切片 **s[idx : ]**，考虑从这一段字符串的那个位置 i 进行截取，**截取的切片 s[idx : i] 在给定字典 wordList 中**，然后以这一个切片作为子节点，继续向下对 **s[i : ]** 进行深搜。

分析可知，这个方法的时间复杂度是：**O(n^2 * m)**（n 为目标串 s 的长度，m 为字典wordList的长度）（线性搜索 wordList，如果使用集合的话会加速）

当出现 s = “aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa”，wordList = ["aaaaa" , "aa" , "a" , "aaaaaaaaaa"] 时，就会超时。

~~~python
class Solution {
public:
    bool ans = false;

    void dfs(string s , int idx , unordered_set<string> &wordDictSet) {
        if (idx == s.length())
            ans = true;

        for (int i = idx+1 ; i <= s.length() ; ++i)
        {
            if (wordDictSet.find(s.substr(idx , i-idx)) != wordDictSet.end())
                dfs(s , i , wordDictSet);
        }

        return;
    }

    bool wordBreak(string s, vector<string>& wordDict) {
        auto wordDictSet = unordered_set<string>();
        for (auto i : wordDict)
            wordDictSet.insert(i);
        
        dfs(s , 0 , wordDictSet);

        return ans;
    }
};
~~~

![image.png](https://i.loli.net/2020/08/28/itEPmMXAyRoklde.png)

#### 三、记忆化搜索

对于 s = "catsandssss"，wordList = ["cats" , "cat" , "sand" , "and"]，搜索到 ”catsand“ 有两种方式，“cat”+“sand” 或者 “cats”+”and“，深搜的过程中，我们进行 “cat”+“sand”  之后，就已经知道，继续向下搜索是没有结果的，但是 “cats”+”and“ 又重复了一遍这个过程，这里就会出现大量重复的搜索。
针对这个问题，我们使用一个记忆数组 memVec[s.length()]，**memVec[i] == false表示 s[i : ] 这个切片是不能搜索成功的，也就是说，这个切片不能用 wordList 中的字符串组合而成**。初始化 memVec 为 false。

~~~python
class Solution {
public:
    vector<bool> memVec;
    bool ans = false;

    void dfs(string s , int idx , unordered_set<string> &wordDictSet) {
        if (idx == s.length())
            ans = true;
        if (memVec[idx] == false)
            return;

        for (int i = idx+1 ; i <= s.length() ; ++i)
        {
            if (wordDictSet.find(s.substr(idx , i-idx)) != wordDictSet.end())
                dfs(s , i , wordDictSet);
        }
        memVec[idx] = false;

        return;
    }

    bool wordBreak(string s, vector<string>& wordDict) {
        auto wordDictSet = unordered_set<string>();
        for (auto i : wordDict)
            wordDictSet.insert(i);
        memVec = vector<bool>(s.length() , true);
        
        dfs(s , 0 , wordDictSet);

        return ans;
    }
};
~~~

#### 四、动态规划

dp数组，dp[i] == true 表示 s[ : i] 是可以被字典中的字符串拼接而成的，dp[i] == false 表示 s[ : i] 不能被字典中的字符串拼接而成。动态转移方程为，dp[i] == true if ( dp[j] == true && s[i:j] in wordList )，最终输出 dp[s.length]。

~~~python
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        bool dp[1000];
        dp[0] = true;
        auto wordDictSet = unordered_set <string> ();
        for (auto word: wordDict) {
            wordDictSet.insert(word);
        }

        for (int i = 1 ; i <= s.length() ; ++i)
        {
        	dp[i] = false;
            for (int j = 0 ; j < i ; ++j)
            { 
                if (dp[j] == 1 && wordDictSet.find(s.substr(j , i-j)) != wordDictSet.end())
                {
                    dp[i] = true;
                }
            }
        }

        return dp[s.length()];
    }
};
~~~

 时间复杂度：**O(n)**