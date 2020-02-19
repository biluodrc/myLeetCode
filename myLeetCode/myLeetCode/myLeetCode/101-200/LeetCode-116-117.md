---
title: LeetCode-116-117 巧妙的方法
body:
  - article
  - comments
date: 2020-02-10 20:44:29
tags: LeetCode
categories: 刷题
---



##### 一、116题递归思路

初遇这道题，我想的是将他划分为子问题求解，也就是递归求解。思路是，假设在一层递归中，当前树的根节点为node，我有它左右子树最左边、最右边的节点所组成的数组，分别是*left，right1，left2，right*，如图所示：

![image.png](https://i.loli.net/2020/02/11/1ZPpv4dnQUJYTL6.png)



这时**添加next指针就变成了连接right1和left1对应位置的节点。**这样子问题就解决了，构建了一个以node为根、next指针连接完成的树（子树），为了上一层能继续连接其他节点，所以将node添加到left、right数组中，然后将left、right作为返回值。继续构建上一层的next连接。

**注意问题：**

1、116题不像117题，给定的树是一个满二叉树，所以不会有一层节点有断开位置的情况。如果116题想要使用这个思路，那就要注意：假设将上图中5节点去掉，此时right1就会变成 {2 , 5}；

2、返回值只能返回一项内容，无法同时返回left、right两个数组。我的解决方法是在上一层先定义两个数组，然后把他们作为引用的参数传到下一层；

##### 二、115题代码递归版本

~~~
class Solution {
public:
    Node* connect(Node* root) {
        if (root == NULL)
            return NULL;
        vector<Node*> left , right;
        helper(root , left , right);
        return root;
    }

    void helper(Node *node , vector<Node*> &left , vector<Node*> &right)
    {
        if (node->left == NULL && node->right == NULL)  // 到达叶节点
        {
            left.push_back(node);
            right.push_back(node);
            return;
        }
        vector<Node*> left2 , right1;
        helper(node->left , left , right1);
        helper(node->right , left2 , right);
        for (int i = 0 ; i < right1.size() ; ++i)
            right1[i]->next = left2[i];
        left.push_back(node);
        right.push_back(node);
    }

};
~~~

时间复杂度：**o(n^2)**（n为树节点的个数）

##### 三、116题迭代思路

从最终得到的连接后的二叉树可以看到，他的next指针是为了连接一层节点，那新的思路就有了：假设一层节点的连接情况已经处理好，那么处理下一层节点就是处理两个问题，**一是某个节点的左右子节点之间的连接关系，二是这一层中相邻节点子节点之间的连接关系**。且第一层只有一个根节点，他是默认处理好连接关系的，所以我们需要做的就是直接从每一层的最左边的节点开始遍历，处理所有节点的连接关系即可。

![image.png](https://i.loli.net/2020/02/11/hGHZeWSRwT6sOIU.png)



这个思路相较于迭代版本时间复杂度和空间复杂度都更好，写出来的代码也更加简洁。

##### 四、115题代码迭代版本

~~~
class Solution {
public:
    Node* connect(Node* root) {
        Node* leftMost = root;
        while (leftMost) // 遍历各层
        {
            for (Node *i = leftMost ; i != NULL ; i = i->next) // 遍历抹一层的所有节点
            {
                if (i->left)
                    i->left->next = i->right;
                if (i->next && i->right)
                    i->right->next = i->next->left;
            }
            leftMost = leftMost->left;
        }
        return root;
    }
};
~~~

时间复杂度：**o(n)**（n为树节点的个数）

##### 五、117题迭代思路

117题思路与116题大同小异，差别在于树不再限制在满二叉树，这个导致的直接问题就是我们在处理下一层的节点时，无论是同一个节点的子节点的连接，还是相邻节点（*这里的相邻就是next指针连接的相邻，而不是树的一层节点中的相邻情况*）的子节点连接，都有可能出现子节点为空的情况。

考虑下图的情况：

![image.png](https://i.loli.net/2020/02/11/nDiRrloETCXkKBJ.png)

> 此时前三层连接已经完成，接下来要处理的是⑧和⑨的连接情况，按照上一题的思路，⑧的next指针应该指向⑥的左子节点。但此时⑥的左子节点为空，所以要寻找下一层⑧之后第一个不为空的节点，作为⑧的next指针指向的空间；

> 同时，④的左右子节点为空，无法完成连接④的左右子节点这个操作；

> 并且，最左端节点也就不能用简单的leftMost = leftMost->left来迭代，可以再遍历本层节点的时候，同时寻找最左端的节点；

##### 六、116题代码迭代版本

```
class Solution {
public:
    Node* connect(Node* root) {
        Node* leftMost = root;
        while (leftMost)
        {
            Node *nextLeftMost = NULL;
            for (Node *scan = leftMost ; scan ; scan = scan->next)
            {
                if (nextLeftMost == NULL)
                {
                    if (scan->left)
                        nextLeftMost = scan->left;
                    else if (scan->right)
                        nextLeftMost = scan->right;
                }
                Node *leftLink , *rightLink;
                leftLink = rightLink = NULL;
                if (scan->left)  // 判断下一层节点链接的左节点是什么
                {
                    if (scan->right)
                    {
                        scan->left->next = scan->right;
                        leftLink = scan->right;
                    }
                    else
                        leftLink = scan->left;
                }
                else
                    leftLink = scan->right;
                
                for (Node *scan1 = scan->next ; scan1 ; scan1 = scan1->next)  // 寻找下一层链接的右节点是什么
                {
                    if (scan1->left)
                    {
                        rightLink = scan1->left;
                        break;
                    }
                    if (scan1->right)
                    {
                        rightLink = scan1->right;
                        break;
                    }
                }

                if (leftLink)
                    leftLink->next = rightLink;
            }

            leftMost = nextLeftMost;
        }

        return root;
    }
};
```

时间复杂度：
$$
o(n)（n为树节点个数）
$$
