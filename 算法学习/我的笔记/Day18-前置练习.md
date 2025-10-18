# Day18 - 链表前置练习

## 练习目的

为了更好地完成 **LeetCode 143 - 重排链表**，先练习三个基础技巧：
1. 快慢指针找中间节点
2. 反转链表（头插法）
3. 合并两个有序链表

---

## 1. 快慢指针找中间节点（LeetCode 876）⭐

**链接：** https://leetcode.cn/problems/middle-of-the-linked-list/

**题目描述：**
给你单链表的头结点 `head`，请你找出并返回链表的中间结点。如果有两个中间结点，则返回第二个中间结点。

**示例：**
```
输入：head = [1,2,3,4,5]
输出：[3,4,5]

输入：head = [1,2,3,4,5,6]
输出：[4,5,6]
```

### 我的代码

```cpp
class Solution {
public:
    ListNode* middleNode(ListNode* head) {
        // 快慢指针，slow跑一格，fast跑两格
        ListNode* slow = head, *fast = head;
        while(fast && fast->next)
        {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }
};
```

### 提交结果

- **状态：** ✅ Accepted
- **用时：** 3分钟
- **时间复杂度：** O(n)
- **空间复杂度：** O(1)

### 核心理解

**1. 为什么循环条件是 `fast && fast->next`？**

- `fast` 每次走两步，需要确保当前节点和下一个节点都存在
- 奇数个节点：`fast` 最终停在最后一个节点，`fast->next` 为 `nullptr`
- 偶数个节点：`fast` 最终为 `nullptr`

**2. slow 的落点：**

```
奇数个节点（5个）：
1 -> 2 -> 3 -> 4 -> 5
初始：slow=1, fast=1
第1次：slow=2, fast=3
第2次：slow=3, fast=5
第3次：fast->next=null，退出循环
结果：slow停在3（中间节点）

偶数个节点（6个）：
1 -> 2 -> 3 -> 4 -> 5 -> 6
初始：slow=1, fast=1
第1次：slow=2, fast=3
第2次：slow=3, fast=5
第3次：slow=4, fast=null，退出循环
结果：slow停在4（第二个中间节点）
```

---

## 2. 反转链表（LeetCode 206）⭐

**链接：** https://leetcode.cn/problems/reverse-linked-list/

**题目描述：**
给你单链表的头节点 `head`，请你反转链表，并返回反转后的链表。

**示例：**
```
输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]
```

### 我的代码

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
       ListNode* cur = head;
       ListNode* newhead = new ListNode(0);
       while(cur)
       {
        ListNode* next = cur->next;  // 先记录cur->next的值，因为cur要变动，next和cur配合遍历处理完链表
        cur->next = newhead->next;   // cur->next的值被保存后就可以移动了，将cur插入到新链表中，需要更新后指针以及前指针
        newhead->next = cur;
        cur = next;  // 已经插入完了继续处理原链表下一个元素
       } 
       return newhead->next;
    }
};
```

### 提交结果

- **状态：** ✅ Accepted
- **用时：** 25分钟（画图理解）
- **时间复杂度：** O(n)
- **空间复杂度：** O(1)

### 核心理解

**1. 头插法的原理（画图）：**

```
原链表：1 -> 2 -> 3 -> 4 -> 5

初始状态：
newhead -> null
cur = 1

第1次插入：
next = 2（保存）
cur->next = null
newhead -> 1 -> null
cur = 2

第2次插入：
next = 3（保存）
cur->next = 1
newhead -> 2 -> 1 -> null
cur = 3

第3次插入：
next = 4（保存）
cur->next = 2
newhead -> 3 -> 2 -> 1 -> null
cur = 4

...依此类推

最终结果：
newhead -> 5 -> 4 -> 3 -> 2 -> 1 -> null
```

**2. 为什么要先保存 `next`？**

因为 `cur->next` 即将被修改，如果不保存，就无法访问原链表的下一个节点。

**3. 头插法的核心步骤：**
```cpp
ListNode* next = cur->next;    // 1. 保存下一个节点
cur->next = newhead->next;     // 2. 当前节点指向新链表的第一个节点
newhead->next = cur;           // 3. 虚拟头节点指向当前节点
cur = next;                    // 4. 移动到下一个节点
```

---

## 3. 合并两个有序链表（LeetCode 21）⭐

**链接：** https://leetcode.cn/problems/merge-two-sorted-lists/

**题目描述：**
将两个升序链表合并为一个新的升序链表并返回。

**示例：**
```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

### 我的代码

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode* newhead = new ListNode(0);
        ListNode* prev = newhead;
        while(list1 && list2)
        {
            if(list1->val <= list2->val)
            {
                prev->next = list1;
                list1 = list1->next;
            }
            else 
            {
                prev->next = list2;
                list2 = list2->next;
            }
            prev = prev->next;
        }
        prev->next = list1 ? list1 : list2;  // 处理另一链表未处理完结果
        return newhead->next;
    }
};
```

### 提交结果

- **状态：** ✅ Accepted
- **用时：** 10分钟
- **时间复杂度：** O(m + n)
- **空间复杂度：** O(1)

### 核心理解

**1. 合并过程（示例）：**

```
list1: 1 -> 2 -> 4
list2: 1 -> 3 -> 4

初始：newhead -> null, prev = newhead

比较 1 vs 1：
  选 list1 的 1
  newhead -> 1
  list1 = 2, prev = 1

比较 2 vs 1：
  选 list2 的 1
  newhead -> 1 -> 1
  list2 = 3, prev = 1

比较 2 vs 3：
  选 list1 的 2
  newhead -> 1 -> 1 -> 2
  list1 = 4, prev = 2

比较 4 vs 3：
  选 list2 的 3
  newhead -> 1 -> 1 -> 2 -> 3
  list2 = 4, prev = 3

比较 4 vs 4：
  选 list1 的 4
  newhead -> 1 -> 1 -> 2 -> 3 -> 4
  list1 = null, prev = 4

list1为空，退出循环
prev->next = list2（剩余的4）
最终：1 -> 1 -> 2 -> 3 -> 4 -> 4
```

**2. 为什么最后要 `prev->next = list1 ? list1 : list2`？**

- 当一个链表遍历完时，另一个链表可能还有剩余节点
- 剩余节点已经是有序的，直接连接即可
- 用三目运算符判断哪个链表还有节点

**3. 为什么不需要逐个拷贝节点？**

- 直接修改指针，复用原链表的节点
- 不需要创建新节点，空间复杂度 O(1)

---

## 总结

### 完成情况

- ✅ LeetCode 876 - 链表的中间结点（3分钟）
- ✅ LeetCode 206 - 反转链表（25分钟）
- ✅ LeetCode 21 - 合并两个有序链表（10分钟）
- **总用时：** 38分钟

### 三大技巧掌握情况

| 技巧 | 掌握程度 | 关键点 |
|------|---------|--------|
| 快慢指针 | ⭐⭐⭐⭐⭐ | 循环条件：`fast && fast->next` |
| 头插法反转 | ⭐⭐⭐⭐⭐ | 先保存next，再修改指针 |
| 合并链表 | ⭐⭐⭐⭐⭐ | 双指针比较，剩余节点直接连接 |

### 核心收获

1. **画图的重要性：** 反转链表时画图帮助理解，这是处理链表题的关键技巧
2. **虚拟头节点：** 简化边界处理，三题都用到了
3. **指针保存：** 修改指针前先保存，避免丢失引用
4. **循环条件：** 根据需求选择 `&&` 或 `||`

---

## 下一步

✅ **基础已打好！现在可以挑战 LeetCode 143 - 重排链表了！**

这题就是把这三个技巧组合起来：
1. 快慢指针找中间节点
2. 反转后半部分
3. 合并两个链表

**回到 Day18-10.18.md 继续第3题！** 🚀

---

**记录时间：** 2024年10月18日

