这道题要求将给定二叉树展开成链表。由示例可知，最终展开的链表顺序其实就是二叉树的**先序遍历顺序**。了解这一点之后，很容易就能想到直接改写二叉树的先序遍历算法。并且在原有的基础上还可以有所提升，也就是实现**原地**算法。这也就是我想到的方法一。

#### 一、改写先序遍历算法

##### （一）思路

原有的先序遍历，会得到一个节点的先根序列，只需要连接序列的前后节点，就可以得到展开后的链表。但是这种方法需要一个o(n)的指针数组用于记录先序遍历的结果。

*我们是否能在先序遍历的过程中，直接生成这个链表呢？*

假设当前遍历到树中的node节点，因为是按照先根顺序遍历，所以我们只需要知道**在node节点之前的节点是谁**就好。这里我使用了一个全局变量nowNode，这个节点表示的是当前的node节点的前一个节点（在先根序列中）。我们需要做的就是node连接到nowNode之后，并继续遍历他的子树。

这个方法实现了原地算法，我们只引入了一个nowNode。

在代码中，由于root是作为先根序列的第一个节点，所以在执行第一层函数helper(root)时，nowNode是为NULL。为了使代码更美观（否则就需要在helper函数中添加一个对nowNode==NULL的判断），我给最后生成的链表首先添加了一个**哨位节点**。这里有一次体现了哨位节点真是个好用的东西！

##### （二）代码

```c++
class Solution {
public:
    TreeNode *nowNode;
    void flatten(TreeNode* root) {
        if (root == NULL)
            return ;

        nowNode = new TreeNode(-1);  //类似于哨位节点
        helper(root);
    }

    void helper(TreeNode *node)
    {
        TreeNode *leftT = node->left;
        TreeNode *rightT = node->right;
        
        nowNode->right = node;
        nowNode->left = NULL;
        nowNode = nowNode->right;

        if (leftT != NULL)
            helper(leftT);
        if (rightT != NULL)
            helper(rightT);
    }
};
```

时间复杂度：**o(n)**

空间复杂度：**o(1)**



#### 二、分解子问题

##### （一）思路

分解子问题也是解决这个问题的好方法，思考这个方法的过程也是我一直在用的**假设法**。

首先明确还是使用递归的方法处理这个树，假设**经过helper(node)函数的处理，我们可以将一个以node为根的树处理成满足题意的链表，返回值是这个链表的尾节点（之后讨论为什么返回值是尾节点）**。在helper()函数中，我们首先处理他的左右子树，根据假设可知：左右子树被处理成左右子链表，并且得到两个链表的尾节点leftLast，rightLast。现在就是将两个链表和一个node合并成一个链表，将右链表连接到左链表尾，然后再将做链表连接到node->right。**递归的出口是遇到叶节点**，叶节点是一个长度为1的链表，直接返回自己就好。

这个思路重点是在讨论**左/右子树为空的情况**：当左树为空，我们直接将处理后的右链表连接到node->right即可；当右树为空，返回的尾节点就不是rightLast而是leftLast。

##### （二）代码

```
class Solution {
public:
    void flatten(TreeNode* root) {
        helper(root);
    }

    TreeNode* helper(TreeNode* node)
    {
        if (node == NULL)
            return NULL;
        if (node->left == NULL && node->right == NULL)
            return node;
        TreeNode* leftLast = helper(node->left);
        TreeNode* rightLast = helper(node->right);
        if (leftLast != NULL)
        {
            leftLast->right = node->right;
            node->right = node->left;
        }
        node->left = NULL;
        return rightLast?rightLast:leftLast;  // 返回最下面的一个节点，需要判断右树为空的情况
    }
};
```

时间复杂度：**o(n)**

空间复杂度：**o(1)**