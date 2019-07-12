---
layout: post
title: 剑指offer专题 -- 其他
tags: [Leetcode，剑指offer]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 其他

## 整数中1出现的次数

题目描述：
```
求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。
```

{% highlight C++ linenos %}
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        int count = 0;
        for(int i = 1; i <= n; i *= 10){
            int a = n/i,b = n%i;
            count += (a+8)/10 * i + (a%10==1)*(b+1);
        }
        return count;
    }
};
{% endhighlight %}

## 丑数

题目描述：
```
把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。
```
用一个数组来存储丑数，解题的关键是保证数组是有序的。对于乘2而言，肯定存在某一个丑数t2使得排在他前面的每一个丑数乘以二都小于已有的最大丑数。t3和t5同理。

{% highlight C++ linenos %}
class Solution {
public:
    int GetUglyNumber_Solution(int index) {
        if(index < 7)
            return index;
        vector<int> res(index);
        for(int i = 0; i < 6; i++)
            res[i] = i + 1;
        
        int t2 = 3, t3 = 2, t5 = 1;
        for(int i = 6; i < index; i++){
            res[i] = min(res[t2]*2,min(res[t3]*3,res[t5]*5));
            
            while(res[i] >= res[t2]*2)
                t2++;
            while(res[i] >= res[t3]*3)
                t3++;
            while(res[i] >= res[t5]*5)
                t5++;
        }
        return res[index-1];
    }
};
{% endhighlight %}

## 

题目描述：
```

```
用一个数组来存储丑数，解题的关键是保证数组是有序的。对于乘2而言，肯定存在某一个丑数t2使得排在他前面的每一个丑数乘以二都小于已有的最大丑数。t3和t5同理。

{% highlight C++ linenos %}

{% endhighlight %}