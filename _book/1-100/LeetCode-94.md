---
title: LeetCode-94 莫里斯中序遍历
body:
  - article
  - comments
date: 2020-02-07 16:23:52
tags: LeetCode
categories:	刷题
---

中序遍历常见的是递归和用栈辅助遍历两种方法，这两种方法网上很常见，故我不再赘述。
在看题解的时候，我看到一种莫里斯中序遍历，很有意思，就在写了一份关于这个的代码。
##### 一、思路
首先是莫里斯中序遍历的过程：
>对于当前节点node来说，
>如果node的左子树是空的，那么就将node的值压入输出序列中，并更新node的值为其右指针；
>否则，将node左子树最右端节点的右指针指向node（这一步的目的之后会说），并更新node的值为其左指针；

从这个过程中可以看到，压入输出序列的时间是当node左子树为空的时候。为了使左子树变空，那么就需要对不空的情况进行处理：易知，**node节点压入输入序列一定是在其左子树压入输入序列之后**，而一棵树中，**最右侧的节点一定是最后压入的**，所以将node节点放到其左子树最右端的右指针上，就能构造一个同时满足这两点，又不会改变树的输出序列的另一棵二叉树。
思路就是这样，在编写代码的时候，还需要注意一个问题：当node作为其左子树最右端节点的右指针的时候，还需要处理此时node的左指针，否则这棵树就会出现闭环，不再是二叉树。
##### 二、代码

```cpp
// 莫里斯中序遍历
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> ans;
        TreeNode *node = root;

        while (node != NULL)
        {
            if (node->left == NULL)  // 左子树空
            {
                ans.push_back(node->val);
                node = node->right;
            }
            else // 左子树未空
            {
                TreeNode *scan = node->left;
                while (scan->right != NULL) // 搜索左节点向右遍历最右侧的节点
                    scan = scan->right;
                scan->right = node;
                TreeNode *tem = node;
                node = node->left;
                tem->left = NULL;
            }
        }

        return ans;
    }
};
```
时间复杂度：o(n)
空间复杂度：o(n)

```cpp
// 中序遍历的递归方法
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> ans;
        dfs(root , ans);
        return ans;
    }
    void dfs(TreeNode *node , vector<int> &ans)
    {
        if (node == NULL)
            return;
        
        dfs(node->left , ans);
        ans.push_back(node->val);
        dfs(node->right , ans);
    }
};
```

```cpp
// 中序遍历的迭代方法
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> ans;
        stack<TreeNode*> s;
        if (root != NULL)
            s.push(root);

        while (!s.empty())
        {
            TreeNode *now = s.top();
            if (now->left == NULL)
            {
                ans.push_back(now->val);
                s.pop();
                if (now->right != NULL)
                    s.push(now->right);
            }
            else
            {
                s.push(now->left);
                now->left = NULL;
            }
        }

        return ans;
    }
};
```

