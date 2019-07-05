---
layout: post
title: LeetCode专题-Implement Trie(Prefix Tree)前缀表达树
tags: [leecode,prefix tree]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 208. Prefix Tree
前缀表达树又称字典树，是一种多叉树结构，主要用于快速搜索。本题要求设计前缀表达树，实现insert, search, 和 startsWith三个方法。
```
Implement a trie with insert, search, and startsWith methods.

Example:

Trie trie = new Trie();

trie.insert("apple");
trie.search("apple");   // returns true
trie.search("app");     // returns false
trie.startsWith("app"); // returns true
trie.insert("app");   
trie.search("app");     // returns true

Note:

    You may assume that all inputs are consist of lowercase letters a-z.
    All inputs are guaranteed to be non-empty strings.
```

解题思路：
首先我们要设计一下TireNode节点,这里用is_word标记当前节点是否为某一个单词的结尾。

节点对于子节点的表达，既可以采用动态数组，也可以采用哈希表来存储子节点。

其中，采用动态数组时，需要注意一定要在节点的析构函数中删除子节点的指针，以免内存泄漏。

```c++
class Trie {
private:
    struct TrieNode
    {
        TrieNode():is_word(false),children(26,nullptr){}

        //must delete TrieNode* in destructor
        ~TrieNode(){
            for(auto child : children)
                if(child)
                    delete child;
        }
        bool is_word;
        vector<TrieNode*> children;
    };

    std::unique_ptr<TrieNode> _root;
```
```c++
class Trie {
private：
    struct TrieNode {
        TrieNode():is_word(false){}
        
        ~TrieNode() {
            for (auto& kv : children)
                if (kv.second) delete kv.second;
        }
        
        bool is_word;
        //use hash map
        unordered_map<char, TrieNode*> children;

        std::unique_ptr<TrieNode> root_;
    };
```
insert方法的实现很简单：
```c++
    /** Inserts a word into the trie. */
    void insert(const string& word) {
        TrieNode* p = root_.get();
        for (const char c : word) {
            if (!p->children.count(c))
                p->children[c] = new TrieNode();
            p = p->children[c];
        }
        p->is_word = true;
    }
```
最后，实现search和startsWith，我们添加一个辅助函数find寻找子节点。
```c++
    const TrieNode* find(const string& prefix) const {
        const TrieNode* p = root_.get();
        for (const char c : prefix) {
            if (!p->children.count(c)) return nullptr;
            p = p->children.at(c);
        }
        return p;
    }
    
    /** Returns if the word is in the trie. */
    bool search(const string& word) const {
        const TrieNode* p = find(word);
        return p && p->is_word;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    bool startsWith(const string& prefix) const {
        return find(prefix) != nullptr;
    }
```