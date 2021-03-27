---
layout: post
title: "A Primer On Bit Manipulation"
date: 2019-05-05
mathjax: true
categories: Algorithm
---
## The Basics
------
Suppose we have a single bit ``x``. 

1. To set it, let ``x = x | 1``. 
2. To clear it, let ``x = x & 0``
3. To read it (i.e. not modifying any bit), read the result of  ``x & 1``
4. To toggle it, let ``x = x ^ 1``

## Create the Mask
------
Suppose we have an **unsigned int** number of ``10111011``.  
Suppose index start at 0.
Suppose n = 3, m = 5

To set bits, we want our interest interval to be all ``1``'s.
1. To set the **nth** bit:
```
    mask = (1 << n)         # mask = 1000
    x = x | mask
```
2. To set the **last n** bits:
```    
    mask = (1 << n) - 1   # mask = 1000 - 1 = 111
    x = x | mask
```
3. To set from ``m``th bits to ``n``th bits, inclusive
```   
    mask = ((1 << m) - 1) - ((1 << n-1) - 1) 
    x = x | mask
```


1. To clear the **nth** bit:
```
    mask = (1 << n) - 1
    x = x & mask
```

2. 
