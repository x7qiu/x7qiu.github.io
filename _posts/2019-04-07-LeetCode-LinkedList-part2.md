---
layout: post
title: "LeetCode::LinkedList(Part2)"
date: 2019-04-24
mathjax: true
categories: Algorithm
---
# Introduction
Most linked list algorithms are conceptually quite straightforward. The main difficult lies in that fact that when modifying a node in the list, often times you also **need to have reference to the node just before or after it**. Take for example a simple case of deleting the node ``B`` from list ``A->B->C``. After you identify where ``B`` is, you still need to keep a reference of ``A`` in order to write ``A->next = B->next``. 

This brings code complexity, as you need to keep track of more variables and treat special cases carefully. The head node obviously has no previous node and the ``NULL`` node has no node after it. I usually approach a problem in the following steps:

1. **Design the inside of ``while`` loop**

    In this step we ignore edge cases and focus on how to modify the body of the list to get the desired result.

2. **Decide the break point for the loop**

    This will usually be when our ``cur`` node reaches ``NULL``, but exceptions exsist. For example, if we referenced the value of ``cur->next`` node in the loop, since a ``NULL`` node has no value filed, we must break out of the loop when ``cur->next`` is ``NULL``.

3. **Handle edge cases**

    The ``head`` node usually needs to be handled seperately. 

## 206. Reverse Linked List
# Description & Example
Reverse a singly linked list.

Example:

Input:  ``1->2->3->4->5->NULL``

Output: ``5->4->3->2->1->NULL``

# Solution 1 (Iterative)
Let's do the aforementioned steps:

1. **Design the inside of while loop**

    Image somewhere in the middle of the list we need to reverse ``B->C->D->E->...``. At this point we must already have a ``cur`` variable(used to traverse the list) and is pointing at ``B``. It is obvious that we need another variable, ``nex``, as the next element of ``cur`` so that we can reverse the ``B->C`` part with ``nex->next = cur``.  

    Continue to loop and things become interesting. What we want now is to let ``cur`` point at C and ``nex`` point at D. But what cannot get a reference to ``D`` anymore because ``C->next`` is now pointing at ``B``. To solve this, we need an extra variable in the loop to store such information.

    So the main loop should resemble something like this:
```c
    struct ListNode* cur = head;
    struct ListNode* next = head->next;

    while (...){     // decide in step 2
        struct ListNode* temp = nex->next;
        nex->next = cur;                    // reverse 

        cur = nex;
        nex = temp;
    }   
```

2. **Decide the break point for the loop**

   Notice that we referenced ``nex->next`` inside our loop. In order for this to be valid, ``nex`` cannot be ``NULL``. So the break condition for the loop should be ``while(nex!=NULL)``, or simply ``while(nex)``.

3. **Handle edge cases**

    As usual, the edge case that requires special treatment here is the original ``head`` node. There are two parts we need to consider here:
    1. At the beginning

        If the ``head`` node is is passed in as ``NULL``, then we cannot have a  ``nex`` node and can simply return ``NULL``.

    2. At the end

        After traversing the list and reversing each nodes along the way, we need ``head->next = NULL``.

The complete solution:
{% highlight C %}
struct ListNode* reverseList(struct ListNode* head) {
    if (!head)
        return NULL;
    struct ListNode* cur = head;
    struct ListNode* nex = head->next;
    
    while(nex){
        struct ListNode* temp = nex->next;
        nex->next = cur;
        
        cur = nex;
        nex = temp;
    }
    head->next = NULL;
    return cur;
}{% endhighlight %}
 

# Solution 2 (Iterative)
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

# Solution 3 (Recursive)
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

## 83. Remove Duplicates from Sorted List
# Description & Example
Given a sorted linked list, delete all duplicates such that each element appear only once.

Example:

Input: ``1->1->2->3->3``

Output: ``1->2->3``

# Solution 1 (Iterative)
{% highlight c %}
ListNode* deleteDuplicates(ListNode* head) {
    ListNode *cur = head;
    while (cur && cur->next) {
        if (cur->val == cur->next->val)
            cur->next = cur->next->next;
        else
            cur = cur->next;
    }
    return head;
}
{% endhighlight %}
# Solution 2 (Recursive)
{% highlight c %}
struct ListNode* deleteDuplicates(struct ListNode* head) {
    if (head == NULL || head->next == NULL)
        return head;
    
    struct ListNode* rest = deleteDuplicates(head->next);
    if (head->val == rest->val){
        // return rest;
        head->next = rest->next;
        return head;
    }
    else
        return head;
}
{% endhighlight %}

## 21. Merge Two Sorted Lists
# Description & Example
Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

Example:

Input: ``1->2->4->NULL``, ``1->3->4->NULL``

Output: ``1->1-2->3->4->4->NULL``

# Solution 1 (Iterative)

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
