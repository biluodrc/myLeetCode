##### 一、思路

直接进行深搜，添加一个参数用于暂时存储到达当前节点时，所组成的数字。到达叶节点时，将完整的数字加到总和sum中（这里的sum可以用全局变量，也可以将他作为引用参数，都能达到加的时候操作的是同一个变量或者说是空间）



##### 二、代码

```c++
class Solution {
public:
    int sumNumbers(TreeNode* root) {
        if (root == NULL)
            return 0;

        int sum = 0;
        helper(root , sum , 0);
        return sum;
    }

    void helper(TreeNode* node , int &sum , int num)
    {
        if (node->left == NULL && node->right == NULL)
        {
            num = num*10+node->val;
            sum += num;
            return;
        }

        num = num*10+node->val;
        if (node->left)
            helper(node->left , sum , num);
        if (node->right)
            helper(node->right , sum , num);
    }
};
```

