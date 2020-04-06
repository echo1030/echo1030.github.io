---
layout: post
title: leetcode中链表反转相关问题总结
categories: leetcode
tag: linked_list
description: question and solution when use github page and jeklly to build a blog 
---
链表反转应该是最基础的面试题目之一了吧，记录一下
其实我不太理解，为啥链表反转这个题目竟然还有反人类的递归方法。又难懂，空间复杂度还是O(n),他存在的意义是啥，为了考试么……
leetcode里关于链表反转的题目有很多，先看基础的吧
## 基础链表反转
#### 题目
206. Reverse Linked List
Reverse a singly linked list.

Example:

Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL
Follow up:

A linked list can be reversed either iteratively or recursively. Could you implement both?

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/reverse-linked-list
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

#### 解题思路
我觉得自己还是不太专业，这个题目，在最最开始的时候，我考虑的是当前节点cur和他下个节点cur->next，和下下个节点cur->next->next之间的链接关系的转换
这样导致一个非常麻烦的事情，就是头结点next要单独处理，会让代码很丑。而且我觉得有时候让程序做事情，就是要提取共性，尽量少的处理特殊情况。
后来也是看到了官方解题，还是其他人的代码，发现如果处理pre,cur和next之间的关系，会比较好，而且直接给pre赋值null，就可以解决反转后头结点next值的问题

#### 代码
在写代码之前，需要考虑一些问题
- 题目中没有说head是不是一定不为空，所以一定是要判断head之后才能用。其实如果是在真正开发的时候，对于head这种上游传过来的指针，最好也是判断一下在用，因为感觉不能完全依赖和上游的约定，碰到好多因为没判断指针然后core掉的情况。
- 对于head为空，或者head只有一个节点的情况，可以直接返回。这种其实不做处理的话，下边真正反转的部分，也是可以处理的，但是这是浪费时间啊，最后耗时又会增长，不要！
- 还有就是返回，反转链表，在处理完最后一组节点之后，cur指向空指针，pre在cur的前一个位置，也就是原链表的结尾，正好是反转后链表的头，所以直接返回就好了

```
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* pre = nullptr;
        ListNode* cur = head;
        while (cur){
            ListNode* next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
};
```
## 部分链表反转
#### 题目
92. Reverse Linked List II
Reverse a linked list from position m to n. Do it in one-pass.

Note: 1 ≤ m ≤ n ≤ length of list.

Example:

Input: 1->2->3->4->5->NULL, m = 2, n = 4
Output: 1->4->3->2->5->NULL

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/reverse-linked-list-ii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

#### 解题思路
这个题目，我最开始的时候，是想着找到第m个节点，然后往后反转n个节点。虽然也通过了，但是写这篇blog的时候，就觉得，天呐，我怎么那么笨
现在看来，这个题目感觉就像是高中的数学题，很多个知识点融合在一起，拆分之后每个就都很简单了
1. 找到需要被饭转的头结点和尾节点（快慢指针）
2. 在1的过程中，保存被反转的头结点的上个节点(pre)和尾节点的下个节点(next)
3. 反转需要反转的（基础链表反转）

#### 代码
实现过程中总是需要注意的一个问题是，边界情况
- m = n 或 节点为空，那不需要反转，直接返回
- m = 1 这种情况，pre节点还是空的，是没法法直接赋值的，所以需要一个dummy节点，dummy->next指向head，让pre=dummy，哪怕碰到m=1的情况，也不需要再额外处理了
- 将n的下一个节点存起来，然后让n->next = nullptr，这样可以直接调用反转函数
- reverse函数的tail参数是指针的引用。因为C++传参是值传递，如果不加引用的话，参数中的tail其实是tail_cpy，是一个新的变量，他的值和tail一样。
这样在函数中修改了tail，其实修改的是tail_cpy。tail本身还是nullptr，再使用会有问题。
如果想修改指针指向的地址的值，直接传递指针是没问题的，但是如果要修改指针本身的指向，是不行的。

```
class Solution {
public:
    ListNode* reverse(ListNode* head, ListNode*& tail){
        ListNode* pre = nullptr;
        ListNode* cur = head;
        tail = head;
        while (cur){
            ListNode* next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        // if m == n needn't to reserve
        // if head == null, can't be reserve
        if (m == n || head == nullptr) return head;

        //dummy node can proc situation that m = 1
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        ListNode* pre = dummy;

        //get diff and use fast slow pointer to find the range that should be reserve
        int diff = n - m;
        ListNode* fast = head;
        int index = 0;
        while (fast && index < diff){
            fast = fast->next;
            ++index;
        }
        ListNode* slow = head;
        index = 1;
        while (fast && slow && index < m){
            pre = slow;
            slow = slow->next;
            fast = fast->next;
            ++index;
        }

        //save pre and next pointer
        ListNode* next = fast->next;
        ListNode* tail = nullptr;
        //cut nods which is the tail of should travered list with original list
        fast->next = nullptr;
        pre->next = reverse(slow, tail);
        tail->next = next;
        return dummy->next;
    }
};
```

## 总结
链表反转，感觉只要处理好pre, cur和next的关系，尝试拆分知识点，多注意边界条件，就没啥问题了
基础链表反转，应该作为肌肉记忆，就是无论何时，需要写了，就是一次过的那种