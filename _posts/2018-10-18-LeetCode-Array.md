---
layout: post
title: "LeetCode Series(1) #array"
date: 2018-10-18
mathjax: true
categories: Algorithm
---
## 1. Two Sum
# Description
Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly one** solution, and you may not use the same element twice.

# Example
Given nums = [2, 7, 11, 15], target = 9,

return [0, 1].

# Thghouts
Note that we are asked to return the indices of the numbers, not the values. So we can't just throw everything in a set and use membership checking. A more fitting data structure to use is dictionary, with numbers as dictionary keys and indices as dictionary values.

The most intuitive way to do this is 2-pass: with the first pass we build the dictionary as discussed above and with the second pass we check if the desired value is in the dictionary. However, there is also a 1-pass solution to the problem:

# Solution
{% highlight python %}
def TwoSum(nums, target):
        d = {}
        for (index, val) in enumerate(nums):
            want = target - val
            if want in d:
                return [d[want],index]
            else:
                d[val] = index    
{% endhighlight %}

Runtime: O(n)

Space: O(n)

## 15. Three Sum
# Description
Given an array of n integers, are there elements a, b, c in it such that a + b + c = 0? Find **all unique** triplets in the array which gives the sum of zero.

Note: The solution set must not contain duplicate triplets.

# Example
Given array nums = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]

# Thoughts
**1st Try**

We can modify the solution from the TwoSum problem to return all unique pairs of values that adds up to a certain value. We can then loop through the array and for each value we find a pair of numbers that adds up to the negation of it. 
{% highlight python %}
N, sols = len(nums), []

for i in range(N):
    two_sums = TwoSum(nums[i:], traget = -num):     
    for [num1, num2] in two_sums:
        sol = [num, num1, num2]
        if sol not in sols:
            sols.append(sol)
return sols
{% endhighlight %}

However, there are some challenging aspects about this approach. **First**, while looping, it is difficult/costly to exclude the current element from the array from which to find the two sums. (Think about an example of ``[-1, 2]``, it should return no answer instead of ``[-1, -1, 2]``). **Secondly**, it is difficult to remove duplicates from the solution set. Since the answer we want is List[List[int]], and a list is **unhashable**(cannot be used as a key in a dictionary), the easiest way of ``list(set(solutions))`` does not work here. If we go through each solution and check them indivisually, since there could be ${n \choose 3}$ solutions(think of an array of entirely 0's), we failed to reach a sub $O(n^3)$ algorithm.

**2rd Try**

After some failed experiments, I found a solution posted by user "caikehe" on Leetcode that handles both problems elegantly. The idea is to sort the numbers first, then for each number in the array, use two pointers on the remaining part to efficiently locate a solution and avoid duplicates. 

# Solution
{% highlight python %}
def ThreeSum(nums):
    nums.sort()
    N, sols = len(nums), []
    
    for i in range(N-2):
        if nums[i] > 0:
            break
        if i > 0 and nums[i] == nums[i-1]:
            continue
        start, end = i+1, N-1
        while start < end:
            if (nums[start] + nums[end]) < -nums[i]:
                start += 1
            elif (nums[start] + nums[end]) > -nums[i]:
                end -= 1
            elif (nums[start] + nums[end]) == -nums[i]:
                sol = [nums[i], nums[start], nums[end]]
                while start < end and nums[start] == nums[start+1]:
                    start += 1
                while start < end and nums[end] == nums[end-1]:
                    end -= 1
                sols.append(sol)
                start += 1
                end -= 1
  
    return sols
{% endhighlight %}

Notes:

`` if nums[i] > 0: break`` Since the array is sorted and 3 positive numbers cannot add up to 0

`` if i>0 and nums[i] == nums[i-1]: continue`` To remove duplicate nums[i]

`` while start < end and nums[start] == nums[start+1]: start += 1`` To remove duplicate nums[start]

`` while start < end and nums[end] == nums[end-1]: end -= 1`` To remove duplicate nums[end]

