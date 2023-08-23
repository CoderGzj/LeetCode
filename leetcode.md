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
数组原地平方后用STL库函数sort排序。
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
因为数组已经非递减有序，平方后大的数一定在两边，小数在中间，所以双指针分别指向两端，向中间迭代。
时间复杂度为O(n)
```c++
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int left = 0;
        int right = nums.size() - 1;
        int i = right; //新数组由小到大，所以从后--。
        vector<int> result(nums.size(), 0); //vector一个新数组用来接收
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

 ```
 ## 206.反转链表 简单
[力扣题目链接](https://leetcode.cn/problems/reverse-linked-list/)
题意：给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。
示例: 输入: 1->2->3->4->5->NULL 输出: 5->4->3->2->1->NULL
### 迭代
时间复杂度: O(n)
空间复杂度: O(1)
```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* pre = NULL;
        ListNode* cur = head;
        ListNode* temp = NULL;
        while (cur) {
            temp = cur->next; // 保存一下 cur的下一个节点，因为接下来要改变cur->next
            cur->next = pre; // 翻转操作
            pre = cur;
            cur = temp;
        }
        return pre;
    }
};
```
### 递归1
时间复杂度: O(n), 要递归处理链表的每个节点
空间复杂度: O(n), 递归调用了 n 层栈空间
```c++
class Solution {
public:
    ListNode* reverse (ListNode* pre, ListNode* cur) {
        if (cur == NULL) return pre;
        ListNode* temp =cur->next;
        cur->next = pre;
        return reverse(cur, temp);
    }
    ListNode* reverseList(ListNode* head) {
        return reverse(NULL, head);
    }
};
```
### 递归2
时间复杂度: O(n)
空间复杂度: O(n)
```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(!head || !head->next) return head; // 边缘条件判断
        // 递归调用，翻转第二个节点开始往后的链表
        ListNode *last = reverseList(head->next);
        // 翻转头节点与第二个节点的指向
        head->next->next = head;
        // 此时的 head 节点为尾节点，next 需要指向 NULL
        head->next = NULL;
        return last; //返回头节点位置
    }
};
```

## **24.两两交换链表中的节点 中等**
[力扣题目链接](https://leetcode.cn/problems/swap-nodes-in-pairs/)
给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。
你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

设置一个虚拟头结点指向原头结点，方便统一后续操作。cur指针先指向虚拟头结点，后续迭代每次指向要交换的两个节点的前一个节点，以此来操作交换过程，在交换中按特定顺序就只需建立一个临时指针保存后续节点。 最后返回虚拟头结点的next就是真正的头结点。
时间复杂度：O(n)
空间复杂度：O(1)
```c++
class Solution {
public:
    ListNode* swapPairs(ListNode* head) { 
        ListNode* dummyHead = new ListNode(0, head);// 设置一个虚拟头结点指向head
        ListNode* cur = dummyHead;
        while (cur->next && cur->next->next) {
            ListNode* temp = cur->next->next; // 记录临时节点
            cur->next->next = temp->next;
            temp->next = cur->next;
            cur->next = temp;
            cur = cur->next->next; // cur移动两位，准备下一轮交换
        }
        return dummyHead->next;
    }
};
```
## **19.删除链表的倒数第N个节点 中等**
[力扣题目链接](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)
给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。
进阶：你能尝试使用一趟扫描实现吗？
### 双指针
思路：双指针的经典应用，如果要删除倒数第n个节点，让fast移动n步，然后让fast和slow同时移动，
直到fast指向链表末尾。删掉slow所指向的节点就可以了。
时间复杂度: O(n)
空间复杂度: O(1)
```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummyHead = new ListNode(0, head);
        ListNode* fast = head; //fast从head(即dummy->next)开始，slow从dummy开始
        ListNode* slow = dummyHead; //保证了最后slow位于删除元素的前驱节点，方便删除操作
        while (n--) fast = fast->next;
        while (fast) {
            fast = fast->next;
            slow = slow->next;
        }
        ListNode* temp = slow->next;
        slow->next = slow->next->next;
        delete temp;
        return dummyHead->next;
    }
};
```
### 栈
在遍历链表的同时将所有节点依次入栈。根据栈「先进后出」的原则，我们弹出栈的第n个节点就是需要删除的节点，并且目前栈顶的节点就是待删除节点的前驱节点。这样一来，删除操作就变得十分方便了。
时间复杂度: O(n)
空间复杂度: O(n)
```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        stack<ListNode*> stk; //开辟一个栈
        ListNode* dummyHead = new ListNode(0, head); //哑结点，虚拟头结点，方便统一操作
        ListNode* cur = dummyHead;
        while (cur) { //遍历链表并依次入栈
            stk.push(cur);
            cur = cur->next;
        } 
        while (n--) stk.pop(); //弹栈n次
        cur = stk.top(); //栈顶元素为倒数第n个要删除元素的前驱结点。
        cur->next = cur->next->next;
        return dummyHead->next;
    }
};
```
## 面试题 02.07.链表相交
同：160.链表相交
[力扣题目链接](https://leetcode.cn/problems/intersection-of-two-linked-lists-lcci/)
给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 null 。

### 一、双指针
设第一个公共节点为node,headA 的节点数量为a,headB 的节点数量为b,两链表的公共尾部的节点数量为c
则有：
* 头节点 headA 到 node 前，共有 a−c 个节点；
* 头节点 headB 到 node 前，共有 b−c 个节点；
设两个指针，A 指向 headA, B 指向 headB。遍历各自链表后对另一个链表开始遍历。
* 若有公共节点，则同时指向公共节点，因为 a + (b - c) = b + (a - c)。
* 若无公共节点，则遍历完两个链表后同时指向NULL，因为 a + b = b + a。
```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* A = headA, * B = headB;
        while (A != B) {
            A = A == NULL ? headB : A->next;
            B = B == NULL ? headA : B->next; 
        }
        return A;
    }
};
```
### 二、哈希集合
判断两个链表是否相交，可以使用哈希集合存储链表节点。
首先遍历链表 headA，并将每个节点加入哈希集合中。然后遍历链表 headB，对于遍历到的每个节点，判断该节点是否在哈希集合中：
* 如果不在哈希集合中，则继续遍历下一个节点；
* 如果在哈希集合中，则后面的节点都在哈希集合中，即从当前节点开始的所有节点都在两个链表的相交部分，因此在链表 headB 中遍历到的第一个在哈希集合中的节点就是两个链表相交的节点，返回该节点。
* 如果链表 headB 中的所有节点都不在哈希集合中，则两个链表不相交，返回 null。
```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        unordered_set<ListNode *> visited;
        ListNode *cur = headA;
        while (cur) {
            visited.insert(cur);
            cur = cur->next;
        }
        cur = headB;
        while (cur) {
            if (visited.count(cur)) return cur;
            cur = cur->next;
        }
        return NULL;
    }
};
```
## 142.环形链表II 中等
[力扣题目链接](https://leetcode.cn/problems/linked-list-cycle-ii/)
题意：给定一个链表，返回链表开始入环的第一个节点。如果链表无环，则返回 null。
说明：不允许修改给定的链表。

### 双指针（快慢指针）
使用两个指针，fast 与 slow 都位于链表的头部。随后，slow每次向后移动一个位置，而fast向后移动两个位置。如果链表中存在环，则 fast 最终将与 slow 在环中相遇。
当 slow 与 fast相遇时，再使指针指向链表头部；随后和slow每次移动一个位置。最终它们会在入环点相遇。
时间复杂度：O(n)
空间复杂度：O(1)
```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *fast = head, *slow = head;
        while (fast && fast->next) { //fast == NULL，fast->next == NULL 无环
            fast = fast->next->next; //fast每次走两个节点
            slow = slow->next; //slow每次走一个节点
            if (fast == slow) { //首次相遇，说明有环
                fast = head; //fast从头开始，和slow同步每次走一个节点
                while (fast != slow) { //再次相遇时就是入环的位置
                    fast = fast->next;
                    slow = slow->next;
                }
                return fast;
            }
        }
        return NULL;
    }
};
```
### 哈希集表
遍历链表中的每个节点，并将它记录下来；一旦遇到了此前遍历过的节点，就可以判定链表中存在环。
借助哈希表可以很方便地实现。
时间复杂度：O(n)
空间复杂度：O(n)
```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode *> visited;
        while (head) {
            if (visited.count(head)) return head;
            visited.insert(head);
            head = head->next;
        }
        return nullptr;
    }
};
```
