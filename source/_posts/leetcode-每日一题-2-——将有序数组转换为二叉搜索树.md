---
title: leetcode 每日一题(2)——将有序数组转换为二叉搜索树
categories: 算法
date: 2020-07-08 14:15:12
tags:
---


题目描述：

将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

示例:

给定有序数组: [-10,-3,0,5,9],

一个可能的答案是：[0,-3,9,-10,null,5]，它可以表示下面这个高度平衡二叉搜索树：

      0
     / \

   -3   9
   /   /
 -10  5

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree

思路：

首先题目要求的是一个高度平衡的二叉树，则我们之间选择中序遍历，先确定根节点。我们知道根节点左孩子小于根节点，右孩子大于根节点，那之间选取有序数组的中间位子为根节点，这样就把原数组二分了，一次递归完成子数组根节点的选择。

c++实现实例：

```c++
 struct TreeNode {
     int val;
     TreeNode *left;
     TreeNode *right;
     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 };

class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        if(nums.size() == 0){
            return nullptr;
        }
        unsigned long index = nums.size() / 2;
        TreeNode* root =new TreeNode(nums[index]);
        std::vector<int>::const_iterator first = nums.begin();
        std::vector<int>::const_iterator end = nums.begin()+index;
        std::vector<int> left_vector(first, end);
        std::vector<int>::const_iterator firstRight = nums.begin()+index+1;
        std::vector<int>::const_iterator endRight = nums.end();
        std::vector<int> right_vector(firstRight,endRight);
        root->left = sortedArrayToBST(left_vector);
        root->right = sortedArrayToBST(right_vector);
        return root;
    }
};
```

