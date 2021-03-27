---
layout: post
title: "LeetCode::Tree(Part1)"
date: 2019-05-03
mathjax: true
categories: Algorithm
---
# 98. Validate Binary Search Tree
Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

* The left subtree of a node contains only nodes with keys less than the node's key.
* The right subtree of a node contains only nodes with keys greater than the node's key.
* Both the left and right subtrees must also be binary search trees.

# Wrong solution:
{% highlight c %}
bool isValidBST(struct TreeNode* root) {
    if (!root)
        return true;
    else if (root->left && (root->val <= root->left->val))
        return false;
    else if (root->right &&(root->val >= root->right->val))
        return false;
    else
        return isValidBST(root->left)&&isValidBST(root->right);
    
}
{% endhighlight %}

```
    5
   / \
  1   4
     / \
    3   6
```
# Correct But Slow Solution

# Solution 1 (Recursive)
{% highlight c %}
bool _isValidBST(struct TreeNode* root, int min, int max){
    if (root == NULL)
        return true;
    else if (root->val >= max || root->val <= min)
        return false;
    else
        return _isValidBST(root->left, min, root->val) &&
                _isValidBST(root->right, root->val, max);
}

bool isValidBST(struct TreeNode* root){
    return _isValidBST(root, INT_MIN, INT_MAX);
}
{% endhighlight %}
# Solution 2
in order traversal 

# 100. Same Tree
Given two binary trees, write a function to check if they are the same or not.

# Solution
{% highlight c %}
bool isSameTree(struct TreeNode* p, struct TreeNode* q) {
    if (p==NULL || q==NULL)
        return q == q;
    else 
        return (p->val == q->val) && 
            isSameTree(p->left, q->left) && 
            isSameTree(p->right, q->right);
    
}
{% endhighlight %}

# 101. Symmetric Tree
Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).

For example, this binary tree ``[1,2,2,3,4,4,3]`` is symmetric:

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

# Solution
{% highlight c %}
bool isMirror(struct TreeNode* p, struct TreeNode* q){
    if (p==NULL || q==NULL)
        return p == q;
    else
        return (p->val == q->val) &&
            isMirror(p->left, q->right) &&
            isMirror(p->right, q->left);
}

bool isSymmetric(struct TreeNode* root){
    if (!root)
        return true;
    else
        return isMirror(root->left, root->right);
}
{% endhighlight %}

# 94. Binary Tree Inorder Traversal
Given a binary tree, return the inorder traversal of its nodes' values.

# Solution 1
{% highlight c %}
/**
 * Return an array of size *returnSize.
 * Note: The returned array must be malloced, assume caller calls free().
 */

int* inorderTraversal(struct TreeNode* root, int* returnSize) {
    if (!root){
        *returnSize = 0;
        return NULL;
    }
    int leftSize, rightSize;
    int* leftTree, *rightTree;
    
    leftTree = inorderTraversal(root->left, &leftSize);
    rightTree = inorderTraversal(root->right, &rightSize);
    *returnSize = leftSize + rightSize + 1;

    int* ans = malloc(sizeof(int)*(*returnSize));
    if (leftTree)
        memcpy(ans, leftTree, sizeof(int)*(leftSize));
    ans[leftSize] = root->val;
    if (rightTree)
        memcpy(ans+(leftSize)+1, rightTree, sizeof(int)*(rightSize));
    
    return ans;
}
{% endhighlight %}  

# 110. Balanced Binary Tree
Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as:
a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

# Solution 1 (Slow)
{% highlight python %}
def isBalanced(root):
    if root is None:
        return True
    else:
        return (abs(maxDepth(root.left) - maxDepth(root.right)) <=1 and
               isBalanced(root.left) and
               isBalanced(root.right))
        
def maxDepth(root):
    if root is None:
        return 0
    else:
        lenLeft = maxDepth(root.left)
        lenRight = maxDepth(root.right)
        return max(lenLeft, lenRight) + 1
{% endhighlight %}
# Solution 2 (Global Variable)
{% highlight python %}
def isBalanced(root):
    Balanced = True                
    def dfs(self, root):
            if root is None:
                return 0
            else:
                left = dfs(root.left)
                right = dfs(root.right)
                if abs(left-right) > 1:
                    Balanced = False
                return max(left, right) + 1
    dfs(root)
    return Balanced
{% endhighlight %}
# Solution 3
{% highlight python %}
def isBalanced(self, root):            
    def dfs(root):
        if root is None:
            return 0
        left  = dfs(root.left)
        right = dfs(root.right)
        if left == -1 or right == -1 or abs(left - right) > 1:
            return -1
        return max(left, right) + 1
            
    return dfs(root) != -1
{% endhighlight %}
# 108. Convert Sorted Array to Binary Search Tree
Given an array where elements are sorted in ascending order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

# Solution
{% highlight c %}
struct TreeNode* sortedArrayToBST(int* nums, int numsSize){
    if (numsSize == 0)
        return NULL;
    else if (numsSize == 1){
        struct TreeNode* newNode = malloc(sizeof(struct TreeNode));
        newNode->val = nums[0];
        newNode->left = NULL;
        newNode->right = NULL;
        return newNode;
    }
    else{
        int mid = numsSize/2;
        struct TreeNode* newNode = malloc(sizeof(struct TreeNode));
        newNode->val = nums[mid];
        newNode->left = sortedArrayToBST(nums, mid);
        newNode->right = sortedArrayToBST(nums+mid+1, numsSize-mid-1);
        return newNode;
    }
}
{% endhighlight %}


# 111. Minimum Depth of Binary Tree
Given a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

Note: A **leaf** is a node with no children.

# Wrong Solution
{% highlight python %}
def minDepth(self, root: TreeNode) -> int:
    if not root:
        return 0
    minLeft = self.minDepth(root.left) 
    minRight = self.minDepth(root.right) 
    return min(minLeft, minRight) + 1
{% endhighlight %}

Example: {1, 2} has minumum depth 2
If one of the subtree has depth 0, we need to get the other one.


# Solution 1 (DFS)
{% highlight python %}
def minDepth(root):
    if not root:
        return 0
    minLeft = minDepth(root.left) 
    minRight = minDepth(root.right) 
    if minLeft == 0:
        return minRight + 1
    if minRight == 0:
        return minLeft + 1
    return min(minLeft, minRight) + 1
{% endhighlight %}

# Solution 2 (BFS)
{%highlight python %}
def minDepth(root):
    if not root:
        return 0
    queue = collections.deque([(root, 1)])
    while queue:
        node, level = queue.popleft()
        if not node.left and not node.right:
            return level
        if node.left:
            queue.append((node.left, level+1))
        if node.right:
            queue.append((node.right, level+1))
{% endhighlight %}

# Solution 3 (BFS without Queue)
For large tree, BFS could be faster

{% highlight python %}
def minDepth(root):
    if not root:
        return 0
        
    depth = 0
    current_level = [root]
        
    while current_level:
        depth += 1
        next_level = []
        for node in current_level:
            left = node.left
            right = node.right
            if not left and not right:
                return depth
            if left:
                next_level.append(left)
            if right:
                next_level.append(right)
        current_level = next_level
    return depth
{% endhighlight %}

