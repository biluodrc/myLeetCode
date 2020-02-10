---
title: LeetCode-92 哨位节点的重要性
body:
  - article
  - comments
date: 2020-01-26 12:38:42
tags: LeetCode
categories: 刷题
---

#### 一、解题思路
这个问题其实等同于反转链表，只是将问题变成了子链表。
在反转链表的时候，是**引入了prev，scan，next三个指针**，分别表示当前节点的前一个节点，当前节点，当前节点的后一个节点。**遍历的过程只需要将scan->next赋值为prev**。最后处理头节点即可。
回到这个问题，其实就是在反转m至n号节点，将最后处理头节点的过程变成处理这一段链表两端节点。
#### 二、具体流程
首先需要先找到第m个节点，然后反转m至n号节点，然后将m号节点的next指针指向第n+1号节点，再将m-1号节点的next指针指向m-1号节点。
#### 三、重点问题
在处理的过程中会有这么几个问题：
eg：1->2->3->4->NULL
1、考虑m=1，n=2的情况，这种情况导致**m-1号节点为空**，如果直接对给定链表操作，会导致代码中不断地在考虑特殊情况，所以我们添加哨位节点，这样代码实现的难度直线下降。
2、考虑m=3，n=4的情况，这种情况会在next进行迭代的时候出现**空指针访问的情况**。所以在迭代的时候需要充分考虑空指针。
#### 四、代码
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode *prev , *scan , *next;
        ListNode *tem = new ListNode(0);
        tem->next = head;
        head = tem;  //添加一个哨位节点
        
        // 遍历找第m个节点
        scan = head;
        for (int i = 1 ; i < m ; ++i)
            scan = scan->next;
        ListNode *m_1Node = scan;
        ListNode *mNode = scan->next;

        // 反转m至n号节点
        prev = mNode;
        scan = mNode->next;
        next = mNode->next==NULL?NULL:mNode->next->next;  //考虑翻转最后一个节点的可能
        for (int i = m ; i < n ; ++i)
        {
            scan->next = prev;

            prev = scan;
            scan = next;
            next = next==NULL?NULL:next->next;  // 考虑n号节点之后再没有节点
        }

        // 连接两端
        m_1Node->next = prev;
        mNode->next = scan;

        return head->next;
    }
};
```

