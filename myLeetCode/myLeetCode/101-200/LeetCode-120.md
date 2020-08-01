##### 一、思路

读完这道题，让人想到用Dijkstra算法来解决，但真的使用Dijkstra算法，反而是大材小用了并且无法满足空间复杂度为o(n)的加分项。而给定的数据**具有一定的分形结构**，并且Dijkstra算法也是一种动态规划算法，于是就想到用**类似动态规划的方法**来解决这个问题。

假设已知自顶到第k层的各个节点的最短路径长度分别保存在长度为k的dp数组中，那么寻找自顶到第k+1层的各个节点的最短路径长度也就很容易可以解决：将*各个节点对应的值*加上与其相连的两个节点的*dp值中较小的一个*。（除了最左端和最右端的两个位置，他们只与一个节点相连）且顶节点对应的dp数组已知，由数学归纳法可以证明解决了这个问题。

每一层与其dp数组的对应情况如下，模拟一遍就能很好的理解这个方法

![image.png](https://i.loli.net/2020/02/16/sLkznhcj59qVRZx.png)

然后题目提到了空间复杂度为o(n)的加分项。这个图形**类似杨辉三角形**，我们在求解杨辉三角形的第n行的时候，有一种空间复杂度为o(n)的方法，并且针对这种方法，由于dp数组前半部分的原始值之后还需要使用，所以，还可以有一种优化的从后往前遍历的方法。

##### 二、代码

```c++
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        vector<int> dp(triangle.size() , 0);
        int row = triangle.size();
        if (row == 0)
            return 0;

        dp[0] = triangle[0][0];
        for (int i = 1 ; i < row ; ++i)
        {
            dp[i] = dp[i-1]+triangle[i][i];  // 模拟移动最后一个节点
            for (int j = i-1 ; j > 0 ; --j) // 模拟中间的所有元素
                dp[j] = (dp[j]>dp[j-1]?dp[j-1]:dp[j])+triangle[i][j];
            dp[0] = dp[0]+triangle[i][0]; // 模拟移动第一个元素
        }
        
        // 返回dp数组的最小值
        int min = dp[0];
        for (int i = 0 ; i < row ; ++i)
            if (dp[i] < min)
                min = dp[i];
        return min;
    }
};
```

时间复杂度：**o(n^2)**（遍历整个三角形，各个节点都需要访问）

空间复杂度：**o(n)**（也就是三角形最后一行对应的dp数组）