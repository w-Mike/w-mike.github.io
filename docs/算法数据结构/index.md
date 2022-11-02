---
layout: default
title: 数据结构&算法
nav_order: 7
has_children: false
---

## 二叉树的遍历

掌握使用stack，进行非递归的二叉树遍历，主要理解二叉树遍历的访问顺序是一样的，只是在节点访问几次后输出的问题。

前序，访问到一次就输出。

中序，访问到两次输出。

后序，访问到三次输出。



# 牛客

## 2022年 11月 02日 星期三 10:16:36 CST

### BM95 分糖果问题

 [分糖果问题_牛客题霸_牛客网](https://www.nowcoder.com/practice/76039109dd0b47e994c08d8319faa352?tpId=295&tqId=1008104&ru=/exam/oj&qru=/ta/format-top101/question-ranking&sourceUrl=%2Fexam%2Foj)

**前提：**

        左条件： 取任意相邻节点左A右B，若 A < B 则分得糖果的数量, numA < numB

        右条件： 同理，若 A > B 则分得的糖果 numA > numB

        题目的要求是，分糖果的结果，每个节点都满足上述两个条件。

        容易理解的是从左向右遍历一遍后，能够使所有节点符合左条件

        先不讨论为什么接着需要从右往左开始遍历，来去满足右条件，直接讨论从右往左遍历的合理性

**右遍历方法：**

        从右往左遍历的方式为：取任意相邻左A右B，经过左遍历后，为numA与numB

        在右遍历的过程中，如果A > B,而numA <= numB，则numA = numB+1,否则不变

**理解：**

        首先从左向右已经符合了左条件了，那么只要证明按给出的方式从右往左遍历（必然满足右条件）后，不会破坏左条件即可。

        从右往左遍历的过程中，任意两个节点左A右B，假设A的左边为C，

        C --- A --- B

1.   C < A，那么经过左遍历后必然numC < numA
   
   1. A > B, 根据右遍历的方法，
      
      - 如果numA < numB, numA = numB+1, 则numA仍然满足 numA > numC,故没有破坏左条件。
      
      - numA >= numB, numA值不变，故仍满足左条件
   
   2. A <= B, numA的值不变，仍然满足左条件

2.  C > A, 则经过左遍历后,A的值必为1，C的值 >= 1，即numC >= numA
   
   - A > B, numA = numB+1, 此时可能不满足 numC >= numA,如果 numC < numA了，就会在下一轮循环中根据右遍历的方法，使得 numC = numA+1, 仍然会使numC > numA。

其实 C > A 的情况不用考虑，C > A不在左条件的判断范围内，而是在右条件的判断范围下，而右遍历后必然满足右条件。

```cpp
int candy(vector<int> &arr)
{
    int size = arr.size();
    vector<int> res(size, 1);
    for (int i = 1; i < size; ++i)
    {
        if (arr[i - 1] < arr[i])
        {
            res[i] = res[i - 1] + 1;
        }
    }
    for (int i = size - 2; i >= 0; --i)
    {
        if (arr[i] > arr[i + 1] && res[i] <= res[i + 1])
        {
            res[i] = res[i + 1] + 1;
        }
    }
    int num = 0;
    for (const auto i : res)
    {
        num += i;
    }
    return num;
}
```

### BM96 主持人调度（二）

[主持人调度（二）_牛客题霸_牛客网](https://www.nowcoder.com/practice/4edf6e6d01554870a12f218c94e8a299?tpId=295&tqId=1008104&ru=%2Fexam%2Foj&qru=%2Fta%2Fformat-top101%2Fquestion-ranking&sourceUrl=%252Fexam%252Foj)

1. 开始时间添加到一个vector中，排序。

2. 结束时间添加到一个vector中，排序。

3. 遍历开始时间，
   
   - 显然最差的情况就是每个活动都有一个主持人，也就是每次循环都要加一个主持人。
   
   - 但是，有一种情况很好理解：就是发现当前循环的开始时间的**活动**比当前指向的结束时间的**活动**晚，那直接让这个结束活动的主持跑去主持当前循环的活动就好了。所以这种情况就不需要添加主持人。那么就指向下一个结束时间的活动。
   
   - 其余情况，主持人保持++就好。

```cpp
int minmumNumberOfHost(int n, vector<vector<int>> &startEnd)
{
    vector<int> start;
    vector<int> end;
    for (const auto time : startEnd)
    {
        start.push_back(time[0]);
        end.push_back(time[1]);
    }
    sort(start.begin(), start.end());
    sort(end.begin(), end.end());
    int num = 0;
    int endindex = 0;
    for (int i = 0; i < n; ++i)
    {
        if (start[i] >= end[endindex])
        {
            ++endindex;
        }
        else
        {
            ++num;
        }
    }
    return num;
}
```

> 两道贪心算法的题，都是似懂非懂的。但是能写出代码就行，别理解太深入了，大不了背下来也行。

### BM97 旋转数组

> 明明是做过的题目，但是还是给忘了，翻转也太巧妙了，记不住啊。。。

![alt](https://uploadfiles.nowcoder.com/images/20220205/397721558_1644051736400/3E6A48137367D997F49AB13EF302653A)

1. 全部翻转

2. 前offset位翻转

3. 后边的翻转

```cpp
    vector<int> solve(int n, int m, vector<int>& a) {
        int offset = m % n;
        if (!offset) return a;
        reverse(a.begin(), a.end());
        reverse(a.begin(), a.begin() + offset);
        reverse(a.begin() + offset, a.end());
        return a;
    }
```
