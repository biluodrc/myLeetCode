##### 一、思路

这道题在之前已经遇到过，在[最长回文子串暴力法中的判断部分](https://leetcode-cn.com/problems/longest-palindromic-substring/)出现。这道题相当于是对判断部分的延伸，重点在于处理**大小写**和**空格**。使用的方法是***双指针***，一个指针从头遍历，另一个指针从尾遍历，判断他们对应字符的关系即可。

##### 二、代码

```c
class Solution {
public:
    bool isPalindrome(string s) {
        transform(s.begin(), s.end(), s.begin(), ::tolower);
        int i = 0;
        int j = s.size()-1;
        while (i < j)
        {
            while (i < s.size())
            {
                if (('a' <= s[i] && s[i] <= 'z') || ('0' <= s[i] && s[i] <= '9'))
                    break;
                i++;
            }
            while (j > -1)
            {
                if (('a' <= s[j] && s[j] <= 'z') || ('0' <= s[j] && s[j] <= '9'))
                    break;
                j--;a
            }
            if (i < s.size() && j > -1 && s[i] != s[j])
                return false;
            i++;
            j--;
        }
        return true;
    }
};
```

时间复杂度：**o(n)**