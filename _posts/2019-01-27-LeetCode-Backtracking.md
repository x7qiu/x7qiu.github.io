---
layout: post
title: "LeetCode::Backtracking::Permutation"
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

**Note**: Often times you can pass the ``decisions`` as a function parameter, and update them after making a choice for each child node instead of computing from scratch. For an example, see the last solution to the Permutation problem below.

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
    res = []                # to save all the solutions
    solveHelper(node, res)  # kickoff function
    return res

def solveHealper(node, res):
    if reach_leaf_condition():
        if is_solution(node):
            res.append(node)    # populate res with valid solutions

    decisions = get_decions(node)      
    for decision in decisions:
        make_decision(node, decision)   
        if no_break_constraint(node):   
            Solve(node):
        unmake_decision(node, decision)  
{% endhighlight %}

**Note**: It's a common trick to use a kickstart function for an extra parameter. Here because we want to save all the solutions, we need our recursive function to somehow remember the state when a solution condition is met. To do so, we give it a ``res`` parameter and only populate it when the desired condition is met.
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

It is not difficult to see that for every **permutation of length n**, if we look past the **first element**, the remaining part is also a **permutation of length (n-1)**. Knowing we can get **ALL** the (n-1)-permutation for free by the power of recursion, we look for ways to rebuild the n-permutations with them. 

The key insight is that we can **insert the first element at all positions of the (n-1)-permutation to get an n-permutation**. For example, suppose we want to get the permutations of [1, 2, 3]. Here the first element is ``1``, and the n-1 permutations are ``[2, 3]`` and ``[3, 2]``. We place ``1`` on all positions of ``[2, 3]``, resulting in ``[1, 2, 3]``, ``[2, 1, 3]`` and ``[2, 3, 1]``. We then repeat the same steps on ``[3, 2]`` to get the rest of the n-permutations. A quick check ensures no repeated answers would be generated from this approach.

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

**Note**: Importantly We don't need the ``unmake_decision()`` step here because slicing creates a new list in Python so the original one is never changed.

Runtime:

Space:

# Second Look
It's interesting that I started working on this problem knowing it's under the "backtracking" category, yet the solution I came with up has nothing to do with it. Just plain old recursion. I couldn't really model the problem in the form of a decision tree untill reading work done by others. The key recursive insight is this: in case of the string "ABCDE", the permutations consists of the following:
    
* The character 'A' followed by all permutations of "BCDE"
* The character 'B' followed by all permutations of "ACDE"
* The character 'C' followed by all permutations of "ABDE"
* The character 'D' followed by all permutations of "ABCE"
* The character 'E' followed by all permutations of "ABCD"

As the recursion proceeds, the number of prefix characters increases, and the length of following permutations decrease. Logically we can treat the prefix as decisions we've alread made so far (initially empty), and the rest as candidate decisions (initially the entire string/numbers to be permutated). Notice however that this problem takes slightly different arguments compared to the original problem. As always, we use a wrapper function to make it consistent, which is convinient since we will need one for saving all the solutions anyways.

{% highlight python %}
def permute(nums):
    ans = []
    permuteHelper([], nums, ans)
    return ans
    
def permuteHelper(sofar, rest, ans):
    if rest == []:
        ans.append(sofar)
    for i in range(len(rest)):
        permuteHelper(sofar+[rest[i]], rest[:i]+rest[i+1:], ans)
{% endhighlight %}

![permutation of 1,2,3](/assets/permutation.png)

Runtime:

Space:

# Third Look
It turns out there are many more interesting ways to generate permutations, many of them beyond the scope of this post. I will however cover another one because I find the idea extremely elegant. 

{% highlight python %}
def permute(nums):
    ans = []
    permuteHelper(nums, 0, ans)
    return ans

def permuteHelper(nums, k, ans):
    if k == len(nums):
        ans.append(nums[:])     # important
        return 
    for i in range(k, len(nums)):
        nums[k], nums[i] = nums[i] ,nums[k]
        permuteHelper(nums, k+1, ans)
        nums[k], nums[i] = nums[i], nums[k]
{% endhighlight %}

It took me a while to realize that this solution does exactly the same thing, but in place. It uses ``k`` as a seperator, such that ``num[:k]`` corresponds to the ``sofar`` set and ``nums[k:]`` corresponds to the ``rest`` set. In making a choice, say nums[i], we 
1. make sure $ i \ge k $ courtesy of ``for i in range(k, len(nums))`` 
2. transfer it from ``rest`` to ``sofar`` by ``nums[k], nums[i] = nums[i], nums[k]``
3. increase ``k`` by 1 to indicate that we've made a choice
4. check if ``k`` reaches the end of array, this is the same as checking ``rest == []``
5. unmake any change from step3, since this time we are not creating a new list

Only works on mutable data .

# reference:

https://web.stanford.edu/class/archive/cs/cs106b/cs106b.1188/lectures/Lecture11/Lecture11.pdf

## 77. Combinations
# Description & Example
Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.

Example:

{% highlight python %}
Input: n = 4, k = 2

Output:
[
    [1, 2],
    [1, 3],
    [1, 4],
    [2, 3],
    [2, 4],
    [3, 4]
]
{% endhighlight %}

# Initial Analysis
It's natural to modify the backtracking algorithm for permutations to obtain combinations. The only care we need is not to generate repeated answers, such as ``[2, 4]`` and ``[4, 2]``. My initial thought is to keep each solution a strictly increasing list of numbers, which is trivial and easy to understnad. But after some time I realized it's more elegant and efficient to handle such cases in the ``making decision`` phase. 

To illustrate the idea, suppose we have the numbers ``[1, 2, 3, 4]``. Back in our permutation algorithm, we choose the first number to be ``1`` and recursively generate all the permutation of ``[2, 3, 4]`` to append after it. Then we move on to choose ``2`` as the first number followed by all permutations of ``[1, 3, 4]``. 

{% highlight python %}
def combine(n, k):
    res = [ ]
    comb([ ], list(range(1, n+1)), k, res)
    return res
    
def comb(sofar, rest, k, res):
    if len(sofar) == k:
       res.append(sofar)
    else:
        for i in range(len(rest)):
            comb(sofar + [rest[i]], rest[i+1:], k, res)
{% endhighlight %}
