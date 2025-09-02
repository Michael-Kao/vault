---
tags:
  - LeetCode
---

# [Find the Number of Ways to Place People I](https://leetcode.com/problems/find-the-number-of-ways-to-place-people-i/editorial/)  

+ 日期 : 2025/09/02  

## 問題描述  

給定一組平面座標上的點，任取兩個取成一組 `(A, B)`，  
其中 `A` 的座標位置必須在 `B` 的左側，即 `A.x < B.x`，  
且 `A` 的高度高於或等於 `B` 的高度，即 `A.y >= B.y`，  
當上述條件成立時，可以形成一組矩形(寬可以為 `0`，即直線)，  
問在所有可以形成的矩形中，有多少個 `(A, B)` 組合之中不會包含其他點在矩形內部。  

## 直覺想法  

首先當然是先排序，因為 `A` 必須在 `B` 的左側，所以我們可以先依照 `x` 座標由小到大排序，  
然後再來是解決 `y` 座標的問題，可能的狀況如下：  

1. 等高：`A.y == B.y`  
2. `A` 高於 `B`：`A.y > B.y`  
3. `A` 低於 `B`：`A.y < B.y` (不符合條件)  

其中為了不要有重疊，可以想像，當 `x` 越小，即 `A` 越靠左時，  
如果 `y` 越大，涵蓋的範圍就越大，在這種情況下，你越往左側走，  
`y` 必須越小，這樣才不會有重疊的情況發生。  

## 題解  

```cpp=
class cmp {
public:
    bool operator()(vector<int> &a, vector<int> &b) {
        if(a[0] == b[0])
            return a[1] > b[1];
        return a[0] < b[0];
    }
};
class Solution {
public:
    int numberOfPairs(vector<vector<int>>& points) {
        sort(points.begin(), points.end(), cmp());
        int n = points.size();
        int res = 0;
        for(int i = 1; i < n; ++i) {
            int preMIN = INT_MAX;
            for(int j = i-1; j >= 0; --j) {
                int cur = points[j][1];
                if(cur >= points[i][1] && cur < preMIN) {
                    res += 1;
                    preMIN = cur;
                }
            }
        }
        return res;
    }
};
```

Time Complexity : $O(nlogn + n^2)$  
Space Complexity : $O(1)$  

## 心得  

沒畫圖還是會錯，容易粗心的問題，我真的寫的好嗎...
