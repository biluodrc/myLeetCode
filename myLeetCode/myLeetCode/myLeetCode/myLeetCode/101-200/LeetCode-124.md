##### 一、思路

这道题确实难，而且是一道非常好的练习递归思想，将问题与其子问题联系起来的题目。开始的时候由于理解题目错误，导致之后无法实现代码。最开始我把这道题理解成了，寻找最大子树，而不是路径，同时寻找最大子树我也没有解决，写完题解还要想这个问题。

最大路径和与之前想错的思路还是有一定的重合，问题（子问题）就是一颗树的根为node，**假设已经知道node左右两颗子树各自的最大路径和，那么这棵树的最大路径和应该是多少？**

最终的最大路径中，节点之间排列关系（也就是每一层子问题中，返回的最大路径对应的node与其左右子树的排列关系）有三种的：一种是**包含node和node的两颗子树**，另两种是**包含node和node的一颗子树**：

![image.png](https://i.loli.net/2020/02/19/xfKdCRNA8bLVaZQ.png)

情况二、三可以视为同一种关系。

为了获得最大路径，在每一层的子问题中，都应该返回三种情况中最大的一个。这时就会出现一个问题：**我们无法知道，当前返回的最大路径就是最终结果的子图。**举一个栗子：

![image.png](https://i.loli.net/2020/02/19/LJFh1BTHvcnM4Xu.png)

这个问题就导致不能单纯返回一个一种情况来解决问题，更具体的来说，就是情况一限制了最大路径的上限，使其不能再向上延伸。解决这个问题的方法很简单，就是***在返回局部最大路径的同时，也返回情况二、三中的较大值*** ！！！

##### 二、代码

```c++
class Solution {
public:
    int maxPathSum(TreeNode* root) {
        int ans = INT_MIN;
        helper(root , ans);
        return ans;
    }

    int helper(TreeNode *node , int &ret)
    {
        if (node == NULL) 
            return 0;
        int maxl = helper(node->left, ret);
        int maxr = helper(node->right, ret);
        int sum = node->val + max(0, maxl) + max(0, maxr);
        int onepath = node->val + max(0, max(maxl, maxr));
        ret = max(ret, max(sum, onepath));
        return onepath;
    }
};
```

时间复杂度：
$$
o(N)（N为树的节点个数，整个过程需要寻找每一个节点对应树的局部最大路径和）
$$
空间复杂度：
$$
o(log_2^N)（递归的过程是一个不断压栈的过程，需要一个和树登高的空间来保存每个节点对应树的局部最大路径和）
$$
