---
title: LeetCode-99 二叉搜索树多往中序遍历去想
body:
  - article
  - comments
date: 2020-01-31 15:46:43
tags: LeetCode
categories: 刷题
---

##### 一、错误思路
最开始的思路，是进行**深搜**，向左树走的时候节点 N 压入biggers栈中，向右树走的时候节点压入smallers栈中，当子树深搜结束后，再将栈顶的节点，也就是节点 N 弹出。然后每到一个节点 P，就判断该节点与biggers和smallers栈中所有元素之间的关系，如果该节点的值大于等于biggers数组中的某一个节点 Q1，或者是该节点的值小于等于smallers数组中的某一个节点 Q2，就说明 P 和 Q1/Q2 就是被交换的节点。
但是这个方法的问题是，只考虑了**任意节点与其一颗子树节点之间的关系，没有考虑两颗子树之间的关系**，导致如果交换的节点是在两颗子树上，就无法找到交换的节点。

##### 二、错误代码

```cpp
// 错误代码！！
class Solution {
public:
    void recoverTree(TreeNode* root) {
        vector<TreeNode*> biggers , smallers;
        helper(root , biggers , smallers);
    }

    // 返回值作为一种标记来表示是否已经找到被交换的节点
    bool helper(TreeNode *node , vector<TreeNode*> biggers , vector<TreeNode*> smallers)
    {
        if (node == NULL)
            return false;
        else
        {
            for (auto bigger:biggers)  // 检测node的值是否小于biggers中的所有元素的值
            {
                if (node->val >= bigger->val)
                {
                    swap(node->val , bigger->val);
                    return true;
                }
            }
            for (auto smaller:smallers)  // 检测node的值是否大于smallers中的所有元素的值
            {
                if (node->val <= smaller->val)
                {
                    swap(node->val , smaller->val);
                    return true;
                }
            }

            // 向左子树移动时，node是作为比左子树大的节点，故放入biggers中
            biggers.push_back(node);
            bool flag = helper(node->left , biggers , smallers);
            if (flag)  // 搜索到想要找的问题就直接返回
                return flag;
            biggers.pop_back();

            // 向右子树移动时，node是作为比右子树大的节点，故放入smallers中
            smallers.push_back(node);
            flag = helper(node->right , biggers , smallers);
            if (flag)
                return true;
            smallers.pop_back();

            return false;  // 当前node并不是被交换的节点
        }
    }
};
```

##### 三、正确思路
在处理二叉搜索树节点大小关系的问题时，都应该想到用中序遍历来处理问题。
**一个正确的二叉搜索树，他的中序遍历应该是一个递增的数组**，把握这一点，就能解决这个问题。
如果交换了两个节点，那么对应的中序遍历，就会出现两个位置有递减的情况。这样问题就变为：**得到树中序遍历的结果，将两个递减位置的节点进行交换**。
举个例子可能会更加直观：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131153305525.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNTc1OTI2,size_16,color_FFFFFF,t_70)
这时一个正确的二叉搜索树，他的中序遍历是：1 2 3 4 5 6
交换节点之后，变为：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131153510926.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNTc1OTI2,size_16,color_FFFFFF,t_70)
这是可以看到中序遍历变为：6 2 3 4 5 1，根据思路叙述，交换的位置应该是6 2和5 1，看到交换的节点是前一个递减对的前一个节点和后一个递减对的后一个节点。
还需要注意的一点是，有可能只有一个递减对，那么就是直接交换递减对的两个节点。
##### 四、正确代码
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
    void recoverTree(TreeNode* root) {
        vector<TreeNode*> midL;
        helper(root , midL);
        TreeNode *n1 , *n2;
        n1 = n2 = NULL;
        for (int i = 0 ; i < midL.size()-1 ; ++i)
        {
            if (n1 == NULL && midL[i]->val > midL[i+1]->val)
                n1 = midL[i];
            if (n1 != NULL && midL[i]->val > midL[i+1]->val)
                n2 = midL[i+1];
        }
        swap(n1->val , n2->val);
    }

    void helper(TreeNode *node , vector<TreeNode*> &midL)
    {
        if (node == NULL)
            return;
        else
        {
            helper(node->left , midL);
            midL.push_back(node);
            helper(node->right , midL);
        }
    }
};
```
