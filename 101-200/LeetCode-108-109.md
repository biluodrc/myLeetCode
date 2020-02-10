---
title: LeetCode-108-109 对链表的二分方法
body:
  - article
  - comments
date: 2020-02-08 22:46:03
tags: LeetCode
categories: 刷题
---

##### 一、思路
这两道题都是要求将线性表转换成二叉搜索树，数组转化为二叉搜索树是很简单的，使用**标准的二分法**即可，不再赘述，之后附上代码。
重点的问题是如何将链表转化为二叉搜索树（这里就不谈将链表转为数组这种只为AC的方法了）。由于不能用下标来访问链表，所以单纯地用数组转树的二分法是不合理的。那么不使用下标，该如何访问链表中点处的节点呢？方法是**用快指针和慢指针来查找中点处的节点**：*快指针每次移动两个节点，慢指针每次移动一个节点，当快指针访问到链尾的时候，慢指针正好指的位置就是中点处的节点。* Over！

##### 二、代码
```cpp
// 108题代码
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return helper(nums , 0 , nums.size());
    }

    TreeNode* helper(vector<int> &nums , int left , int right)
    {
        if (left >= right)
            return NULL;
        int mid = (left+right)/2;
        TreeNode* node = new TreeNode(nums[mid]);
        node->left = helper(nums , left , mid);
        node->right = helper(nums , mid+1 , right);
        return node;
    }
};
```

```cpp
// 109题代码
class Solution {
public:
    TreeNode* sortedListToBST(ListNode* head) {
        if (head == NULL)
            return NULL;

        return helper(head , NULL);
    }
    TreeNode* helper(ListNode *left , ListNode *right)  // 表示一种左闭右开
    {
        if (left == right)
            return NULL;
        ListNode *slow , *fast;
        slow = fast = left;
        while (1)
        {
            if (fast == right || fast->next == right)
                break;
            fast = fast->next->next;
            slow = slow->next;
        }
        TreeNode *ans = new TreeNode(slow->val);
        ans->left = helper(left , slow);
        ans->right = helper(slow->next , right);
        return ans;
    }
};
```
