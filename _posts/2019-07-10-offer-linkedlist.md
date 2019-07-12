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


## 两个链表的第一个公共结点

题目描述：
```
输入两个链表，找出它们的第一个公共结点。
```

解法一：
{% highlight C++ linenos %}
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        if(pHead1 == NULL || pHead2 == NULL)
            return NULL;
        
        stack<ListNode*> s1,s2;
        while(pHead1!=NULL){
            s1.push(pHead1);
            pHead1 = pHead1->next;
        }
        while(pHead2!=NULL){
            s2.push(pHead2);
            pHead2 = pHead2->next;
        }
        
        ListNode *res = NULL;
        while(!s1.empty()&&!s2.empty()){
            if(s1.top()==s2.top())
                res = s1.top();
            s1.pop();
            s2.pop();
        }
        return res;
    }
};
{% endhighlight %}

解法二：
{% highlight C++ linenos %}
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        if(pHead1 == NULL || pHead2 == NULL)
            return NULL;
        
        unsigned int l1 = getLength(pHead1);
        unsigned int l2 = getLength(pHead2);
        
        ListNode* plong = pHead1;
        ListNode* pshort = pHead2;
        int gap = l1-l2;
        if(l1 < l2){
            plong = pHead2;
            pshort = pHead1;
            gap = l2-l1;
        }
        for(int i = 0; i < gap; i++){
            plong = plong->next;
        }
        
        while(plong!=NULL && pshort !=NULL&& plong!=pshort){
            plong = plong->next;
            pshort = pshort->next;
        }
        
        return plong;
    }
    
    unsigned int getLength(ListNode* pHead){
        if(pHead == NULL)
            return 0;
        
        unsigned int res = 0;
        while(pHead!=NULL){
            pHead = pHead->next;
            res++;
        }
        return res;
    }
};
{% endhighlight %}