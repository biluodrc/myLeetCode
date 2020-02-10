---
title: LeetCode-1 利用哈希表进行查找加速
date: 2020-01-23 10:30:11
tags: LeetCode
categories: 刷题
body: [article, comments]
---

问题可以转换为，已有一个ele（为数组中的任意元素），需要在数组中寻找另一个元素等于target-ele
### 1、暴力遍历法
最直观的想法遍历每一个元素，再对该元素进行遍历，查看是否有满足条件的其他元素，使得他们两的加和为target。  
```python
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for (int i = 0 ; i < nums.size() ; ++i)
        {
            for (int j = i+1 ; j < nums.size() ; ++j)
            {
                if (nums[i]+nums[j] == target)
                    return {i , j};
            }
        }
        return {-1 , -1};
    }
};
```
时间复杂度：O(n^2)
空间复杂度：O(1)

### 2、一遍hash
这个方法的想法主要是利用哈希表来进行查找加速。
遍历数组元素ele，将ele放入哈希表中（ele值作为外键，ele下标作为內键），同时判断target-ele是否在哈希表中：
如果在，那么搜索结束，直接返回；
如果不在，继续遍历，直到结尾；

```python
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        map<int, int> m;
        for (int i = 0 ; i < nums.size() ; ++i)
        {
            if (m.count(target-nums[i]) == 1)
            {
                return {m[target-nums[i]] , i};
            }
            m[nums[i]] = i;
        }
        return {-1, -1};
    }
};
```
时间复杂度：O(n)
空间复杂度：O(n)
