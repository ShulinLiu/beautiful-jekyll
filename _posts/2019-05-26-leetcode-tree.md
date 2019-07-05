---
layout: post
title: LeetCode专题-Tree
tags: [Leetcode,Tree,Golang]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

本文主要总结leetcode中与Tree相关的题目，并给出了Go语言解法。

# 94. Binary Tree Inorder Traversal

题目描述：

```
Given a binary tree, return the inorder traversal of its nodes' values.

Example:

Input: [1,null,2,3]
   1
    \
     2
    /
   3

Output: [1,3,2]

Follow up: Recursive solution is trivial, could you do it iteratively?
```
解法如下，主要利用了stack。
```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func inorderTraversal(root *TreeNode) (ret []int) {
    if root == nil{
        return
    }
    
    var stack []*TreeNode
    cur := root
    
    for cur!=nil || len(stack) > 0{
        if cur != nil{
            stack = append(stack,cur)
            cur = cur.Left
        }else{
            cur  = stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            ret = append(ret,cur.Val)
            cur = cur.Right
        }
    }
    return
}
```
# 96. Unique Binary Search Trees
题目描述：
```
Given n, how many structurally unique BST (binary search trees) that store values 1 ... n?

Example:

Input: 3
Output: 5
Explanation:
Given n = 3, there are a total of 5 unique BST's:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3

```
本题要求给出能够存储整数1到n的BST的个数。解题思路是运用DP动态规划。
```
func numTrees(n int) int {
    if n == 0{
        return 0
    }
    
    var dp []int
    //dp[0] = 1
    dp = append(dp,1)
    
    for i:=1;i <= n;i++{
        dp = append(dp,0)
        for j := 0; j < i; j++{
            dp[i] += dp[j]*dp[i-1-j]
        }
    }
    
    return dp[n]
}
```
# 95. Unique Binary Search Trees II
题目描述：
```
Given an integer n, generate all structurally unique BST's (binary search trees) that store values 1 ... n.

Example:

Input: 3
Output:
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
Explanation:
The above output corresponds to the 5 unique BST's shown below:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3

```
本题要求列出所有可能的BST。对于这类问题，主要的思路是DFS。
```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func generateTrees(n int) []*TreeNode {
    var ret []*TreeNode
    if n < 1{
        return ret
    }
    return genBST(1,n)
}

func genBST(min, max int) []*TreeNode{
    var ret []*TreeNode
    if min > max{
        ret = append(ret,nil)
        return ret
    }
    
    for i:=min; i <=max; i++{
        leftsubtree := genBST(min,i-1)
        rightsubtree := genBST(i+1,max)
        
        for j:=0;j < len(leftsubtree); j++{
            for k:= 0; k < len(rightsubtree); k++{
                root :=  &TreeNode{Val:i}
                root.Left = leftsubtree[j]
                root.Right = rightsubtree[k]
                ret = append(ret,root)
            }
        }
    }
    
    return ret
}
```

# 98. Validate Binary Search Tree
题目描述：
```
Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

    The left subtree of a node contains only nodes with keys less than the node''s key.
    The right subtree of a node contains only nodes with keys greater than the node's key.
    Both the left and right subtrees must also be binary search trees.

 

Example 1:

    2
   / \
  1   3

Input: [2,1,3]
Output: true

Example 2:

    5
   / \
  1   4
     / \
    3   6

Input: [5,1,4,null,null,3,6]
Output: false
Explanation: The root node's value is 5 but its right child's value is 4.
```
本题要求判断一颗tree是否为二叉搜索树。根据二叉搜索树的定义，只要满足任何一个节点的左节点的值小于该节点的值，右节点的值都大于该节点的值即可。回忆一下二叉树的遍历，根据二叉搜索树的中序遍历是有序的特点，在这里我们采用中序遍历遍历整棵树，并判断其值是否有序，便可得到本题的解。
```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isValidBST(root *TreeNode) bool {
    if root == nil{
        return true
    }
    var stack []*TreeNode
    cur := root
    var prev *TreeNode
    
    for len(stack)>0 || cur!=nil{
        if cur!=nil{
            stack = append(stack,cur)
            cur = cur.Left
        }else{
            cur = stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            if prev!=nil &&prev.Val >= cur.Val{
                return false
            }
            
            prev = cur
            cur = cur.Right
        }
    }
    return true
}
```

# 99. Recover Binary Search Tree
题目描述：
```
Two elements of a binary search tree (BST) are swapped by mistake.

Recover the tree without changing its structure.

Example 1:

Input: [1,3,null,null,2]

   1
  /
 3
  \
   2

Output: [3,1,null,null,2]

   3
  /
 1
  \
   2

Example 2:

Input: [3,1,4,null,null,2]

  3
 / \
1   4
   /
  2

Output: [2,1,4,null,null,3]

  2
 / \
1   4
   /
  3

Follow up:

    A solution using O(n) space is pretty straight forward.
    Could you devise a constant space solution?
```
本题依然是采用中序遍历，主要是找到两个错序的节点，交换两个节点的值即可。O(n)空间的解法如下：
```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func recoverTree(root *TreeNode)  {
    cur := root
    var pre,first,second *TreeNode
    var stack []*TreeNode
    
    for cur!=nil || len(stack)>0{
        if cur != nil{
            stack = append(stack,cur)
            cur = cur.Left
        }else{
            cur = stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            
            if pre != nil{
                if pre.Val > cur.Val{
                    if first == nil{
                        first = pre
                    }
                    second = cur
                }
            }
            
            pre = cur
            cur = cur.Right
        }
    }
    val := first.Val
    first.Val = second.Val
    second.Val = val
}
```
# 100. Same Tree
题目描述如下：
```
Given two binary trees, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

Example 1:

Input:     1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

Output: true

Example 2:

Input:     1         1
          /           \
         2             2

        [1,2],     [1,null,2]

Output: false

Example 3:

Input:     1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

Output: false
```
本题要求判断两棵树是否为相同的树，采用递归判断即可，解法如下：
```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isSameTree(p *TreeNode, q *TreeNode) bool {
    if p == nil && q == nil{
        return true
    }else if p == nil || q == nil{
        return false
    }else
    {
        return p.Val == q.Val && isSameTree(p.Left,q.Left) && isSameTree(p.Right,q.Right)
    }
}
```
# 101. Symmetric Tree
题目描述：
```
Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).

For example, this binary tree [1,2,2,3,4,4,3] is symmetric:

    1
   / \
  2   2
 / \ / \
3  4 4  3

 

But the following [1,2,2,null,3,null,3] is not:

    1
   / \
  2   2
   \   \
   3    3

 

Note:
Bonus points if you could solve it both recursively and iteratively.
```
本题与上题类似，也可以采取递归来判断。
```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isSymmetric(root *TreeNode) bool {
    if root == nil{
        return true
    }
    
    return isSame(root.Left,root.Right)
}

func isSame(p,q *TreeNode) bool{
    if p == nil && q == nil{
        return true
    }else if p==nil || q==nil{
        return false
    }else{
        a := isSame(p.Left,q.Right)
        b := isSame(p.Right,q.Left)
        return p.Val == q.Val && a && b
    }
}
```
# 102. Binary Tree Level Order Traversal
题目描述：
```
Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

For example:
Given binary tree [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7

return its level order traversal as:

[
  [3],
  [9,20],
  [15,7]
]
```
这里给出树的层序遍历的非递归实现，主要是要利用queue结构。
```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func levelOrder(root *TreeNode) (ret [][]int) {
    if root == nil{
        return
    }
    
    que := []*TreeNode{root}
    for len(que) > 0{
        var res []int
        size := len(que)
        for i:=0;i < size;i++{
            cur := que[0]
            res = append(res,cur.Val)
            que = que[1:]
            if cur.Left!= nil{
                que = append(que,cur.Left)
            }
            if cur.Right!=nil{
                que = append(que,cur.Right)
            }
        }
        ret = append(ret,res)
    }
    return
}
```
# 103. Binary Tree Zigzag Level Order Traversal
题目描述：
```
Given a binary tree, return the zigzag level order traversal of its nodes' values. (ie, from left to right, then right to left for the next level and alternate between).

For example:
Given binary tree [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7

return its zigzag level order traversal as:

[
  [3],
  [20,9],
  [15,7]
]
```
本题是树的层序遍历的一个应用，要求每一层以不同的顺序打印。这里只需要考虑不同层的打印顺序与层数之间的关系即可。
```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func zigzagLevelOrder(root *TreeNode) (ret [][]int ){
    if root == nil{
        return
    }
    
    que := []*TreeNode{root}
    isleft := true
    
    for len(que) > 0{
        size := len(que)
        path := make([]int,size)
        for i:=0;i<size;i++{
            cur := que[0]
            que = que[1:]
            
            idx := i
            if !isleft{
                idx = size-1-i
            }
            path[idx] = cur.Val
            
            if cur.Left!=nil{
                que = append(que,cur.Left)
            }
            if cur.Right!=nil{
                que = append(que,cur.Right)
            }
        }
        ret = append(ret,path)
        isleft = !isleft
    }
    return
}
```
# 104. Maximum Depth of Binary Tree
题目描述：
```
Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

Note: A leaf is a node with no children.

Example:

Given binary tree [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7

return its depth = 3.
```
本题要求二叉树的最大高度。与大多数与树有关的题目类似，主要采用递归方法来实现。
```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func Max(x,y int) int{
    if x > y{
        return x
    }else{
        return y
    }
}

func maxDepth(root *TreeNode) int {
    if root == nil{
        return 0
    }
    return Max(maxDepth(root.Left),maxDepth(root.Right))+1
}
```