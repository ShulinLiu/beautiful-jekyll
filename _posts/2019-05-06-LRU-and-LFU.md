---
layout: post
title: LeetCode专题-LRU和LFU详解
tags: [leecode,LRU,LFU,C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 1. LRU与LFU简介
LRU (Least recently used，最近最少使用 ) 和 LFU (Least frequently used，最不经常使用)是两种经典的cache管理算法。其主要差别在于其核心思想：

+ LRU：如果数据最近被访问过，那么将来被访问的几率也更高
+ LFU：如果数据在最近一段时间内访问次数越少，那么将来被访问的几率也越低

LRU和LFU具备的操作主要是：`get` and `put`.

+ `get(key)` - 返回key对应的值，如果key不存在，则返回-1
+ `put(key, value)` - 如果key存在则将其对应的值设置为value，如果key不存在，则插入新的节点。如果cache已经满了，则需要以某种准则（满足某种函数f(x)）去除cache中已存在的节点。

这一数据结构的核心在于当存储空间满了要插入新的节点时，以何种规则（即所谓的f(x)）丢弃相应的节点。在设计LRU和LFU时的主要差别也在于此。

# 2. LRU的设计与实现

为了快速找到最早更新的节点，可选的数据结构有：queue，heap，linked list。由于题目又要求快速访问指定节点，并且在访问之后要更新它的时间顺序，queue和heap不太适用。因此考虑使用linked list，这里我们选用double linked list，简化节点的删除操作，实现O(1)的删除效率。并用hash table以便实现O(logn)时间随机访问节点。

首先是双链表的节点定义：
{% highlight c++ linenos %} 
struct Node{
        Node *pre;
        Node *next;
        int key;
        int val;
        Node(int k,int v):key(k),val(v),pre(NULL),next(NULL){}
    };
{% endhighlight %}

使用map将key与相应的节点对应起来，实现以O(logn)时间随机访问linked list中的相应节点，并进行删除更新等相关操作。另外在构造函数中还需指定capacity的大小：
{% highlight c++ linenos %} 
class LRUCache {
public：
LRUCache(int capacity) {
        capacity_ = capacity;
        head = NULL;
        tail = NULL;
        keyToNode.clear();
    }
private:
    int capacity_; 
    Node *head;//头节点
    Node *tail;//尾节点
    unordered_map<int,Node*> keyToNode;//key与节点Node的map
};
{% endhighlight %}

下面设计辅助函数。由于我们将节点按照使用的时间顺序插入double linked list当中，所以头节点是最少最近使用的节点，而尾节点是最近使用的节点。因此首先设计三个函数：删除头节点：removeHead()以及插入新节点：insertToEnd(int k, int v)，以及更新已存在节点在双链表中的顺序：moveToEnd(int key)。
{% highlight c++ linenos %} 
    void insertToEnd(int k, int v){
        //if full or already exist, return
        if(isFull()||keyToNode.count(k)!=0) return;
        
        Node *nd = new Node(k,v);
        keyToNode[k] = nd;
        //if head = tail = NULL
        if(!head){
            head = tail = nd;
        }
        else{
            tail->next = nd;
            nd->pre = tail;
            tail = tail->next;
        }
    }
    
    void removeHead(){
        //if head not exist
        if(!head) return;
        keyToNode.erase(head->key);
        Node *tmp = head;
        if(head == tail)//if only one node
        {
            head = tail = NULL;
        }
        else{
            head = head->next;
            head->pre = NULL;
        }
        delete tmp;
    }
    
    void moveToEnd(int key){
        //if key not exist or already in the end
        if(keyToNode.count(key) == 0|| keyToNode[key] == tail) return;
        
        Node *nd = keyToNode[key];
        if(nd == head)//if at the front
        {
            head = head->next;
            head->pre = NULL;
        }
        else{
            nd->pre->next = nd->next;
            nd->next->pre = nd->pre;
        }
        tail->next = nd;
        nd->pre = tail;
        nd->next = NULL;
        tail = tail->next;
    }
{% endhighlight %}

`get(int key)`函数的设计比较简单，直接查找map中key值是否存在，如果不存在返回-1，反之，更新节点位置到链表尾端并返回其值即可。
{% highlight c++ linenos %} 
    int get(int key) {
        if(keyToNode.count(key) == 0) return -1;
        //如果存在，将节点更新到链表尾端
        moveToEnd(key);
        return keyToNode[key]->val;
    }
{% endhighlight %}

 `put(int key, int value)`分为以下情况：1.如果节点存在，只需要更新节点的值并更新节点在链表中的位置(moveToEnd)即可。这里我们使用get函数判断节点是否存在，则可以省略moveToEnd操作。2.如果节点不存在，则插入节点前需要判断是否溢出，如果溢出，则先将头节点删除再在尾节点插入新节点即可。

{% highlight c++ linenos %} 
    void put(int key, int value) {
        //if already exist, update value
        if(get(key)!=-1){
            keyToNode[key]->val = value;
            return;
        }
        
        //if not exist, insert
        if(isFull()) removeHead();
        insertToEnd(key,value);
    }
{% endhighlight %}

# 3. LFU的设计与实现

与LRU纯粹比较数据使用时间与当前时间的远近不同，LFU的核心思想如果数据在最近一段时间内访问次数越少，那么将来被访问的几率也越低。因此在设计LFU时，我们需要引进变量：频率frequency，并且需要根据频率值来决定节点的删除操作。这里需要注意的是：当几个节点的频率都相同时，采用LRU的思想，优先删除的是最近最少使用的节点。

根据以上的分析，首先我们需要在节点上增加一个变量freq记录节点的频率。其次，除了LRU中key与Node的map之外，对于每一个频率值，我们还需要一个list 来记录在这一频率值下的节点。因此我们增加一个频率与list的map：freqMap。另外，为了迅速找到并删除节点，我们在Node结构中增加iterator，记录frequency list的头部。

节点的定义和LFU的结构如下：
{% highlight c++ linenos %} 
struct Node{
        int val;
        int freq;
        list<int>::iterator it;
        Node():freq(1){}
        Node(int value):val(value),freq(1){}
    };
{% endhighlight %}

{% highlight c++ linenos %} 
class LFUCache {
public:
    LFUCache(int capacity) {
        this->capacity = capacity;
    }

private:
    int capacity;
    int minFreq;
    unordered_map<int,Node*> cacheMap;
    unordered_map<int,list<int>> freqMap;
};
{% endhighlight %}

`get(int key)`函数的设计与LRU中get函数类似。需要注意的是这里更新节点不再是将节点移到到链表尾部，而是更新节点的freq,将原节点从以前的freq list中移除，添加到新的freq list前端，最后更新节点的iterator。
{% highlight c++ linenos %} 
    int get(int key) {
        //if not exist
        if(cacheMap.find(key) == cacheMap.end()) return -1;
        
        Node* node = cacheMap[key];
        //update freq
        freqMap[node->freq].erase(node->it);
        node->freq += 1;
        freqMap[node->freq].push_front(key);
        node->it = freqMap[node->freq].begin();
        if(freqMap[minFreq].size()==0)
            minFreq = node->freq;
        
        return node->val;
    }
{% endhighlight %}
 `put(int key, int value)`的设计同样需要注意对freq list的处理。

{% highlight c++ linenos %} 
    void put(int key, int value) {
        if(capacity <= 0) return;
        
        if(get(key) == -1)
        {
            if(cacheMap.size()==capacity)
            {
                //remove
                int popkey = freqMap[minFreq].back();
                cacheMap.erase(popkey);
                freqMap[minFreq].pop_back();
            }
            
            //insert new node
            Node*  node = new Node(value);
            minFreq = 1;
            freqMap[1].push_front(key);
            node->it = freqMap[1].begin();
            cacheMap[key] = node;
        }
        else
        {
            cacheMap[key]->val = value;
        }
    }
{% endhighlight %}
