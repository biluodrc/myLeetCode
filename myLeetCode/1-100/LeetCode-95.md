---
title: LeetCode-95 合理利用返回值
body:
  - article
  - comments
date: 2020-01-27 21:48:24
tags: LeetCode
categories: 刷题
---
#### 一、思路
最开始做这道题的时候，我把题目理解成了值为1到n的二叉搜索树有几颗，而不是这些树分别是什么。但这两个问题的关联度较大，问题的关键在于想到：**当我们确定了根节点的值是什么，那么他左右子树的取值范围就已经很明确了。**
若根节点的取值是在[left , right]中的一个值k，由二叉搜索树的定义可知，左子树的值在[left , k-1]，右子树的值在[k+1 , right]。假设我们已经求出左右子树（**注意求出的应该是一组树**，而不是一个树），所以我们用两重循环遍历所有左右子树搭配的可能，也就求出根节点值为k、取值范围在[left , right]间的所有树，**遍历即可求出值为[left , right]的所有树**。
这样问题就形成了递归，自然也就解决。
#### 二、注意：
在最开始的时候，我将helper()（代码中的函数）的返回值设置成void，虽然想到根节点确定，左右子树范围确定这一点，但是一直不知道该怎么转换成代码。（当时冒出的想法是，一棵树如果已经完整（有n个节点），那么就将他push_back到结果中，但是没有实现）
#### 三、代码
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> generateTrees(int n) {
        if (n == 0)
        {
            vector<TreeNode*> ans;
            return ans;
        }
        return helper(1 , n);
    }
    
    vector<TreeNode*> helper(int left , int right)
    {
        vector<TreeNode*> ans;
        if (left > right)
        {
            ans.push_back(NULL);
            return ans;
        }

        for (int i = left ; i <= right ; ++i)
        {
            vector<TreeNode*> leftTrees = helper(left , i-1);
            vector<TreeNode*> rightTrees = helper(i+1 , right);
            
            for (auto leftTree : leftTrees)
            {
                for (auto rightTree : rightTrees)
                {
                    TreeNode* root = new TreeNode(i);
                    root->left = leftTree;
                    root->right = rightTree;
                    ans.push_back(root);
                }
            }
        }
        return ans;
    }
};
```
