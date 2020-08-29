---
title: LeetCode-100 留心小问题即可
body:
  - article
  - comments
date: 2020-01-31 09:18:04
tags: LeetCode
categories: 刷题
---

#### 一、问题及注意点
这道题主要是考察对节点为空的情况时的考虑，想要访问某一个节点的内容，第一个需要解决的问题就是**排除节点空的情况**。
剩下的就是编写类似深搜的代码。

#### 二、具体代码

```cpp
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        return helper(p , q);
    }
    bool helper(TreeNode* p , TreeNode* q)
    {
        if ((p == NULL && q != NULL) || (p != NULL && q == NULL))  // 判断只有一者空的情况
            return false;
        else if (p == NULL && q == NULL)  // 判断出现节点都空的情况
            return true;
        else
        {
            if (p->val != q->val)
                return false;
            bool flag1 = helper(p->left , q->left);
            bool flag2 = helper(p->right , q->right);
            return flag1*flag2;
        }
    }
};
```
时间复杂度：o(n)（n为树节点个数）