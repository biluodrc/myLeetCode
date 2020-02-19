---
title: LeetCode-103 双端队列
body:
  - article
  - comments
date: 2020-02-06 12:49:13
tags: LeetCode
categories: 刷题
---

##### 一、思路的诞生
这个问题是在**层次遍历的基础上做了延伸**，寻找锯齿型的层次遍历。与一般层次遍历的单方向不同，锯齿形遍历是奇数层从左向右遍历，偶数层从右向左遍历（视根节点在奇数层）。**这种对称的操作让我们想到了对层次遍历的数据结构队列进行改变，变为对称的双端队列**（然鹅我并没有想到，我的想法还是使用队列，在入栈的顺序上做一些变化，后面会继续探讨这个想法不正确的地方。但是思路就是这样，**把遍历的对称性作为突破口**）。
##### 二、主要思路
在确定了使用双端队列之后，接下来的问题就是具体每一层的入队、出队的位置是怎样的：
基本的原则是：**同一层入队和出队的方向不同；相邻层入队方向不同，出队方向也不同**
基于这个原则，自己模拟一颗树的遍历过程会更加清晰的，我接下来给出我的模拟：
***假设：奇数层从队尾入，则偶数层从队头入***
初始化双端队列设为：d = [ 3 ]
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200206115714399.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNTc1OTI2,size_16,color_FFFFFF,t_70)
第一层（根节点）：根节点从队头出；因为偶数层从队头入，队尾出，为了满足从右向左的遍历顺序，所以根节点的**右节点先入，左节点后入**；同时将根节点的值压入该层的遍历结果中。此时d = [ 9 , 20]；
第二层：*20* 出队后，因为其子节点位于奇数层，所以将其子节点**先左后右**压入队尾。*9* 如果有子节点也是同样的道理。压队的同时，将*20 9*按顺序压入该层的遍历结果中。此时d = [ 15 , 7]；
第三层：*15* 和 *7* 没有子节点，故没有压队的步骤，只需将其按顺序压入该层的遍历结果即可。
此时队列为空，故遍历结束，返回总体遍历结果；
**注意：**
1、问题要求返回的结果是**分层保存**的，故我们需要将不同层的遍历结果放在不同的一维数组中，然后再将他们放到一个二维数组中；
2、每一层的遍历，我们都需要知道**哪些是该层的节点**（不能单纯的使用下一层节点数是上一层的二倍，因为这不一定是一颗满树），我使用的方法是建了一个和保存节点的双端队列完全相同的双端队列，操作和树节点入队出队完全对应。（当然还有别的方法，暂时还能想到的是使用哈希表）

##### 三、代码

```clike
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector< vector<int> > ans;
        vector<int> tem;
        deque<int> d1;  //存放节点的层数
        deque<TreeNode*> d2; //存放节点
        if (root == NULL)  // 空树
            return ans;

        d1.push_back(1);
        d2.push_back(root);
        int depth = 1;
        while (!d1.empty())
        {
            if (depth % 2 == 1)
            {
                while (1)
                {
                    int nowD = d1.front();
                    TreeNode* node = d2.front();
                    if (nowD != depth)  //将这一层的所有节点弹出来
                        break;
                    tem.push_back(node->val);
                    // 将左右节点压队列（先左后右）
                    d1.pop_front();
                    d2.pop_front();
                    if (node->left)
                    {
                        d1.push_back(nowD+1);
                        d2.push_back(node->left);
                    }
                    if (node->right)
                    {
                        d1.push_back(nowD+1);
                        d2.push_back(node->right);
                    }
                }
            }
            else
            {
                while (1)
                {
                    int nowD = d1.back();
                    TreeNode* node = d2.back();
                    if (nowD != depth)
                        break;
                    tem.push_back(node->val);
                    // 将右左节点压队列（先右后左）
                    d1.pop_back();
                    d2.pop_back();
                    if (node->right)
                    {
                        d1.push_front(nowD+1);
                        d2.push_front(node->right);
                    }
                    if (node->left)
                    {
                        d1.push_front(nowD+1);
                        d2.push_front(node->left);
                    }
                }
            }
            ans.push_back(tem);
            tem.clear();
            depth++;
        }

        return ans;
    }
};
```
时间复杂度：
$$
o(n)（遍历所有节点）
$$
