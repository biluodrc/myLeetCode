---
title: LeetCode-102 层次遍历
body:
  - article
  - comments
date: 2020-02-01 16:58:43
tags: LeetCode
categories: 刷题
---

##### 一、思路
这道题使用的是二叉树的广度优先遍历，也就是层次遍历。与单纯的层次遍历不同的地方在于，这个问题**要求返回的遍历结果是一个二维数组**，一行代表树的一层。但是总的思路还是和层次遍历没有区别。
为了知道二叉树中**每一个节点位于书的哪一层的问题**，**需要标识每一个节点所在的层数**，并根据层次遍历时的节点，对其子节点进行标识。在这里我想到的方法有两种：第一种是建一个**队列**保存节点的层数，与层次遍历的队列保持一一对应；第二种是建一个**哈希表**，来实现每一个节点和其层数之间的对应关系。这里的标识方法甚至都可以使用线性表，但是考虑到查找的效率太低，故不使用线性表。
##### 二、代码

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        queue<TreeNode*> q;
        queue<int> qDepth;
        vector< vector<int> > ans;
        if (root == NULL)
            return ans;
        vector<int> tem;
        q.push(root);
        qDepth.push(1);

        int nowDepth = 1;  // 标记现在已经遍历到哪一层
        while (!q.empty())  // 标准的层次遍历
        {
            TreeNode *node = q.front();
            q.pop();
            int depth = qDepth.front();
            qDepth.pop();

            if (node->left)
            {
                q.push(node->left);
                qDepth.push(depth+1);
            }
            if (node->right)
            {
                q.push(node->right);
                qDepth.push(depth+1);
            }

            if (depth == nowDepth)
            {
                tem.push_back(node->val);
            }
            else  // 每当到达新的一层将上一层的节点压入ans数组中
            {
                nowDepth++;
                ans.push_back(tem);
                tem.clear();
                tem.push_back(node->val);
            }
        }
        ans.push_back(tem);  // 注意最后还需要再将再压一次ans
        					 // 假设层数为m，当到达m+1层的时候，会直接跳出循环
        					 // 所以没有执行m层的压ans操作

        return ans;
    }
};
```
