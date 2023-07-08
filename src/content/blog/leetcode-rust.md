---
author: cosin
pubDatetime: 2023-07-08T11:17:18+08:00 
title: leetcode 题解
featured: false
draft: false
tags:
  - color-schemes
  - docs
description:
  使用rust学习leetcode题目
---

## 2.Add Two Numbers
```rs
impl Solution {
    pub fn add_two_numbers(
        l1: Option<Box<ListNode>>,
        l2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        match (l1, l2) {
            (None, None) => None,
            (Some(v), None) | (None, Some(v)) => Some(v),
            (Some(v1), Some(v2)) => {
                let sum = v1.val + v2.val;
                if (sum < 10) {
                    Some(Box::new(ListNode {
                        val: sum,
                        next: Solution::add_two_numbers(v1.next, v2.next),
                    }))
                } else {
                    Some(Box::new(ListNode {
                        val: sum - 10,
                        next: Solution::add_two_numbers(
                            Solution::add_two_numbers(Some(Box::new(ListNode::new(1))), v1.next),
                            v2.next,
                        ),
                    }))
                }
            }
        }
    }
}
```

## 3. Longest Substring Without Repeating Characters
```rs
impl Solution {
  pub fn length_of_longest_substring(s: String) -> i32 {
      let mut ans = 0;
      let mut chars: [usize; 128] = [0; 128];
      let mut start = 0;
      for (end, ch) in s.chars().enumerate() {
        start = start.max(chars[ch as usize]);
        ans = ans.max(end - start + 1);
        chars[ch as usize] = end + 1;
      }
      ans as i32
  }
}
```
