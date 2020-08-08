---
title: LeetCode-2 需要考虑周全的问题
body:
  - article
  - comments
date: 2020-01-23 10:54:44
tags: LeetCode
categories: 刷题
---

### 一次遍历
这个问题主要考察的是链表的操作和出现特殊情况的处理。
1、当两个链表一样长的时候，问题比较好处理，直接挨个相加即可；
2、当两个链表不一样的时候，就需要对遍历节点进行判断，不能出现再访问属性val的语句；
3、由于一条链有可能为空这种极端情况，所以不能简单的采用原地算法；
4、问题有一个小坑，就是有可能最后还有一位进位，对这个情况要考虑在内；
5、可以通过添加一个哑节点来减少代码；

```python
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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode *scan1 = l1;
        ListNode *scan2 = l2;
        ListNode *head = new ListNode(0);  //这是哑节点
        ListNode *scan = head;
        int flag = 0;  //标记进位

        while (1)
        {
            if (scan1 == NULL && scan2 == NULL)
                break;
            int n1 = scan1!=NULL?scan1->val:0;
            int n2 = scan2!=NULL?scan2->val:0;
            scan->next = new ListNode((n1+n2+flag)%10);
            flag = (n1+n2+flag)/10;

            if (scan1 != NULL)
                scan1 = scan1->next; 
            if (scan2 != NULL)
                scan2 = scan2->next;
            scan = scan->next;
        }
        if (flag)  //如果最后还有进位
            scan->next = new ListNode(1);

        return head->next;
    }
};
```

时间复杂度：O(MAX{m,n})
空间复杂度：O(MAX{m,n})