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

## 1. Two Sum
```rs
impl Solution {
    pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
        use std::collections::HashMap;
        
        let mut m: HashMap<i32, i32> = HashMap::new();
        for (i, v) in nums.iter().enumerate() {
            match m.get(&(target - *v)) {
                Some(&i2) => return vec![i as i32, i2],
                None => m.insert(*v, i as i32),
            };
        }
        vec![]
    }
}
```

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

## 21. Merge Two Sorted Lists
```rs
impl Solution {
    pub fn merge_two_lists(
        list1: Option<Box<ListNode>>,
        list2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        match (list1, list2) {
            (None, None) => None,
            (Some(list), None) | (None, Some(list)) => Some(list),
            (Some(list1), Some(list2)) => {
                let mut node = Box::new(ListNode::new(list1.val.min(list2.val)));
                if list1.val < list2.val {
                    node.next = Solution::merge_two_lists(list1.next, Some(list2));
                } else {
                    node.next = Solution::merge_two_lists(Some(list1), list2.next);
                }
                Some(node)
            }
        }
    }
}
```

## 121. Best Time to Buy and Sell Stock
```rs
impl Solution {
  pub fn max_profit(prices: Vec<i32>) -> i32 {
      let mut ans = 0;
      let mut min_cost = prices[0];
      for price in prices {
        ans = (price - min_cost).max(ans);
        min_cost = min_cost.min(price);
      }
      ans
  }
}
```

## 122. Best Time to Buy and Sell Stock II
```rs
impl Solution {
  pub fn max_profit(prices: Vec<i32>) -> i32 {
      let mut dp = vec![];
      let mut i = 0;

      while i < prices.len() - 1 {
        if prices[i] < prices[i + 1] {
          dp.push(prices[i + 1] - prices[i])
        }
        i += 1;
      }

      dp.iter().fold(0, |acc, x| acc + x)
  }
}
```
基于状态机
```rs
impl Solution {
  pub fn max_profit(prices: Vec<i32>) -> i32 {
      let mut hold = std::i32::MIN;
      let mut not_hold = 0;
    
      for price in prices {
        let mut prev_hold = hold;
        let mut prev_not_hold = not_hold;
        hold = prev_hold.max(prev_not_hold - price);
        not_hold = prev_not_hold.max(price + prev_hold)
      }

      not_hold
  }
}
```

## 100. Same Tree
```rs
use std::cell::RefCell;
use std::rc::Rc;
impl Solution {
    pub fn is_same_tree(
        p: Option<Rc<RefCell<TreeNode>>>,
        q: Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        match (p, q) {
            (None, None) => true,
            (Some(n), None) | (None, Some(n)) => false,
            (Some(ref n), Some(ref m)) => {
                let n = n.borrow();
                let m = m.borrow();
                n.val == m.val
                    && Solution::is_same_tree(n.left.clone(), m.left.clone())
                    && Solution::is_same_tree(n.right.clone(), m.right.clone())
            }
        }
    }
}
```

## 112. Path Sum
```rs
use std::rc::Rc;
use std::cell::RefCell;
impl Solution {
    pub fn has_path_sum(root: Option<Rc<RefCell<TreeNode>>>, target_sum: i32) -> bool {
        match root {
          None => false,
          Some(ref n) => {
            let n = n.borrow();
            let val = n.val;
            if val == target_sum && n.left == None && n.right == None {
              return true;
            }
            Solution::has_path_sum(n.left.clone(), target_sum - val) || Solution::has_path_sum(n.right.clone(), target_sum - val)
          }
        }
    }
}
```

## 70. Climbing Stairs
```rs
impl Solution {
    pub fn climb_stairs(n: i32) -> i32 {
        let mut prev = 0;
        let mut cur = 1;
        let mut i = 1;
        while i <= n {
            let temp = cur;
            cur += prev;
            prev = temp;
            i += 1;
        }
        cur
    }
}
```

## 198. House Robber
```rs
impl Solution {
  pub fn rob(nums: Vec<i32>) -> i32 {
      let len = nums.len();
      if len == 0 {
        return 0;
      }
      let mut dp = Vec::<i32>::with_capacity(len);
      dp.push(nums[0]);
      Solution::trade(nums, len, &mut dp);
      dp[len - 1]
  }

  fn trade(nums: Vec<i32>, len: usize, dp: &mut Vec<i32>) {
    let mut i = 1;
    while i < len {
      match i == 1 {
        true => dp.push(nums[0].max(nums[1])),
        false => dp.push((nums[i] + dp[i - 2]).max(dp[i - 1])),
      };
      i += 1;
    }
  }
}
```

## 199. Binary Tree Right Side View
```cpp
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> ans;
        if (!root) return ans;
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            int size = q.size();
            while (size > 0) {
                TreeNode* cur = q.front();
                q.pop();
                if (cur->left) q.push(cur->left);
                if (cur ->right) q.push(cur->right);
                if (size == 1) ans.push_back(cur->val);
                size--;
            }
        }
        return ans;
    }
};
```

## 88. Merge Sorted Array
```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int len = m + n - 1;
        int i = m - 1;
        int j = n - 1;
        while (i >= 0 && j >= 0) {
            nums1[len--] = nums1[i] > nums2[j] ? nums1[i--] : nums2[j--];
        }
        while (j >= 0) {
            nums1[len--] = nums2[j--];
        }
    }
};
```

## 139. Word Break
```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        if (s.size() == 0) return false;
        unordered_set<string> set(wordDict.begin(), wordDict.end());
        vector<bool> dp(s.size() + 1, false);
        dp[0] = true;
        for (int i = 1; i <= s.size(); i++) {
            for (int j = i - 1; j >= 0; j--) {
                if (dp[j]) {
                    string cur = s.substr(j, i - j);
                    if (set.find(cur) != set.end()) {
                        dp[i] = true;
                        break;
                    }
                }
            }
        }
        return dp[s.size()];
    }
};
```

## 27. Remove Element
```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int idx = 0;
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] != val) {
                nums[idx++] = nums[i];
            }
        }
        return idx;
    }
};
```

## 322. Coin Change
```rs
impl Solution {
    pub fn coin_change(coins: Vec<i32>, amount: i32) -> i32 {
        let mut dp = vec![-1; (amount + 1) as usize];
        dp[0] = 0;
        for coin in coins {
            let mut i = coin as usize;
            while i < dp.len() {
                let diff = i - coin as usize;

                if dp[diff] == -1 {
                    i += 1;
                    continue;
                }
                dp[i] = match dp[i] == -1 {
                    true => dp[diff] + 1,
                    false => dp[i].min(dp[diff] + 1),
                };

                i += 1;
            }
        }
        dp[dp.len() - 1]
    }
}
```

## 55. Jump Game
```rs
impl Solution {
  pub fn can_jump(nums: Vec<i32>) -> bool {
      if nums.len() == 0 {
        return false;
      }

      let mut dp = vec![-1; nums.len()];
      
      dp[0] = nums[0];

      let mut i = 1;

      while i < dp.len() {
        if dp[i - 1] > 0 {
          dp[i] = (dp[i - 1] - 1).max(nums[i]);
        } 
        i += 1;
      }

      dp[dp.len() - 1] != -1
  }
}
```
贪心
```rs
impl Solution {
  pub fn can_jump(nums: Vec<i32>) -> bool {
      if nums.len() == 0 {
        return false;
      }
      let mut i = 0;
      let mut cur = 0;
      while i < nums.len() {
        if cur < i {
          return false;
        }
        cur = cur.max(i + nums[i] as usize);
        i += 1;
      }
      true
  }
}
```

## 45. Jump Game II
```rs
impl Solution {
  pub fn jump(nums: Vec<i32>) -> i32 {
      let (mut cur, mut next, mut ans, mut i) = (0, 0, 0, 0);
      if nums.len() < 2 {
        return ans
      } 
      while i < nums.len() {
        next = next.max(nums[i] as usize + i);
        if i == cur {
          ans += 1;
          if next >= nums.len() - 1 {
            break;
          } else {
            cur = next;
          }
        }
        i += 1;
      }
      ans
  }
}
```

## 101. Symmetric Tree
```rs
use std::rc::Rc;
use std::cell::RefCell;
impl Solution {
    pub fn is_symmetric(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        match root {
          None => true,
          Some(ref root) => {
            let root = root.borrow();
            Solution::is_same(root.left.clone(), root.right.clone())
          }    
        }
    }
    fn is_same(left: Option<Rc<RefCell<TreeNode>>>, right: Option<Rc<RefCell<TreeNode>>>) -> bool {
      match (left, right) {
        (None, None) => true,
        (Some(_), None) | (None, Some(_)) => false,
        (Some(l), Some(r)) => {
          let l = l.borrow();
          let r = r.borrow();
          l.val == r.val && Solution::is_same(l.left.clone(), r.right.clone()) && Solution::is_same(l.right.clone(), r.left.clone())
        }
      }
    }
}
```

## 226. Invert Binary Tree
```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == NULL) return NULL;
        TreeNode* temp = root->left;
        root->left = invertTree(root->right);
        root->right = invertTree(temp);
        return root;
    }
};
```

## 300. Longest Increasing Subsequence
```rs
impl Solution {
  pub fn length_of_lis(nums: Vec<i32>) -> i32 {
      let len = nums.len();
      let mut lis = vec![0; len];
      let mut size: usize = 0;
      for num in nums {
        let mut l = 0;
        let mut r = size;
        while l < r {
          let mut mid = l + (r - l) / 2;
          if lis[mid] < num {
            l = mid + 1;
          } else {
            r = mid;
          }
        }
        lis[l] = num;
        if l == size {
          size += 1;
        }
      }
      size as i32
  }
}
```

## 108. Convert Sorted Array to Binary Search Tree
```rs
use std::rc::Rc;
use std::cell::RefCell;
impl Solution {
    pub fn sorted_array_to_bst(nums: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        Solution::build_tree(&nums, 0, nums.len() - 1)
    }
    fn build_tree(nums: &Vec<i32>, left: usize, right: usize) -> Option<Rc<RefCell<TreeNode>>> {
      if nums.len() == 0 || left > right {
        return None;
      }
      let mid = left + (right - left) / 2;
      let mut node = TreeNode::new(nums[mid]);
      if mid >= 1 {
        node.left = Solution::build_tree(nums, left, mid - 1);
      }
      node.right = Solution::build_tree(nums, mid + 1, right);
      Some(Rc::new(RefCell::new(node)))
    }
}
```