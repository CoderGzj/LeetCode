LeetCode算法

# 一、数组
**数组是存放在连续内存空间上的相同类型数据的集合。**
---
## 704.二分查找
[力扣题目链接](https://leetcode.cn/problems/binary-search/)
给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回-1。

示例：
```
输入: nums = [-1,0,3,5,9,12], target = 9     
输出: 4       
解释: 9 出现在 nums 中并且下标为 4    
```
思路：
写二分法，区间的定义一般为两种，左闭右闭即[left, right]，或者左闭右开即[left, right)。
### 解法一
左闭右闭区间
时间复杂度：O(log n)
空间复杂度：O(1)
```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
        while (left <= right) { // 当left==right，区间[left, right]依然有效，所以用 <=
            int middle = left + ((right - left) / 2); //防止溢出
            if (nums[middle] > target) {
                right = middle - 1; // target 在左区间，所以[left, middle - 1]
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，所以[middle + 1, right]
            } else {
                return middle;
            }
        }
        return -1;
    }
};
```
### 解法二
左闭右开区间
时间复杂度：O(log n)
空间复杂度：O(1)
```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size(); //[left,right）
        while (left < right) { //当left==right，区间[left, right）无效，所以用 <
            int middle = (right - left) / 2 + left;
            if (nums[middle] > target) {
                right = middle;
            } else if (nums[middle] < target) {
                left = middle + 1;
            } else {
                return middle;
            }
        }
        return -1;
    }
};
```
## 27.移除元素
[力扣题目链接](https://leetcode.cn/problems/binary-search/)
给你一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，并返回移除后数组的新长度。
不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并原地修改输入数组。
元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

示例: 给定 nums = [3,2,2,3], val = 3, 函数应该返回新的长度 2, 并且nums中的前两个元素均为2。
你不需要考虑数组中超出新长度后面的元素。
### 暴力法
时间复杂度：O(n^2)
空间复杂度：O(1)
```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        //暴力
        int k = nums.size();
        for (int i = 0; i < k; i++) {
            if (nums[i] == val) {
                for (int j = i + 1; j < k; j++) {
                    nums[j - 1] = nums[j];
                }
                i--;  // 因为下标i以后的数值都向前移动了一位，所以i也向前移动一位
                k--;
            }
        }
        return k;
    }
};
```
### 双指针1
双指针法（快慢指针法）： 通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。
时间复杂度：O(n)
空间复杂度：O(1)
```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int slow = 0; //慢指针
        for (int fast = 0; fast < nums.size(); fast++) {
            if (nums[fast] != val) { //快指针寻找新元素
                nums[slow++] = nums[fast]; //慢指针更新
            }    
        }
        return slow; //慢指针即元素个数
    }
};
```
### 双指针2
相向双指针方法，基于元素顺序可以改变的题目描述改变了元素相对位置，确保了移动最少元素
时间复杂度：O(n)
空间复杂度：O(1)
```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int left = 0; //第一个元素
        int right = nums.size() - 1; //最后一个元素
        while (left <= right) {
            while (left <= right && nums[left] != val) { //左边找等于要删除值的下标
                left++;
            } 
            while (left <= right && nums[right] == val) { //右边找不等于val,赋值到前面
                right--;
            }
            if (left < right) { 
                nums[left++] = nums[right--];
            } 
        }
        return left;
    }
};
```
## 977.有序数组的平方
[力扣题目链接](https://leetcode.cn/problems/squares-of-a-sorted-array/)
给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。

示例 ：
输入：nums = [-4,-1,0,3,10]
输出：[0,1,9,16,100]
### 暴力
时间复杂度是 O(n + nlogn)
```c++
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        for (int i = 0; i < nums.size(); i++) {
            nums[i] *= nums[i];
        }
        sort(nums.begin(), nums.end());
        return nums;
    }
};
```
### 双指针
时间复杂度为O(n)
```c++
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int left = 0;
        int right = nums.size() - 1;
        int i = right;
        vector<int> result(nums.size(), 0);
        while (left <= right) {
            if (nums[left] * nums[left] > nums[right] * nums[right]) {
                result[i--] = nums[left] * nums[left];
                left++;
            } 
            else {
                result[i--] = nums[right] * nums[right];
                right--;
            }
        }
        return result;
    }
};
```
