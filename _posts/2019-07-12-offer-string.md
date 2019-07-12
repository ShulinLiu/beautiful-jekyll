---
layout: post
title: 剑指offer专题 -- 字符串
tags: [Leetcode，剑指offer]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 字符串

## 第一个只出现一次的字符

题目描述：
```
在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.
```

{% highlight C++ linenos %}
class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        int n = str.size();
        if(n <= 0)
            return -1;
        
        unordered_map<char,int> mp;
        for(int i = 0; i < n; i++){
            if(mp.find(str[i])!=mp.end())
                mp[str[i]]+=1;
            else
                mp[str[i]] = 1;
        }
        
        for(int i = 0; i < n; i++){
            if(mp[str[i]]==1)
                return i;
        }
        
        return -1;
    }
};
{% endhighlight %}

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