---
title: leetcode 每日一题(1)——回文数
tags: 算法
categories: 算法
date: 2020-06-10 21:44:30
---


判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例 1:

输入: 121
输出: true
示例 2:

输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
示例 3:

输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
进阶:

你能不将整数转为字符串来解决这个问题吗？

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/palindrome-number
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

##### 思路

左右反转之后相等，又不能转换为字符串做比较。那就直接反转Int。

11为例，十位的1需要处以10变为个位的1。各位的1需要乘以10变成十位的1。

很简单了。两种思路，遍历或递归，我们采取递归的例子：

```c++
class Solution {

public:
    bool isPalindrome(long x) {
        if (x < 0) {
              return false;
        }
        int revert = getRevertNumber(0, x);
        return revert == x;
    }

    long getRevertNumber(long result, long x) {
        if (x >= 10) {
              int temp = x % 10;
              return getRevertNumber(result * 10 + temp, x / 10);
            
        } else {
              return result * 10 + x;
        }
    }
};
```

