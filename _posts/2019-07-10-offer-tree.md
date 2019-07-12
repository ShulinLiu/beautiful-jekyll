---
layout: post
title: 剑指offer专题 -- 数组
tags: [Leetcode，剑指offer]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 二叉树

## 二叉树中和为某一值的路径

题目描述：
```
输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)
```
这道题是典型的DFS题。
{% highlight C++ linenos %}
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    vector<vector<int> > FindPath(TreeNode* root,int expectNumber) {
        vector<vector<int>> res;
        vector<int> path;
        dfs(root,expectNumber, path,res);
        return res;
    }
    
    void dfs(TreeNode* root, int expectNumber,vector<int>& path,vector<vector<int>>& res){
        if (root == NULL)
            return;
        
        path.push_back(root->val);
        if(root->left == NULL && root->right == NULL && 0 == expectNumber-root->val ){
            res.push_back(path);
        }
        
        dfs(root->left,expectNumber-root->val, path, res);
        dfs(root->right, expectNumber-root->val, path, res);
        path.pop_back();
    }
};
{% endhighlight %}

## 二叉树的深度

题目描述：
```
输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。
```

{% highlight C++ linenos %}
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    int TreeDepth(TreeNode* pRoot)
    {
        if(pRoot == NULL)
            return 0;
        return max(TreeDepth(pRoot->left),TreeDepth(pRoot->right))+1;
    }
};
{% endhighlight %}

## 平衡二叉树

题目描述：
```
输入一棵二叉树，判断该二叉树是否是平衡二叉树。
```
平衡二叉树的定义是：树中任意一个结点下左右两个子树的高度差不超过 1。

解法1:
{% highlight C++ linenos %}
class Solution {
public:
    bool IsBalanced_Solution(TreeNode* pRoot) {
        if(pRoot == NULL)
            return true;
        
        int left = TreeDepth(pRoot->left);
        int right = TreeDepth(pRoot->right);
        return abs(left-right) <= 1 &&IsBalanced_Solution(pRoot->left) && IsBalanced_Solution(pRoot->right);
    }
    
    int TreeDepth(TreeNode* root){
        if(root == NULL)
            return 0;
        return max(TreeDepth(root->left),TreeDepth(root->right))+1;
    }
};
{% endhighlight %}

解法2:
{% highlight C++ linenos %}
class Solution {
public:
    bool IsBalanced_Solution(TreeNode* pRoot) {
        if(pRoot == NULL)
            return true;
        
        int left = TreeDepth(pRoot->left);
        int right = TreeDepth(pRoot->right);
        return abs(left-right) <= 1 &&IsBalanced_Solution(pRoot->left) && IsBalanced_Solution(pRoot->right);
    }
    
};
{% endhighlight %}

# 二叉搜索树

## 二叉搜索树与双向链表

题目描述：
```
输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。
```
这道题主要是利用二叉搜索树的中序遍历是有序的这一特点，递归的连接相应的节点即可。
{% highlight C++ linenos %}
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    TreeNode* Convert(TreeNode* pRootOfTree)
    {
        TreeNode *listRoot = NULL;
        TreeNode* p = NULL, *cur = pRootOfTree;
        stack<TreeNode*> s;
        
        while(!s.empty()||cur!=NULL){
            if(cur!=NULL){
                s.push(cur);
                cur = cur->left;
            }else{
                cur = s.top();
                s.pop();
                
                //双向链表的头节点
                if(p == NULL){
                    p = cur;
                    listRoot = p;
                }else{
                    p->right = cur;
                    cur->left = p;
                    p = p->right;
                }
                
                cur = cur->right;
            }
        }
        return listRoot;
    }
};
{% endhighlight %}
