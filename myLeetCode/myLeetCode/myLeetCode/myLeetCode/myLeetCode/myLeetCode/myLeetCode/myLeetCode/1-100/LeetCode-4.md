---
title: LeetCode-4 超多方法;找到多个变量之间的关系来减少变量个数
body:
  - article
  - comments
date: 2020-01-29 23:39:59
tags: LeetCode
categories:	刷题
---

#### 一、直接顺序遍历到所求位置
所谓求中位数，**实际上就是求两个数组排序后的第 tar 个元素**（当两数组长度之和m+n为奇数时，tar1 = (m+n)/2，当为偶数时，tar1 = (m+n)/2，tar2 = tar1+1）。
可以想到**归并排序时归并的操作**，cnt1和cnt2分别指向两个数组当前遍历到的位置，选择两数组中较小的元素，将其对应遍历指针cnt加一。
在这里需要注意的是，有可能出现

> nums1 = {1,2}
> nums2 = {3,4,5,6,7,8}

这样的情况，这时就需要对下边进行判断，**当一个遍历指针已经越界的时候，就直接将另一个遍历指针加一**。
```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int len1 = nums1.size();
        int len2 = nums2.size();
        int tar1 = (len1+len2)/2;
        int tar2 = tar1+1;
        int cnt1 , cnt2;
        cnt1 = cnt2 = 0;
        int ans1 , ans2;
        ans1 = ans2 = 0;
        
        for (int i = 1 ; i <= tar2 ; ++i)
        {
            if (cnt2 >= len2 || (cnt1 < len1 && nums1[cnt1] < nums2[cnt2]))
            {
                if (i == tar1)
                    ans1 = nums1[cnt1];
                if (i == tar2)
                    ans2 = nums1[cnt1];
                cnt1++;
            }
            else
            {
                if (i == tar1)
                    ans1 = nums2[cnt2];
                if (i == tar2)
                    ans2 = nums2[cnt2];
                cnt2++;
            }
        }

        if ((len1+len2) % 2 == 0)
            return (ans1+ans2)/2.0;
        else
            return ans2;
    }
};
```
**时间复杂度：o(m+n)**
没有达到题目的要求，还需要再进行优化
#### 二、查找第tar个数
第一种方法使用顺序遍历的方式查找第tar个数字，时间复杂度必然无法达到log(m+n)，想要优化，第一个想到的就是用二分查找的方式加速查找，这时需要思考的问题就是： **用什么作为二分的标准？**
一种方法是将查找第tar数字进行二分，**每一次确定 tar/2 个一定在中位数之前的数字**；
另一种方法是将下标作为二分的标准，找到两个数组分块位置之间的关系，**对一个数组进行标准的二分**；

第一种方法是通过一定的判断，将数组舍去一部分，假设两个数组为

> nums1 = {1,3,5,6};
> nums2 = {2,4,9,10,12};

可知：tar这时是等于4，则 tar/2 等于2，这时比较两个数组中第二个元素，由于 3<4 所以可以确定**nums1中的 {1,3}一定是小于最后的中位数。也就是第tar数**，新数组变为

>nums1 = {5,6};
>nums2 = {2,4,9,10,12};

我们的tar也就变成 4-2=2 然后进行新一轮的处理。

**在代码中，用k来代替tar**
```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int len1 = nums1.size() , len2 = nums2.size();
        int len = len1+len2;
        if (len % 2 == 0)  // 需要探查查两次
        {
            int ans1 = helper(nums1 , nums2 , len/2);
            int ans2 = helper(nums1 , nums2 , len/2+1);
            return (1.0*ans1+ans2)/2;
        }
        else  //只要探查一次
        {
            int ans = helper(nums1 , nums2 , len/2+1);
            return ans;
        }
    }
    int helper(vector<int> nums1 , vector<int> nums2 , int k)
    {
        if (nums1.size() == 0)
        {
            return nums2[k-1];
        }
        else if (nums2.size() == 0)
        {
            return nums1[k-1];
        }
        else if (k == 1)
        {
            return nums1[0]>nums2[0]?nums2[0]:nums1[0];
        }
        int k_2 = k/2;
        int p1 = k_2>nums1.size()?nums1.size():k_2;
        int p2 = k_2>nums2.size()?nums2.size():k_2;
        
        if (nums1[p1-1] > nums2[p2-1])
        {
            return helper(nums1 , vector<int>(nums2.begin()+p2,nums2.end()) , k-p2);
        }
        else
        {
            return helper(vector<int>(nums1.begin()+p1,nums1.end()) , nums2 , k-p1);
        }
    }
};
```
**时间复杂度：o(log(m+n))**（在代码中实际是o(log(k))，因为k=(m+n)/2，故时间复杂度也就是o(log(m+n))）
#### 三、二分查找
##### 1、心路历程
当我看到题目中要求的复杂度有 log 的时候，第一个想到的就是二分查找，由于二分查找是作用于一个数组，所以我的想法就是分别有left1、right1、left2、right2来表示两个数组的边界。同时，注意到中位数的定义，决定了我们最后要找的数字，**两个数组的分界线两边的元素个数应该是相同的（在长度和为奇数的情况下）**，这样也就约束了二分查找时的**递归条件**。而中位数的定义决定了，**位于中位数左边的元素一定是小于位于它右边的元素**，这样就约束了**递归出口条件**。
但是由于这个方法引入了两个分界线，虽然知道它们之间的关系，但是在编写代码时还是容易产生混乱，然后，我最后没有把这个思路编成代码（手动狗头），*望有缘人能完成它*。
##### 2、解题思路
根据*两个数组的分界线两边的元素个数应该是相同的（在长度和为奇数的情况下）*，假设分界线为p1和p2，需要注意的是，上面思路中，是将下标作为分界线，而这里是**将两个下标之间的位置假象为分界线**，故

>当len1+len2为偶数时
> ∵ p1 + p2 = len1-p1 + len2-p2
> ∴ p2 = (len1+len2) / 2 - p1;
> 当len1+len2为奇数时
> ∵ p1 + p2 = len1-p1 + len2-p2 + 1
> ∴ p2 = (len1+len2 + 1) / 2 - p1; 

**注意：**
①、因为**代码中下标都为整型**，所以偶数情况等价于 p2= (len1 + len2 + 1) / 2 - p1
②、因为分界线一定是非负的，所以 p1 应该是长度较小数组的分界线（以 p1 作为二分查找的中点时）

***为了写题解方便，暂时不考虑边界条件（p1 != 0 , p1 != len1 , p2 != 0 , p2 != len2）***
根据*位于中位数左边的元素一定是小于位于它右边的元素*，由于同一个数组元素已经有序，需要判断的就是不同数组分界线处元素的大小关系。

> 假设某一次迭代两数组分解情况如下
> nums1 = { ...... 4 | 5 ...... }
> nums2 = { ...... 2 | 3 ...... }
> 很容易就能发现，如果将这种分界情况作为答案，一定是不可行的：因为 4<3 ，分界线左端元素不满足恒小于分界线端元素。这时为了得到合理的分界情况，就希望 nums1 的分界线向左移，nums2 的分界线会随之移动。
> 同时还有
> ②
> nums1 = { ...... 2 | 3 ...... }
> nums2 = { ...... 4 | 5 ...... }
> 这种情况，这时就希望 nums1 的分界线向右移动

因为使用二分查找，所以分界线的移动都是通过**端点的移动**来进行：
**向左移，就将右端点赋值为 p1-1;
向右移，就将左端点赋值为 p1+1;**

大致思路完成后，就需要考虑端点的情况，通过示例，能知道需要处理那些边界条件，**这些边界条件不仅在递归过程中，还会在最后的递归出口用到**：

> 比较两端元素时，分界线已经到端点位置
> ①
> nums1 = { | ...... }
> nums2 = { ...... | ...... }
> ②
> nums1 = { ...... | ...... }
> nums2 = { | ...... }
> ③
> nums1 = { ...... | }
> nums2 = { ...... | ...... }
> ④
> nums1 = { ...... | ...... }
> nums2 = { ...... | }

##### 3、具体代码

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int len1 = nums1.size();
        int len2 = nums2.size();
        if (len1 > len2)
            return findMedianSortedArrays(nums2 , nums1);
        int left = 0 , right = len1;
        while (1)
        {
            int p1 = (left+right+1) / 2;
            int p2 = (len1+len2+1)/2-p1;
            if (p1 != 0 && p2 != len2 && nums1[p1-1] > nums2[p2])  // 还需要将查找区间向左移
                right = p1-1;
            else if (p1 != len1 && p2 != 0 && nums2[p2-1] > nums1[p1])
                left = p1+1;
            else
            {
                int maxLeft = 0;
                if (p1 == 0) { maxLeft = nums2[p2-1]; }
                else if (p2 == 0) { maxLeft = nums1[p1-1]; }
                else { maxLeft = max(nums1[p1-1], nums2[p2-1]); }
                if ( (len1 + len2) % 2 == 1 ) { return maxLeft; } // 奇数的话不需要考虑右半部分

                int minRight = 0;
                if (p1 == len1) { minRight = nums2[p2]; }
                else if (p2 == len2) { minRight = nums1[p1]; }
                else { minRight = min(nums2[p2], nums1[p1]); }

                return (maxLeft + minRight) / 2.0; //如果是偶数的话返回结果
            }
        }
    }
};
```
**时间复杂度：o( log(min{m , n}) )**（m，n为两数组长度）

