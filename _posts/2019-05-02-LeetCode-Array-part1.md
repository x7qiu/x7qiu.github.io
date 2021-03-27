---
layout: post
title: "LeetCode::Array(Part1)"
date: 2019-05-02
mathjax: true
categories: Algorithm
---
# 27. Remove Element
Given an array nums and a value ``val``, remove all instances of that value in-place and return the new length.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.

# Solution 1
{% highlight c %}
int removeElement(int* nums, int numsSize, int val){
    int newLen = 0;
    for(int i=0; i<numsSize; i++){
        if (nums[i] != val){
            nums[newLen] = nums[i];
            newLen++;
        }
    }
    return newLen;
}
{% endhighlight %}


# 26. Remove Duplicates from Sorted Array
Given a sorted array nums, remove the duplicates in-place such that each element appear only once and return the new length.

# Solution 
{% highlight c %}
int removeDuplicates(int* nums, int numsSize){
    if (!numsSize) return 0;
    
    int newLen = 1;
    
    for(int i=1; i<numsSize; i++){
        if (nums[i] == nums[i-1]){
            continue;    
        }
        else{
            nums[newLen] = nums[i];
            newLen++;
        }
    }
    return newLen;
}
{% endhighlight %}
