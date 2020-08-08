#### 一、找规律

##### （一）思路

看到这道题给出的图形是具有一定规律的时候，就会想到这是一道找规律的题目。题目要求按行输出Z字型排列的字符串，我们的思路也就是按行找每一个元素对应原来线性字符串的下标。

首先我们设定，len为字符串的长度；一个类v字型遍历作为一个完整循环，一个循环中包含 2r-2 个字符；整个z字型图像中包含 len/(2r-2) 个完整循环（最后一个v字有可能不完整）；**第 i 个循环中，第一行和最后一行只有一个字符，下标为 i(2r-2) 和 i(2r-2)+r-1，中间第 j 行有两个字符，下标为 i(wr-1)+j 和 i(2r-2)+(r-1)+(r-j-1)**，第一行和最后一行比较容易得到，而中间行推断的方式是根据中间行与第一和最后一行之间的距离得到。

具体代码中，由于我将有可能出现的不完整循环与完整循环统一操作，所以需要判断对应字符下标**是否越界的问题**。

##### （二）代码

~~~c++
class Solution {
public:
    string convert(string s, int numRows) {
        if (numRows == 1)
            return s;

        string ans;
        int len = s.size();
        int circle = len / (2*numRows-2); // 完整循环的个数

        // 处理第一行
        for (int i = 0 ; i < circle+1 ; ++i)
            if (i*(2*numRows-2) < len)
                ans.push_back(s[i*(2*numRows-2)]);
        // 处理中间的各行
        for (int j = 1 ; j < numRows-1 ; ++j)
            for (int i = 0 ; i < circle+1 ; ++i)
            {
                if (i*(2*numRows-2)+j < len)
                    ans.push_back(s[i*(2*numRows-2)+j]);
                if (i*(2*numRows-2)+numRows-1+numRows-j-1 < len)
                    ans.push_back(s[i*(2*numRows-2)+numRows-1+numRows-j-1]);
            }
        // 处理最后一行
        for (int i = 0 ; i < circle+1 ; ++i)
            if (i*(2*numRows-2)+numRows-1 < len)
                ans.push_back(s[i*(2*numRows-2)+numRows-1]);
        
        return ans;
    }
};
~~~

时间复杂度：**o(n)**（n为字符串长度）

#### 二、模拟Z字型遍历

##### （一）思路

一种比较暴力的做法是，**模拟Z字型字符串生成的过程**，用额外的空间将每一行字符单独存放，最后再把他们连接在一起，这样就可以得到要求的字符串。

模拟的过程很简单，有两种方法，

*1、将整个过程模拟出来，包括那些空出来的地方；*

*2、只模拟每行字符的相对位置关系，从上向下在从下向斜上；*

前者看起来更加清晰，但是在代码实现的时候，行和列都需要考虑，最后连接的时候也很不方便。而第二种方法**只需要考虑行元素的相对关系，而不考虑他们之间各自相差几个单位**。

编写代码时，为了表示向下和向斜上两种情况，使用变量flag来记录，且到达边界的时候，将flag取反，表示换方向。之后的代码就比较容易。

##### （二）代码

~~~c++
class Solution {
public:
    string convert(string s, int numRows) {
        if (numRows == 1) return s;

        vector<string> sim(min(numRows, int(s.size())));
        int r = 0;
        bool goingDown = false;

        for (char c : s) {
            sim[r] += c;
            if (r == 0 || r == numRows - 1) 
                goingDown = !goingDown;
            r += goingDown?1:-1;
        }

        string ans;
        for (string row : sim) 
            ans += row;
        return ans;
    }
};
~~~

