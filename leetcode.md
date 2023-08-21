LeetCode算法

# 一、数组
**数组是存放在连续内存空间上的相同类型数据的集合。**
---
## 704.二分查找 简单
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
## 27.移除元素 简单
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
## 977.有序数组的平方 简单
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
## 209.长度最小的子数组 中等
[力扣题目链接](https://leetcode.cn/problems/minimum-size-subarray-sum/)
给定一个含有 n 个正整数的数组和一个正整数 target 。
找出该数组中满足其和 ≥ target 的长度最小的 连续子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。

示例 ：
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
### 滑动窗口
时间复杂度：O(n)
空间复杂度：O(1)
所谓滑动窗口，就是不断的调节子序列的起始位置和终止位置，从而得出我们要想的结果。
分为以下几个步骤：
右滑j，使窗口内和sum>=target
左滑i，保证sum>=target
重复步骤1
```c++
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int sum = 0 ,j = 0, len = 0, i = 0; 
        //sum滑动窗口数值之和,i滑动窗口起始位置,len滑动窗口的长度
        int result = INT32_MAX;
        for (; j < nums.size(); j++) {
            sum += nums[j];
            while (sum >= target) {
                len = j - i + 1;
                result = min(len, result);
                sum -= nums[i++]; //这里体现出滑动窗口的精髓之处，不断变更i（子序列的起始位置）
            }
        }
        return result == INT32_MAX ? 0 : result; 
    }
};
```
## 59.螺旋矩阵II 中等
[力扣题目链接](https://leetcode.cn/problems/spiral-matrix-ii/)
给你一个正整数 n ，生成一个包含 1 到 n^2 所有元素，且元素按顺时针顺序螺旋排列的 n x n 正方形矩阵 matrix 。
### 模拟实现
```c++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) { 
        vector<vector<int>> result(n, vector<int>(n, n * n)); //初始化n*n，不用考虑奇数中间值
        int x = 0, y = 0, i, j;  // x,y 定义每循环一个圈的起始位置
        int loop = n / 2;
        int count = 1, offset = 1;  //count给矩阵赋值,offset控制每一条边遍历的长度
        while (loop--) {
            for (j = y; j < n - offset; j++)  // 模拟填充上行从左到右(左闭右开)
                result[x][j] = count++;
            for (i = x; i < n - offset; i++) // 模拟填充右列从上到下(左闭右开) 
                result[i][j] = count++;
            for (; j > x; j--) // 模拟填充下行从右到左(左闭右开)
                result[i][j] = count++;
            for (; i > y; i--) // 模拟填充左列从下到上(左闭右开)
                result[i][j] = count++;
            x++; y++; offset++;
        }
        return result;
    }
};
```

# 二、链表
## 203.移除链表元素 简单
[力扣题目链接](https://leetcode.cn/problems/remove-linked-list-elements/)
删除链表中等于给定值 val 的所有节点。

示例 1： 输入：head = [1,2,6,3,4,5,6], val = 6 输出：[1,2,3,4,5]
示例 2： 输入：head = [], val = 1 输出：[]
示例 3： 输入：head = [7,7,7,7], val = 7 输出：[]

### 设置一个虚拟头结点再进行移除节点操作
时间复杂度: O(n)
空间复杂度: O(1)
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        ListNode* dummyNode = new ListNode(0); // 设置一个虚拟头结点
        dummyNode->next = head; // 将虚拟头结点指向head，这样方面后面做删除操作
        ListNode* cur = dummyNode;
        while (cur->next != NULL) {
            if (cur->next->val == val) {
                ListNode* tmp = cur->next;
                cur->next = cur->next->next;
                delete tmp;
            } else {
                cur = cur->next;
            }
        }
        head = dummyNode->next;
        delete dummyNode;
        return head;
    }
};
```
## 707.设计链表 中等
[力扣题目链接](https://leetcode.cn/problems/design-linked-list/)
在链表类中实现这些功能：
get(index)：获取链表中第 index 个节点的值。如果索引无效，则返回-1。
addAtHead(val)：在链表的第一个元素之前添加一个值为 val 的节点。插入后，新节点将成为链表的第一个节点。
addAtTail(val)：将值为 val 的节点追加到链表的最后一个元素。
addAtIndex(index,val)：在链表中的第index个节点之前添加值为val的节点。如果 index 等于链表的长度，则该节点将附加到链表的末尾。如果 index 大于链表长度，则不会插入节点。如果index小于0，则在头部插入节点。
deleteAtIndex(index)：如果索引 index 有效，则删除链表中的第 index 个节点。

```c++
class MyLinkedList {
public:
    struct Node { // 定义链表节点结构体
        int val;
        Node* next;
        Node(int val):val(val), next(nullptr){}
    };
    MyLinkedList() { // 初始化链表
        dummyNode = new Node(0); // 这里定义的头结点 是一个虚拟头结点，而不是真正的链表头结点
         _size = 0;
    }
    //获取第index个节点数值，如果index是非法数值返回-1， 注意index是从0开始的，第0个节点就是头结点
    int get(int index) {
        if (index < 0 || index > (_size - 1)) return -1;
        Node* cur = dummyNode->next;
        while (index--) {
            cur = cur->next;
        }
        return cur->val;
    }
    void addAtHead(int val) {  //头插，新插入的节点为链表的新的头结点
        Node* new_node = new Node(val);
        new_node->next = dummyNode->next;
        dummyNode->next = new_node;
        _size++;
    }
    void addAtTail(int val) { // 尾插
        Node* new_node = new Node(val);
        Node* cur = dummyNode;
        while (cur->next != NULL) {
            cur = cur->next;
        }
        cur->next = new_node;
        _size++;
    }
    void addAtIndex(int index, int val) {
        if(index > _size) return;
        if(index < 0) index = 0;  
        Node* new_node = new Node(val);
        Node* cur= dummyNode;
        while (index--) {
            cur = cur->next;
        }
        new_node->next = cur->next;
        cur->next = new_node;
        _size++;
    }
    void deleteAtIndex(int index) {
        if (index >= _size || index < 0) return;
        Node* cur= dummyNode;
        while (index--) {
            cur = cur->next;
        }
        Node* tmp = cur->next;
        cur->next = cur->next->next;
        _size--;
        delete tmp; //回收内存
        tmp = nullptr; //防止野指针
    }
private:
    int _size;
    Node* dummyNode;
};
/**
 * Your MyLinkedList object will be instantiated and called as such:
 * MyLinkedList* obj = new MyLinkedList();
 * int param_1 = obj->get(index);
 * obj->addAtHead(val);
 * obj->addAtTail(val);
 * obj->addAtIndex(index,val);
 * obj->deleteAtIndex(index);
 */
 ```
 