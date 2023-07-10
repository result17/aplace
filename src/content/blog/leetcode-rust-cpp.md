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

## 9. Palindrome Number
```rs
impl Solution {
  pub fn is_palindrome(x: i32) -> bool {
      if x < 0 {
        false
      } else {
        let mut rev_n = 0;
        let mut n = x;
        while n > 0 {
          let rest = n % 10;
          rev_n = rev_n * 10 + rest;
          n = n / 10;
        }
        rev_n == x
      }
  }
}
```

## 66. Plus One
```rs
impl Solution {
  pub fn plus_one(digits: Vec<i32>) -> Vec<i32> {
      let mut carry = true;
      let mut i = digits.len() - 1;
      let mut ans = Vec::<i32>::new();
      loop {
        let n = match carry {
            true => {
              let tmp = digits[i] + 1;
              if tmp < 10 {
                carry =  false;
              }
              tmp % 10
            },
            false => digits[i]
        };
        ans.push(n);

        if i == 0 {
          break;
        }

        i -= 1;
      }
      if carry {
        ans.push(1)
      }
      ans.reverse();
      ans
  }
}
```

## 172. Factorial Trailing Zeroes
```rs
impl Solution {
    pub fn trailing_zeroes(n: i32) -> i32 {
        let mut x = n;
        let mut count = 0;
        while x >= 5 {
            x /= 5;
            count += x;
        }
        count
    }
}
```

## 141. Linked List Cycle
```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) return true;
        }
        return false;
    }
};
```
