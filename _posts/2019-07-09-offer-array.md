---
layout: post
title: 剑指offer专题 -- 数组
tags: [Leetcode，剑指offer]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 数组

## 数组中出现次数超过一半的数字
题目描述：
```
数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。
```
解题思路1：用一个哈希表记录每个元素的次数，比较是否超过一半。

{% highlight C++ linenos %}
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        int n = numbers.size();
        for(int i = 0; i < n; i++){
            if(mp.find(numbers[i]) == mp.end())
                mp[numbers[i]] = 1;
            else
                mp[numbers[i]] += 1;
        }
         
        for(int i = 0; i < n; i++){
            if(mp.find(numbers[i]) == mp.end()){
                continue;
            }else{
                if(mp[numbers[i]] > n / 2)
                    return numbers[i];
                else
                    mp.erase(numbers[i]);
            }
        }
         
        return 0;
    }
    unordered_map<int,int> mp;
};
{% endhighlight %}

解法2:
{% highlight C++ linenos %}
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        int n = numbers.size();
        if(n == 0) return 0;
        
        int res = numbers[0], count = 1;
        
        for(int i = 1; i < n; i++){
            if(count == 0){
                res = numbers[i];
                count = 1;
            }else if(res == numbers[i]){
                count += 1;
            }else{
                count -= 1;
            }
        }
        
        count = 0;
        for(int i = 0; i < n; i++){
            if(res == numbers[i])
                count++;
        }
        if(count > n / 2)
            return res;
        return 0;
    }
};
{% endhighlight %}

## 最小的K个数

题目描述：
```
输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。
```

解法1，排序暴力解法：
{% highlight C++ linenos %}
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if(k < 0 || k > input.size())
            return res;
        
        sort(input.begin(),input.end());
        for(int i = 0; i < k; i++)
            res.push_back(input[i]);
        
        return res;
    }
};
{% endhighlight %}

解法2:

上述的解法1的算法时间复杂度是O(N^2)，还有一种基于对排序的O(Nlogk)算法。

我们县创建一个大小为k的数据容器来存储k个最小的数字，接下来每次从输入中读取一个一个整数。如果容器中数字少于k个则直接插入；如果容器已满，则比较这个整数与容器中的最大值，确定替换或者丢弃。

因此在容器满了之后需要做三件事：
1 在k个整数中找最大输
2 有可能需要删除最大数
3 有可能需要新插入一个数

如果用一个二叉树来实现这个容器，则需要O(logk)时间来实现。总时间为O(nlogk).
{% highlight C++ linenos %}
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> result;
        int length = input.size();
        bool change = true;
        if(length <= 0 || k <= 0 || k > length){
            return result;
        }
        
        for(int i = 0; i < input.size(); i++){
            if(result.size() < k){
                result.push_back(input[i]);
            }
            else{
                if(change == true){
                    for(int j = k / 2; j >= 0; j--){
                        HeadAdjust(result, j, k);
                    }
                    for(int j = k - 1; j > 0; j--){
                        swap(result[0], result[j]);
                        HeadAdjust(result, 0, j);
                    }
                    change = false;
                }
                if(result[k-1] > input[i]){
                    result[k-1] = input[i];
                    change = true;
                }
            }
        }
        return result;
    }
private:
    void HeadAdjust(vector<int> &input, int parent, int length){
        int temp = input[parent];
        int child = 2 * parent + 1;
        while(child < length){
            if(child + 1 < length && input[child] < input[child+1]){
                child++;
            }
            if(temp >= input[child]){
                break;
            }
            input[parent] = input[child];
            
            parent = child;
            child = 2 * parent + 1;
        }
        input[parent] = temp;
    }
};
{% endhighlight %}

## 连续字数组的和

题目描述：
```
HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)
```

{% highlight C++ linenos %}
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        int n = array.size();
        
        //初始化
        int maxsum = array[0];
        int cursum = array[0];
        
        for(int  i = 1; i < n; i++){
            if(cursum <= 0)
                cursum = array[i];
            else
                cursum += array[i];
            
            if(cursum > maxsum)
                maxsum = cursum;
        }
        
        return maxsum;
    }
};
{% endhighlight %}

## 把数组拍成最小的数

题目描述：
```
输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。
```

解题思路：

定义一种比较方法cmp,满足：
1 
2 
3 

{% highlight C++ linenos %}
class Solution {
public:
    string PrintMinNumber(vector<int> numbers) {
        int n = numbers.size();
        if(n == 0)
            return "";
        
        sort(numbers.begin(),numbers.end(),cmp);
        string res;
        for(int i = 0; i < n; i++){
            res += to_string(numbers[i]);
        }
        return res;
    }
    
    static bool cmp(int a, int b){
        string A = to_string(a)+to_string(b);
        string B = to_string(b)+to_string(a);
        return A < B;
    }
};
{% endhighlight %}

其中，cmp必须定义为静态成员函数或者是全局变量。因为，std::sort是全局函数，无法在其中调用非静态成员函数。静态成员函数或者全局函数是不依赖于具体对象的可以独立访问的。

当然，也可以用lambda表达式来代替cmp.
{% highlight C++ linenos %}
class Solution {
public:
    string PrintMinNumber(vector<int> numbers) {
        int n = numbers.size();
        if(n == 0)
            return "";
        
        sort(numbers.begin(),numbers.end(),[](int a,int b){return to_string(a)+to_string(b) < to_string(b)+to_string(a);});
        string res;
        for(int i = 0; i < n; i++){
            res += to_string(numbers[i]);
        }
        return res;
    }
};
{% endhighlight %}