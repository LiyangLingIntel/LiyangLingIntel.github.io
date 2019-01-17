---
layout:     post
title:      "Work on LeetCode"
subtitle:    My answers on leetcode [持续更新]
date:       2019-01-15
author:     Lyon Ling
header-img: img/post-bg-code.jpg
catalog: true
tags:
    - Algorithm
    - Job Hunting
---

>I'm planning to collect my answers on leetcode here.
>
>One thing is to share my ideas, and another is to remind me to keep going.

### 1. Two Sum

Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly** one solution, and you may not use the *same* element twice.

**Example:**

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

**My code:**

```python
class Solution:
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        ret_indices = []
        num_dict = {}
        for i in range(len(nums)):
            if nums[i] in num_dict:
                return [num_dict[nums[i]], i]
            num_dict[target - nums[i]] = i
```

### 2. Add Two Numbers

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example:**

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```

**My code:**

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        carry = 0
        p = root = ListNode(0)
        while l1 or l2 or carry:
            n1 = n2 = 0
            if l1:
                n1 = l1.val
                l1 = l1.next
            if l2:
                n2 = l2.val
                l2 = l2.next
            carry, s = divmod(n1+n2+carry, 10)
            p.next = ListNode(s)
            p = p.next

        return root.next
```

### 3. Longest Substring Without Repeating Characters

Given a string, find the length of the **longest substring** without repeating characters.

**Example 1:**

```
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 
```

**Example 2:**

```
Input: "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

**My code:**

```python
class Solution:
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        
        s_dic = {}
        length = 0
        start = -1
        for i in range(len(s)):
            if s_dic.get(s[i], -1) > start:
                start = s_dic[s[i]]
            s_dic[s[i]] = i
            length = max(length, i-start)
        return length
```

