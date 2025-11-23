---
tags:
  - LeetCode
---

# [Greatest Sum Divisible by Three](https://leetcode.com/problems/greatest-sum-divisible-by-three/editorial/)  

+ 日期 : 2025/11/23  

## 問題描述  

給定一個數字陣列 `nums`，找出陣列中元素和能被 3 整除的最大和。  

## 直覺想法  

1. 計算陣列中所有元素的總和 `total`  
2. 若 `total` 不被 `3` 整除，則情況如下  
3. 若 `total % 3 == 1`，則移除陣列中 `num % 3 == 1` 的最小值，或移除兩個 `num % 3 == 2` 的最小值  
4. 若 `total % 3 == 2`，則移除陣列中 `num % 3 == 2` 的最小值，或移除兩個 `num % 3 == 1` 的最小值  
5. 返回調整後的 `total`  

## 題解  

```cpp=
class Solution {
public:
    int maxSumDivThree(vector<int>& nums) {
        int res = 0;
        vector<vector<int>> remain(3);
        sort(nums.begin(), nums.end());
        for(int num : nums) {
            res += num;
            int val = num % 3;
            if(val == 1) {
                remain[val].push_back(num);
            } else if(val == 2) {
                remain[val].push_back(num);
            }
        }
        if(res % 3 == 1) {
            int target = remain[1].size() ? remain[1][0] : INT_MAX;
            if(remain[2].size() > 1)
                target = min(target, remain[2][0] + remain[2][1]);
            res -= target;
        } else if(res % 3 == 2) {
            int target = remain[2].size() ? remain[2][0] : INT_MAX;
            if(remain[1].size() > 1)
                target = min(target, remain[1][0] + remain[1][1]);
            res -= target;
        }
        return res;
    }
};
```

Time Complexity : $O(nlogn)$  
Space Complexity : $O(n)$  

## 心得  

又是不看 `Hint` 不會寫的題目QQ  
