---
layout: post
title: "LeetCode Series: Backtracking"
date: 2019-01-27
mathjax: true
categories: Algorithm
---
## Backtracking
# About
Backtracking is a general approach to solving **constraint-satisfaction problems** without trying all possibilities.

It incrementally builds candidates solutions, and abadons a solution("backtracks") as soon as it determines the candidate cannot be valid.

It can be applied only for problems which admit the concept of a "partial candidate solution" and a relatively quick test of whether it can possibly be completed to a valid solution.

It is often realized by recursion(but not necessarily). 

# Description & pseudocode
Think of the search space as a decision tree, where each node represents a **partial candidate solution**, and every possible **decision** from that node leads to a child node. Backtracking traverses the decision tree in a DFS manner, at each node checking to see if it could possibly lead to a valid solution. If not, it discard all children of that node(pruning), and backtracks to the previous node.

There are several incarnations of backtracking algorithms: 
1. to determine if a problem has a solution 
2. to find one solution of a problem
3. to find all solutions of a problem
4. to find the best solution of a problem

1) determine if a solution exists
{% highlight python %}
def Solve(node):
    if reach_leaf_condition():
        return is_solution(node)

    decisions = get_decions(node)       # get available decisons for this node
    for decision in decisions:
        make_decision(node, decision)   # make a decision, update node
        if no_break_constraint(node):   # pruning part   
            if Solve(node):
                return True
        unmake_decision(node, decision) # restore state to beginning of loop
    return False
{% endhighlight %}

2) find one solution or return False if none exists
{% highlight python %}
def Solve(node):
    decisions = get_decions(node)       
    if no decisions:
        if is_solution(node):
            return node
        else:
            return False
    for decision in decisions:
        make_decision(node, decision)   
        if no_break_constraint(node):   
            if Solve(node):
                return Solve(node):
        unmake_decision(node, decision)
    return False
{% endhighlight %}

3) find all solutions
{% highlight python %}
def Solve(node):
    res = []
    solveHelper(node, res)
    return res

def solveHealper(node, res):
    if reach_leaf_condition():
        if is_solution(node):
            res.append(node)

    decisions = get_decions(node)      
    for decision in decisions:
        make_decision(node, decision)   
        if no_break_constraint(node):   
            Solve(node):
        unmake_decision(node, decision)  
{% endhighlight %}

## 46. Permutation
# Description & Example
Given a collection of distinct integers, return all possible permutations.

Example:

{% highlight python %}
Input: [1, 2, 3]

Output:
[
    [1, 2, 3],
    [1, 3, 2],
    [2, 1, 3],
    [2, 3, 1],
    [3, 1, 2],
    [3, 2, 1]
]
{% endhighlight %}

# Initial Analysis
It is clear that we should somehow use recursion. The typical pattern is to either divide and conquer or decrease and conquer. Namely:

    1. Divide or decrease the original problem into subproblem(s)
    2. Solve the subproblem(s) using recursion      # trivial
    3. Somehow put the answers to subproblem(s) together to solve the original problem

After some thinking, it is not difficult to see that for every permutation of length ``n``, if we look past the first element, the remaining part is also a permutation of length ``n-1``. Conversely, if we want to generate a longer permutaion from a short one, we can simply put the missing element at each position of our short permutation.

For example, suppose we have all the permutations of [2, 3], i.e. ``[2, 3] and [3, 2]``, and want to get the permutations of [1, 2, 3]. We can put the number ``1`` on the **1st, 2rd and 3rd** index of ``[2, 3]`` to get ``[1, 2, 3], [2, 1, 3] and [2, 3, 1]``. We can repeat the process on ``[3, 2]`` to get the remaining permutations.

Code:
{% highlight python %}
def permute(nums):
    sols = []
    if len(nums) <= 1:      # base case
        return [nums]
    else:
        first = nums[0]               
        tail_perms = permute(nums[1:])    # all permutations of nums[1:]        
        for perm in tail_perms:
            for i in range(len(perm) + 1):   # insert nums[0] at all indices
                sol = perm[:i] + [first] + perm[i:]
                sols.append(sol)
                return sols
{% endhighlight %}

Runtime:
