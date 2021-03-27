---
layout: post
title: "LeetCode::LinkedList(Part3)-Multiple Lists"
date: 2019-05-01
mathjax: true
categories: Algorithm
---
## Introduction
------


# 21. Merge Two Sorted Lists
Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

Example:

Input: ``1->2->4->NULL``, ``1->3->4->NULL``

Output: ``1->1-2->3->4->4->NULL``

# Solution 1 (Iterative)
{% highlight c %}
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2) {    
    struct ListNode dummy = {0, NULL};
    struct ListNode* pre = &dummy;
        
    while(l1 && l2){
        if (l1->val <= l2->val){
            pre->next = l1;
            l1 = l1->next;
        }
        else{
            pre->next = l2;
            l2 = l2->next;
        }
        pre = pre->next;
    }
    if (!l1){
        pre->next = l2;
    }
    if (!l2){
        pre->next = l1;
    }
    return dummy.next;
}
{% endhighlight %}

# Solution 2 (Recursive)
As is often the case, the recursive solution is much easier to read.

{% highlight c %}
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2) {
    if (!l1){
        return l2;
    }
    if (!l2){
        return l1;
    }
    if (l1->val <= l2->val){
        l1->next = mergeTwoLists(l1->next, l2);
        return l1;
    }
    else{
        l2->next = mergeTwoLists(l1, l2->next);
        return l2;
    }
}
{% endhighlight %}

# 2. Add Two Numbers
You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Example:

Input: ``(2 -> 4 -> 3) + (5 -> 6 -> 4)``

Output: ``7 -> 0 -> 8``

Explanation: 342 + 465 = 807.

# Solution
{% highlight c %}
struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2) {
    struct ListNode dummy = {0, NULL};
    struct ListNode* pre = &dummy;
    int carry = 0;
    while (l1 || l2 || carry){
        if (l1){
            carry += l1->val;
            l1 = l1->next;
        }
        if (l2){
            carry += l2->val;
            l2 = l2->next;
        }
        struct ListNode* cur = malloc(sizeof(struct ListNode));
        cur->val = carry%10;
        cur->next = NULL;           // don't forget 
        pre->next = cur;
        
        carry = carry/10;
        pre= cur;
    }
    return dummy.next;
}
{% endhighlight %}
