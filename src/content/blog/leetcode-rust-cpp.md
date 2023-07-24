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
     let mut record = vec![0; 128];
     let mut i = 0;
     let bytes = s.as_bytes();
     let mut ans = 0;
     let mut start = 0;
     while i < s.len() {
      let char = bytes[i];
      // record 记录出现的索引
      let last_index_after = record[char as usize];
      // 出现重复字符后就要从重复字符后一位开始计算子串
      start = start.max(last_index_after);
      ans = ans.max(i - start + 1);
      // 保存的是该字符后一位的索引
      record[char as usize] = i + 1;
      i += 1;
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

## 105. Construct Binary Tree from Preorder and Inorder Traversal
```rs
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn build_tree(preorder: Vec<i32>, inorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
      Solution::build(preorder.as_slice(), inorder.as_slice())
    }
    fn build(preorder: &[i32], inorder: &[i32]) -> Option<Rc<RefCell<TreeNode>>> {
      let len = preorder.len();
      if len == 0 {
        return None
      }
      let val = preorder[0];
      let mut node = TreeNode::new(val);

      let in_split = inorder.split(|num| *num == val).collect::<Vec<&[i32]>>();
      // = 很重要避免in_split[0].len() = 0 导致运行出错
      node.right = Solution::build(in_split[0], &preorder[1..=in_split[0].len()]);
      
      node.right = Solution::build(in_split[1], &preorder[in_split[0].len() + 1..]);

      Some(Rc::new(RefCell::new(node)))
    }
}
```

## 106. Construct Binary Tree from Inorder and Postorder Traversal
```rs
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn build_tree(inorder: Vec<i32>, postorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
      Solution::build(inorder.as_slice(), postorder.as_slice())
    }
    fn build(inorder: &[i32], postorder: &[i32]) -> Option<Rc<RefCell<TreeNode>>> {
      let len = postorder.len();
      if len == 0 {
        return None
      }
      let val = postorder[postorder.len() - 1];
      let mut node = TreeNode::new(val);

      let in_split = inorder.split(|num| *num == val).collect::<Vec<&[i32]>>();
      node.left = Solution::build(&in_split[0], &postorder[..in_split[0].len()]);
      node.right = Solution::build(in_split[1], &postorder[in_split[0].len()..postorder.len() - 1]);

      Some(Rc::new(RefCell::new(node)))
    }
}
```

## 209. Minimum Size Subarray Sum
```rs
impl Solution {
    pub fn min_sub_array_len(target: i32, nums: Vec<i32>) -> i32 {
        let mut ans = nums.len() + 1;
        let mut left = 0;
        let mut right = 0;
        let mut sum = 0;
        while right < nums.len() {
            sum += nums[right];
            while left <= right && sum >= target {
                ans = ans.min(right - left + 1);
                sum -= nums[left];
                left += 1;
            }
            right += 1;
        }
        match ans > nums.len() {
          true => 0,
          false => ans as i32,
        }
    }
}

```

## 416. Partition Equal Subset Sum
01 背包
```rs
impl Solution {
    pub fn can_partition(nums: Vec<i32>) -> bool {
        let sum = nums.iter().fold(0 , |acc, cur| acc + cur );
        if sum % 2 == 1 {
            return false;
        }
        let target = sum / 2;
        let mut i = 0;
        let mut j = 0;
        let mut dp = vec![0; (target + 1) as usize];

        while i < nums.len() {
            j = target;
            while j >= nums[i] {
                let diff = (j - nums[i]) as usize;
                let tmp = dp[diff] + nums[i];
                dp[j as usize] = dp[j as usize].max(tmp);
                j -= 1;
            }
            i += 1;
        }

        dp[dp.len() - 1] == target
    }
}
```
```rs
impl Solution {
    pub fn can_partition(nums: Vec<i32>) -> bool {
        let sum = nums.iter().fold(0 , |acc, cur| acc + cur );
        if sum % 2 == 1 {
            return false;
        }
        let target = sum / 2;
        let mut i = 0;
        let mut j = 0;
        let mut dp = vec![false; (target + 1) as usize];
        dp[0] = true;
        while i < nums.len() {
            j = target;
            while j >= nums[i] {
                let diff = (j - nums[i]) as usize;
                dp[j as usize] = dp[j as usize] || dp[diff];
                j -= 1;
            }
            i += 1;
        }

        dp[dp.len() - 1]
    }
}
```

## 1049. Last Stone Weight II
```rs
impl Solution {
    pub fn last_stone_weight_ii(stones: Vec<i32>) -> i32 {
        let sum = stones.iter().fold(0, |acc, cur| acc + cur);
        let target = sum / 2;
        let mut i = 0;
        let mut j = 0;
        let mut dp = vec![0; (target + 1) as usize];

        while i < stones.len() {
            j = target;
            while j >= stones[i] {
                let diff = (j - stones[i]) as usize;
                let tmp = dp[diff] + stones[i];
                dp[j as usize] = dp[j as usize].max(tmp);
                j -= 1;
            }
            i += 1;
        }

        (sum - dp[dp.len() - 1] * 2)
    }
}
```

## 494. Target Sum
01 背包 target_sum需要推导
```rs
impl Solution {
    pub fn find_target_sum_ways(nums: Vec<i32>, target: i32) -> i32 {
        let sum = nums.iter().fold(0, |acc, x| acc + x);
        let temp = target + sum;

        let target_sum = temp / 2;
        let len = nums.len();

        if target > sum || temp % 2 == 1 || target_sum + 1 < 0 {
            return 0;
        }
        
        let mut dp = vec![0; target_sum as usize + 1];
        dp[0] = 1;

        let mut i = 0;
        let mut j = 0;
        while i < len {
            j = target_sum;
            while j >= nums[i] {
                let diff = j - nums[i];
                dp[j as usize] += dp[diff as usize];
                j -= 1;
            }
            i += 1;
        }

        dp[dp.len() - 1]
    }
}
```

## 518. Coin Change II
组合数
- 外层循环 物品个数
- 内层循环 背包容量 从小到大
```rs
impl Solution {
    pub fn change(amount: i32, coins: Vec<i32>) -> i32 {
        let mut dp = vec![0; amount as usize + 1];
        dp[0] = 1;

        let mut i = 0;

        while i < coins.len() {
            let mut j = coins[i];
            while j < amount + 1 {
                let diff = j - coins[i];
                if diff >= 0 {
                    dp[j as usize] += dp[diff as usize];
                }
                j += 1;
            }
            i += 1;
        }

        dp[dp.len() - 1]
    }
}
```

## 377. Combination Sum IV
```rs
impl Solution {
    pub fn combination_sum4(nums: Vec<i32>, target: i32) -> i32 {
        let mut dp = vec![0; target as usize + 1];
        dp[0] = 1;
        let mut i: i32 = 0;
        while (i as usize) < dp.len() {
            let mut j = 0;
            while j < nums.len() {
                let diff = i - nums[j];
                if diff >= 0 {
                    dp[i as usize] += dp[diff as usize];
                }
                j += 1;
            }
            i += 1;
        }

        dp[dp.len() - 1]
    }
}
```

## 160. Intersection of Two Linked Lists
```cpp
class Solution {
public:
    ListNode* getIntersectionNode(ListNode* headA, ListNode* headB) {
        if (headA == nullptr || headB == nullptr) {
            return nullptr;
        }
        ListNode* i = headA;
        ListNode* j = headB;

        while (i != j) {
            i = i == nullptr ? headB : i->next;
            j = j == nullptr ? headA : j->next;
        }

        return i;
    }
};
```

## 39. Combination Sum
```rs
impl Solution {
    pub fn combination_sum(candidates: Vec<i32>, target: i32) -> Vec<Vec<i32>> {
        let mut ans = Vec::<Vec<i32>>::new();
        let mut cur = Vec::<i32>::new();
        Solution::get_sum(&candidates, &mut ans, target, 0, &mut cur);
        ans
    }   

    fn get_sum(nums: &Vec<i32>, ans: &mut Vec<Vec<i32>>, target: i32, start: usize, cur: & mut Vec<i32>) {
        if target < 0 {
            return;
        } else if target == 0 {
            let cur = cur.clone();
            ans.push(cur);
        } else {
            let mut i = start;
            while i < nums.len() {
                let n = nums[i];
                cur.push(n);
                Solution::get_sum(nums, ans, target - n, i, cur);
                cur.pop();
                i += 1;
            }
        }
    }
}
```

## 77. Combinations
```rs
impl Solution {
    pub fn combine(n: i32, k: i32) -> Vec<Vec<i32>> {
        let mut ans = Vec::<Vec<i32>>::new();
        let mut cur = Vec::<i32>::new();
        Solution::get_combine(n + 1, 1, k, &mut ans, & mut cur);
        ans
    }
    fn get_combine(n: i32, start: usize, k: i32, ans: &mut Vec<Vec<i32>>, cur: &mut Vec<i32>) {
        if k == 0 {
            let cur = cur.clone();
            ans.push(cur);
        } else {
            for i in start..(n - k + 1) as usize {
                cur.push(i as i32);
                Solution::get_combine(n, i + 1, k - 1, ans, cur);
                cur.pop();
            }
        }
    }
}
```

## 113. Path Sum II
```cpp
class Solution {
public:
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        vector<vector<int>> ans;
        vector<int> cur;
        travel(root, targetSum, ans, cur);
        return ans;
    }
private:
    void travel(TreeNode* root, int target, vector<vector<int>> &ans, vector<int> &cur) {
        if (root == NULL) return;
        target -= root->val;
        cur.push_back(root->val);
        if (target == 0 && isLeaf(root)) {
            ans.push_back(cur);
        }
        travel(root->left, target, ans, cur);
        travel(root->right, target, ans, cur);
        cur.pop_back();
    }

    bool isLeaf(TreeNode* root) {
        if (root == NULL) return false;
        return root->left == NULL && root->right == NULL;
    }
};
```
## 11. Container With Most Water
```rs
impl Solution {
  pub fn max_area(height: Vec<i32>) -> i32 {
      let mut ans = 0;
      let mut l = 0;
      let mut r = height.len() - 1;
      while l < r {
        let cur_area = (height[l].min(height[r])) as usize * (r - l);
        ans = ans.max(cur_area);
        if height[l] < height[r] {
          l += 1;
        } else {
          r -= 1;
        }
      }
      ans as i32
  }
}
```

## 42. Trapping Rain Water
```rs
impl Solution {
  pub fn trap(height: Vec<i32>) -> i32 {
      let len = height.len();
      if len < 2 {
        return  0;
      }
      let mut left = 0;
      let mut right = len - 1;
      let mut ans = 0;
      let mut max_left = height[left];
      let mut max_right = height[right];
      
      while left < right {
         if height[left] < height[right] {
          ans += max_left.min(max_right) - height[left];
          left += 1;
          max_left = max_left.max(height[left]); 
         } else {
          ans += max_left.min(max_right) - height[right];
          right -= 1;
          max_right = max_right.max(height[right]);
         } 
      }
      ans
  }
}
```

## 496


## 739. Daily Temperatures
```rs
impl Solution {
  pub fn daily_temperatures(temperatures: Vec<i32>) -> Vec<i32> {
      let len = temperatures.len();
      let mut ans = vec![0; len];
      let mut stack = Vec::<usize>::new();
      stack.push(0);
      let mut i = 0;

      while i < len {
        let temperature = temperatures[i];
        
        while stack.len() > 0 && temperature > temperatures[stack[stack.len() - 1]] {
          // 维护单调递减stack // 75 71 69 72
          match stack.pop() {
            None => unreachable!("stack.len() must > 0"),
            Some(index) => {
              ans[index] = (i - index) as i32;
            }
          }
        }
        stack.push(i);
        i += 1;
      }
      ans
  }
}
```

## 88. Merge Sorted Array
坑点是因为是原地构建数组，所以思路先把后面0变为有序数组，避免从前遍历会覆盖nums1的值。
```rs
impl Solution {
  pub fn merge(nums1: &mut Vec<i32>, m: i32, nums2: &mut Vec<i32>, n: i32) {
    let mut x = m - 1;
    let mut y = n - 1;
    let mut i = m + n - 1;
    while x >= 0 && y >= 0 {
      nums1[i as usize] = nums1[x as usize].max(nums2[y as usize]);
      if nums1[x as usize] > nums2[y as usize] {
        x -= 1;
      } else {
        y -= 1;
      }
      i -= 1;
    }
    while y >= 0 {
      nums1[i as usize] = nums2[y as usize];
      y -= 1;
      i -= 1;
    }
  }
}
```
## 1143. Longest Common Subsequence
坑点是字符相等应该是从y - 1和 x - 1转移
```rs
impl Solution {
  pub fn longest_common_subsequence(text1: String, text2: String) -> i32 {
      let len1 = text1.len();
      let len2 = text2.len();
      let bytes1 = text1.as_bytes();
      let bytes2 = text2.as_bytes();
      let mut dp = vec![vec![0; len2 + 1]; len1 + 1];

      let mut x = 1;
      let mut y = 1;

      while y < len1 + 1{
        while x < len2 + 1 {
          if bytes1[y - 1] == bytes2[x - 1] {
            dp[y][x] = dp[y - 1][x - 1] + 1;
          } else {
            dp[y][x] = dp[y - 1][x].max(dp[y][x - 1]);
          }
          x += 1;
        }
        x = 1;
        y += 1;
      }
      
      dp[len1][len2] as i32
  }
}
```
## 46. Permutations
```rs
impl Solution {
  pub fn permute(nums: Vec<i32>) -> Vec<Vec<i32>> {
      let mut ans = Vec::<Vec<i32>>::new();
      let mut cur = Vec::<i32>::new();
      for num in nums {
        cur.push(num)
      }
      Solution::backtrack(&mut ans, &mut cur, 0);
      ans
  }
  fn backtrack(ans: &mut Vec<Vec<i32>>, cur: &mut Vec<i32>, begin: usize) {
    if begin == cur.len() {
      ans.push(cur.clone());
      return
    }
    let mut i = begin;
    while i < cur.len() {
      Solution::swap(cur, i, begin);
      Solution::backtrack(ans, cur, begin + 1);
      Solution::swap(cur, begin, i);
      i += 1;
    }
  }

  fn swap(cur: &mut Vec<i32>, i: usize, j: usize) {
    let temp = cur[j];
    cur[j] = cur[i];
    cur[i] = temp;
  }
}
```
## 47. Permutations II
```rs
impl Solution {
    pub fn permute_unique(nums: Vec<i32>) -> Vec<Vec<i32>> {
        let mut ans = Vec::<Vec<i32>>::new();
        let mut cur: Vec<i32> = Vec::<i32>::new();
        let mut nums = nums;
        let len = nums.len();
        nums.sort();
        let mut is_used = vec![false; len];
        Solution::backtrack_unique(&mut ans, & nums, &mut cur, &mut is_used);
        ans
    }
    fn backtrack_unique(ans: &mut Vec<Vec<i32>>, nums: & Vec<i32>, cur: &mut Vec<i32>, is_used: &mut Vec<bool>) {
        let len = nums.len();
        if cur.len() == len {
            ans.push(cur.clone());
            return;
        }
        let mut i = 0;
        while i < len {
            if (i > 0 && nums[i] == nums[i - 1] && is_used[i - 1] == false) || is_used[i] == true {
                 i += 1;
                 continue;
            }
            cur.push(nums[i]);
            is_used[i] = true;
            Solution::backtrack_unique(ans, nums, cur, is_used);
            cur.pop();
            is_used[i] = false;
            i += 1;
        }
    }
}
```
## 39. Combination Sum
```rs
impl Solution {
    pub fn combination_sum(candidates: Vec<i32>, target: i32) -> Vec<Vec<i32>> {
        let mut ans = Vec::<Vec<i32>>::new();
        let mut cur = Vec::<i32>::new();
        Solution::backtrack(&mut ans, &mut cur, &candidates, 0, target);
        ans
    }
    fn backtrack(ans: &mut Vec<Vec<i32>>, cur: &mut Vec<i32>, candidates: &Vec<i32>, begin: usize, target: i32) {
        if target < 0 {
            return
        } else if target == 0 {
            ans.push(cur.clone());
            return
        } else {
            let mut i = begin;
            while i < candidates.len() {
                let val = candidates[i];
                cur.push(val);
                Solution::backtrack(ans, cur, candidates, i,target - val);
                cur.pop();
                i += 1;
            }
        }
    }
}
```

## 40. Combination Sum II
```rs
impl Solution {
    pub fn combination_sum2(candidates: Vec<i32>, target: i32) -> Vec<Vec<i32>> {
        let mut ans = Vec::<Vec<i32>>::new();
        let mut cur = Vec::<i32>::new();
        let mut candidates = candidates;
        candidates.sort();
        let mut is_used = vec![false; candidates.len()];
        Solution::backtrack(&mut ans, &mut cur, &candidates, &mut is_used, 0, target);
        ans
    }
    fn backtrack(ans: &mut Vec<Vec<i32>>, cur: &mut Vec<i32>, candidates: &Vec<i32>, is_used: &mut Vec<bool>, begin: usize, target: i32) {
        if target < 0 {
            return
        } else if target == 0 {
            ans.push(cur.clone());
            return
        } else {
            let mut i = begin;
            while i < candidates.len() {
                if (i > 0 && candidates[i] == candidates[i - 1] && is_used[i - 1] == false) || is_used[i] == true {
                    i += 1;
                    continue
                }
                let val = candidates[i];
                cur.push(val);
                is_used[i] = true;
                Solution::backtrack(ans, cur, candidates, is_used, i + 1, target - val);
                is_used[i] = false;
                cur.pop();
                i += 1;
            }
        }
    }
}
```
