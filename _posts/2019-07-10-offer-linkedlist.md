---
layout: post
title: 剑指offer专题 -- 链表
tags: [Leetcode，剑指offer]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

本文主要分类总结剑指offer以及其对应的leetcode相关题目。

# 链表

## 复杂链表的复制

对应leetcode 138.复制带随机指针的链表

题目描述：

```
A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.
```
第一种解法主要利用了哈希表：
{% highlight C++ linenos %}
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;

    Node() {}

    Node(int _val, Node* _next, Node* _random) {
        val = _val;
        next = _next;
        random = _random;
    }
};
*/
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if(!head)
            return nullptr;
        
        if(mp.find(head)==mp.end()){
            mp[head] = new Node(head->val);
            mp[head]->next = copyRandomList(head->next);
            mp[head]->random = copyRandomList(head->random);
        }
        return mp[head];
    }
};

private:
    unordered_map<Node*,Node*> mp;
{% endhighlight %}

第二种解法：
{% highlight C++ linenos %}
/*
struct RandomListNode {
    int label;
    struct RandomListNode *next, *random;
    RandomListNode(int x) :
            label(x), next(NULL), random(NULL) {
    }
};
*/
class Solution {
public:
    RandomListNode* Clone(RandomListNode* pHead)
    {
        CloneNodes(pHead);
        ConnectSiblingNodes(pHead);
        return ReconnectNodes(pHead);
    }
    
//第一步，复制复杂指针的label和next
    void CloneNodes(RandomListNode* pHead){
        RandomListNode* pNode = pHead;
        while(pNode != NULL){
            RandomListNode* pCloned = new RandomListNode(0);
            pCloned->label = pNode->label;
            pCloned->next = pNode->next;
            pCloned->random = NULL;
            
            pNode->next = pCloned;
            pNode = pCloned->next;
        }
    }
    
    //第二步，处理复杂指针的random
    void ConnectSiblingNodes(RandomListNode* pHead){
        RandomListNode* pNode = pHead;
        while(pNode != NULL){
            RandomListNode* pCloned = pNode->next;
            if(pNode->random != NULL){
                pCloned->random = pNode->random->next;
            }
            pNode = pCloned->next;
        }
    }
    
    //第三步，拆分复杂指针
    RandomListNode* ReconnectNodes(RandomListNode* pHead){
        RandomListNode* pNode = pHead;
        RandomListNode* pClonedHead = NULL;
        RandomListNode* pClonedNode = NULL;
        
        if(pNode != NULL){
            pClonedHead = pClonedNode = pNode->next;
            pNode->next = pClonedNode->next;
            pNode = pNode->next;
        }
        
        while(pNode != NULL){
            pClonedNode->next = pNode->next;
            pClonedNode = pClonedNode->next;
            pNode->next = pClonedNode->next;
            pNode = pNode->next;
        }
        return pClonedHead;
    }
    
};
{% endhighlight %}


# 字符串

## 字符串的排列
题目描述：
```
输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc，则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

输入描述：

输入一个字符串,长度不超过9(可能有字符重复)，字符只包括大小写字母。
```
解题思路：

我们求整个字符串的排列，可以看成两步：首先求所有可能出现在第一个位置的字符，即把第一个字符和后面所有的字符交换。

首先固定第一个字符，求后面所有字符的排列。这个时候我们仍把后面的所有字符分为两部分：后面的字符的第一个字符，以及这个字符之后的所有字符。然后把第一个字符逐一和它后面的字符交换。

这个思路，是典型的递归思路。
{% highlight C++ linenos %}
class Solution {
public:
    vector<string> Permutation(string str) {
        //判断输入
        if(str.length() == 0){
            return result;
        }
        PermutationCore(str, 0);
        //对结果进行排序
        sort(result.begin(), result.end());
        return result;
    }
    
private:
    void PermutationCore(string str, int begin){
        //递归结束的条件：第一位和最后一位交换完成
        if(begin == str.length()){
            result.push_back(str);
            return;
        }
        for(int i = begin; i < str.length(); i++){
            //如果字符串相同，则不交换
            if(i != begin && str[i] == str[begin]){
                continue;
            }
            //位置交换
            swap(str[begin], str[i]);
            //递归调用，前面begin+1的位置不变，后面的字符串全排列
            PermutationCore(str, begin + 1);
        }
    }
    vector<string> result;
};
{% endhighlight %}
