##### 一、思路

将数字反转，**如果反转后的数字等于给定数字**，说明给定数字是回文数。

这里需要注意的问题是，**数字反转之后，有可能溢出**，比如INT_MAX反转之后是7463847412，int无法保存，所以需要用long long来存储。

##### 二、代码

```
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0)
            return false;
        
        long long y = 0;
        long long temX = x;
        while (x)
        {
            y = y*10 + x%10;
            x /= 10;
        }

        if (temX == y)
            return true;
        else
            return false;
    }
};
```

时间复杂度：**O(n)**