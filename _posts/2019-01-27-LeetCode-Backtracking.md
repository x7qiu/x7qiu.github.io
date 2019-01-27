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
Think of the problem space as a decision tree, where each node represents a **partial candidate solution**, and every possible **decision** from that node leads to a child node. Backtracking traverses the decision tree in a DFS manner, at each node checking to see if it could possibly lead to a valid solution. If not, it discard all children of that node(pruning), and backtracks to the previous node.

There are several incarnations of backtracking algorithms: 
1. to determine if a problem has a solution 
2. to find one solution of a problem
3. to find all solutions of a problem
4. to find the best solution of a problem

1) determine if a solution exists
{% highlight python %}
def Solve(node):
    decisions = get_decions(node)       # get available decisons for this node
    if no decisions:
        return is_solution(node)
    for decision in decisions:
        make_decision(node, decision)   # make a decision, node now becomes one of its children node 
        if no_break_constraint(node):   # pruning part   
            if Solve(node):
                return True
        unmake_decision(node, decision)
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
    results = []
    decisions = get_decions(node)       
    if no decisions:
        if is_solution(node):
            results.append(node)
        else:
            return False
    for decision in decisions:
        make_decision(node, decision)   
        if no_break_constraint(node):   
            if Solve(node):
        unmake_decision(node, decision)
    
{% endhighlight %}





