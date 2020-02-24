#### 一、118题回忆过去

##### （一）、随便聊一聊

这道题难度确实不高，就是根据我们以前学过的**递推杨辉三角形**的方法就可以了。我就不再赘述。后面的话就是我关于杨辉三角形的记忆，跳过就好！

我想讲一个高中时候关于杨辉三角形的事情。刚上高一的时候，我在课外班里看到一张草稿纸，上面写着一些关于杨辉三角形的推导、在二项式展开上的例子，还写着：“*为了感谢一个同学借给我这张草稿纸，我写下关于杨辉三角形的一些知识，供大家学习。*”（大概是这个意思，我并没有记得完整的话）。当时的我并不懂杨辉三角形，但这个同学写的确实很好，整整一页A4纸。记得那应该是一个午后，看这张纸的时候心中似乎并没有什么感觉，但每当我会想起这件事，心中总会有一种莫名的情绪。

说起这段记忆没什么意思，如果要强行给它加上一些与我写博客相关的意义的话，那可能是*不经意之间分享的知识或许真的会影响别人*。但我觉得这段回忆的意义并不在此，以后或许能明白。

##### （二）、代码

~~~c++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        if (numRows == 0)
            return vector<vector<int>>(0);

        vector<vector<int>> ans;
        ans.push_back(vector<int>(1 , 1));
        for (int i = 1 ; i < numRows ; ++i)
        {
            ans.push_back(vector<int>(i+1));
            ans[i][0] = ans[i][i] = 1;
            for (int j = 1 ; j < i ; ++j)
                ans[i][j] = ans[i-1][j-1]+ans[i-1][j];
        }

        return ans;
    }
};
~~~

时间复杂度：**o(n)**（n为杨辉三角形的元素个数）

#### 二、119题原地递推

##### （一）思路

###### 1、思路一

解决118题之后，119题的思路其实是显而易见的。将上一题在不同空间递推改为在一个空间进行递推。递推时需要注意一个问题，如果从前往后递推的话，会出现**被更新的元素原始的值还需要在后面使用**，解决这个问题有两个方法：第一个方法是添加一个prev变量，保存被更新元素的原始值；第二个方法是从后往前递归，自然就不会出现这样的问题。

###### 2、思路二

除了上面这个思路以外，还有一个利用杨辉三角形的应用的思路，就是**第n行的第k个元素的值等于组合数**
$$
C_n^k = \cfrac{n!}{k!·(n−k)!}=\cfrac{n·(n−1)·(n−2)·...·(n−k+1)}{k!}
$$


为了计算每一个第rowIndex行的元素，看到杨辉三角形对称的性质，以及前后项之间的数值关系：
$$
C_n^k = C_n^{k-1}·\cfrac{n-k+1}{k}
$$
在编写利用公式的代码时会有这样一个问题：

**当更新当前位置的组合数时，需要先进行乘法然后进行除法，有乘法就会有越界的危险。**

![image.png](https://i.loli.net/2020/02/16/17LEbQdfGXkSpPM.png)

*所以需要先用longlong型的空间来保存数据，然后再压入结果序列中的时候再转为int型。*

##### （二）代码（三种方法）

```c++
// 顺推采用prev保存前一个的原始值
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> ans(rowIndex+1 , 1);
        for (int i = 2 ; i <= rowIndex+1 ; ++i)
        {
            int prev = ans[0] , tem;
                for (int j = 1 ; j < i-1 ; ++j)
                {
                    tem = ans[j];
                    ans[j] = ans[j]+prev;
                    prev = tem;
                }
        }
        return ans;
    }
};
```

```c++
// 顺推直接消除会覆盖原始值的问题
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> ans(rowIndex+1 , 1);
        for (int i = 2 ; i <= rowIndex ; ++i)
        {
            for (int j = i-1 ; j > 0 ; --j)
                ans[j] = ans[j]+ans[j-1];
        }
        return ans;
    }
};
```

```c++
// 利用公式直接推导
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> ans;
        long long prev;
        int cur;
        prev = 1;
        ans.push_back(prev);
        for (int k = 1 ; k <= rowIndex ; ++k)
        {
            cur = prev * (rowIndex-k+1) / k;
            ans.push_back(cur);
            prev = cur;
        }
        return ans;
    }
};
```

