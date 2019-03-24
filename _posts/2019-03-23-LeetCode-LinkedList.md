---
layout: post
title: "LeetCode::SinglyLinkedList"
date: 2019-03-23
mathjax: true
categories: Algorithm
---
# Introduction
Conceptually, most SLL algorithms are very similar to those applied on arrays. The defining difference is that when modifying a node, often times **you also need to have reference to the node just before or after it**. Take for example a simple example of deleting the node ``B`` from list ``A->B->C``. After you identify where ``B`` is, you still need to keep a reference of ``A`` in order to write ``A->next = B->next``. 

A slightly more challenging example is to reverse the same list. Not only do you need to know where ``A`` and ``B`` to write ``B->next = A``, you also need to know where ``C`` is in order to continue traversing the list, after you've altered the original ``B->next``.

This immediately brings code complexity, as you need to take care of more variables and treat special cases carefully. The head node obviously has no previous node, and the ``NULL`` node has no node after it. Identifying what variables to keep, how to update them in a loop, and when to stop are keys to solve SLL problems.

# Tricks 

## 206. Reverse Linked List
# Description & Example
Reverse a singly linked list.

Example:

Input:  ``1->2->3->4->5->NULL``

Output: ``5->4->3->2->1->NULL``

# Solution 1
The idea is straightfoward in Python:
{% highlight python %}
def reverseList(head):
    pre, cur = None, head
    while cur:
        cur.next, pre, cur = pre, cur, cur.next
    return pre
{% endhighlight %}

In languages that doesn't support such syntax, an extra variable is used to store the information lost when reversing a link.

{% highlight C %}
struct ListNode* reverseList(struct ListNode* head) {
    if (!head)
        return head;
    struct ListNode* prev = NULL;
    struct ListNode* cur = head;

    
    while (cur){
        struct ListNode* temp = cur->next;
        cur->next = prev;
        prev = cur;
        cur = temp;
    }
    return prev;
}
{% endhighlight %}

There are methods to use less than 3 variables, but space efficiency is not enough to justify the lost code readbility IMO, so I will not cover them here. 

# Solution 2
We can also solve this recursively. 

{% highlight C %}
struct ListNode* reverseList(struct ListNode* head) {
    if (head == NULL || head->next == NULL){
        return head;
    }
    
    struct ListNode* tail = reverseList(head->next);
    (head->next)->next = head;
    head->next = NULL;
    return tail;
}
{% endhighlight %}

After the first recursive call, we have:
{% highlight python %}
1->2<-3<-4<-5
   ↓
  NULL 
{% endhighlight %}
while ``tail`` is at 5 and ``head`` still at 1. We fix the ``1->2`` link with ``(head->next)->next = head`` and add ``1->NULL``.

## 21. Merge Two Sorted Lists
# Description & Example
Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

Example:

Input: ``1->2->4->NULL``, ``1->3->4->NULL``

Output: ``1->1-2->3->4->4->NULL``

# Solution 1

{% highlight c %}
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2) {    
    struct ListNode dummy = {0, NULL};
    struct ListNode* cur = &dummy;
        

    while(l1 && l2){
        if (l1->val <= l2->val){
            cur->next = l1;
            l1 = l1->next;
        }
        else{
            cur->next = l2;
            l2 = l2->next;
        }
        cur = cur->next;
    }
    if (!l1){
        cur->next = l2;
    }
    if (!l2){
        cur->next = l1;
    }
    return dummy.next;
}
{% endhighlight %}

# Solution 2
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
