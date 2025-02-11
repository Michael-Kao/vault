---
title: Leetcode noob
tags: [Leetcode]

---

# Leetcode noob
[TOC]
## Array
### 1.Two Sum
:::info
提供一個target值，若值為vector內的兩個數相加則回傳兩個數的位置。(同個位置的值只能使用一次)
:::
+ 第一遍
```cpp=
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for(int i = 0; i < nums.size(); i++)
        {
            for(int j = i + 1; j < nums.size(); j++)
            {
                if(nums[i] + nums[j] == target)
                    return {i, j};
            }
        }
        return {0};
    }
};
```
    純粹暴力破解，省了少量記憶體但效率極差。
+ Time complexity : O(n^2)  
+ 問題 : 如何提升效率  
+ 題目解析 : 快速查找Array的值

## 基礎知識補充
### STL map
+ STL中的其中一個container
+ 擁有兩個值，第一個稱做key，第二個稱做value，其中key只能出現一次且不能更改，只有value的值能修改。
+ 有one-to-one的性質(身分證對應本人)
+ 內部資料結構為紅黑數(red-black tree)，意即:
    + 內部資料有排序
    + 查找和插入操作友善(O(logn))
+ 宣告方式:
    ```cpp=
    map<int, string> mapStudent;
    ```
+ 新增資料的方法:
    + 第一種:用insert插入pair
    ```cpp=
    map<int, string> mapStudent;
    mapStudent.insert(pair<int, string>(1, "student_one"));
    ```
    + 第二種:用insert插入value_type
    ```cpp=
    map<int, string> mapStudent;
    mapStudent.insert(map<int, string>::value_type(1, "student_one"));
    ```
    + 第三種:用array方式插入
    ```cpp=
    map<int, string> mapStudent;
    mapStudent[1] = "student_one";
    mapStudent[2] = "student_two";
    ```
+ 查看map的大小:
    ```cpp=
    int nSize = mapStudent.size();
    ```
+ 資料的Traversal:
    + 第一種:利用迭代器
    ```cpp=
    map<int, string>::iterator iter;
    for(iter = mapStudent.begin(); iter!= mapStudent.end(); iter++)
    {
        cout << iter -> first << " " << iter -> second << endl;
    }
    ```
    + 第二種:利用反向迭代器
    ```cpp=
    map<int, string>::reverse_iterator iter;
    for(iter = mapStudent.rbegin(); iter != mapStudent.rend(); iter++)
    {
        cout << iter -> first << " " << iter -> second << endl;
    }
    ```
    + 第三種:利用array的方式
    ```cpp=
    int nSize = mapStudent.size();
    for(int nIndex = i; nIndex <= nSize; nIndex++)
    {
        cout << mapStudent[nIndex] << endl;
    }
    ```
+ 資料的查找:
    + 第一種:用count函式判斷key有無出現(無法定位key的位置)
    + 第二種:用find函式(回傳迭代器)  
        如果查找的值不在map中，則回傳值=map.end();  
    ```cpp=
    int main()
    {
        map<int, string> mapStudent;
        mapStudent.insert(pair<int, string>(1, "student_one"));
        mapStudent.insert(pair<int, string>(2, "student_two"));
        mapStudent.insert(pair<int, string>(3, "student_three"));
        map<int, string>::iterator iter;
        iter = mapStudent.find(1);
        if(iter != mapStudent.end())
        {
            cout << "Found, the value is " << iter -> second << endl; 
        }else
        {
            cout << "Not found" << endl;
        }
    }
    ```
    + 第三種:利用equal_range(k)來判斷有沒有出現。  
        函式回傳一對(pair)迭代器，迭代器代表範圍邊界，範圍為代表容器中所有key值為k的元素，由於map中的key唯一，所以第一個迭代器指向k元素，第二個則指向下一個鍵值，如果找不到k，則兩個迭代器值相同。
    ```cpp=
    pair<map<int, string>::iterator, map<int, string>::iterator> mapPair;
    mapPair = mapStudent.equal_range(2);
    if(mapPair.first->first == mapPair.second->first)
    {
        cout << "Key is not found." << endl;
    }
    ```
+ 資料清空和判斷為空  
    清空使用clear()，判斷為空使用empty()，返回true表示map是空的。
+ 資料的刪除  
    利用erase()刪除
    + 第一種:利用迭代器
    ```cpp=
    iter = mapStudent.find(1);
    mapStudent.erase(iter);
    ```
    + 第二種:利用關鍵字
    ```cpp=
    int boo = mapStudent.erase(1);
    //如果刪除則boo = 1, 否則boo = 0
    ```
    + 第三種:利用迭代器整個刪除
    ```cpp=
    mapStudent.erase(mapStudent.begin(), mapStudent.end());
    ```
+ 有swap, value_comp等，參考:[map-C++](https://cplusplus.com/reference/map/map/)
#### Map之間的差別
> 總共有4種map, 分別是map, hash_map, unordered_map, unordered_set。
+ hash_map, unordered_map
    + 內部皆為hash table
    + unordered_map在C++11引入標準，hash_map則否，建議使用unordered_map。
    + 查詢平均時間是O(1) (hash table特性)
    + unordered代表未排序
+ unordered_map, map
    + map內部為紅黑樹(red-black tree)
    + 紅黑樹保證查詢、插入、刪除都是O(logn)
    + 雖然hash table查詢時間是O(1)，但是unordered_map的查詢時間不一定比map的短，需要考慮到數量，應針對情況分析決定哪個好。
+ unordered_map, unordered_set
    + https://blog.csdn.net/qq_30815237/article/details/91047041 待看
:::warning
參考資料
+ https://ithelp.ithome.com.tw/articles/10250990
+ https://www.ewdna.com/2008/11/hashmap.html
+ http://www.360doc.com/content/10/0814/11/2595782_45943931.shtml
+ https://blog.csdn.net/u013195320/article/details/23046305
+ https://mropengate.blogspot.com/2015/12/cc-map-stl.html
:::
        