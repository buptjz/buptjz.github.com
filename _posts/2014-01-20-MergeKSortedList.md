---
layout:       post
title:        "【算法】Merge k Sorted Lists"
description: 
banner: 
categories: 
- algorithm
---

<br />

#Merge k Sorted Lists

[leetcode上的一道题](http://oj.leetcode.com/problems/merge-k-sorted-lists/)

###**题目描述:** 
Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity. k个链表，都已经排序好了，现在要将k个链表合并。

###**解析:**

最直接想到的是**归并排序**，归并排序的思想就是将一个数组分成两个子数组，分别进行排序，排序好之后再进行合并，合并的方式是用两个指针指向数组的第一个元素，比较两个数，依次向后移动，所用的时间是O(n)，根据这个思想，写出的代码如下：


具体代码如下：

```
#include "MergekSortedLists .h"
#include <vector>
#include <stdlib.h>
class Solution {
public:
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        ListNode *res = (ListNode *)malloc(sizeof(ListNode));//返回的链表
        int k = (int)lists.size();//求k路归并的k是多少
        
        //创建k个指针分别指向k路的第一个node
        ListNode **pp = (ListNode **)malloc(sizeof(ListNode*)*k);
        for (int i = 0 ; i < k; i++) {
            pp[i] = lists[i];
        }
        while (true) {
            ListNode *small;
            for (int i = 0; i < k; i++) {
                if (pp[i] != NULL) {
                    if (small == NULL && small->val < pp[i]->val) {
                        small = pp[i];
                    }
                    pp[i] = pp[i]->next;
                }
            }
            if(small == NULL){
                break;
            }else{
                res->next = small;
            }
        }
        return res->next;
    }
};

```

遗憾的是上述代码在leetcode上time out了，说好的bug free呢... :(

然后就想到k的值可能非常大，每次对全部k个链表头结点查找最大，需要的时间是O(k)，整个算法的时间是O(k*n)，n是所有链表包含节点数之和。自然的发现每次找最大值而进行全扫描是多此一举的，**最小堆**是一个更好的选择，最小堆的插入、弹出都是O(logK)，在C++使用最小堆用priority_queue即可，具体用法参见references.代码如下：

```
#include <vector>
#include <stdlib.h>

#include <queue>

using namespace std;

struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};

struct cmp{
    bool operator()(ListNode* t1,ListNode* t2)
    {
        if ( !t1 || !t2 )
            return !t2;
        return t1->val>t2->val;
    }
};

class MergekSortedLists {
public:
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        if (lists.empty()) return NULL;
        int k = (int)lists.size();//求k路归并的k是多少
        
        priority_queue<ListNode*,vector<ListNode*>,cmp> Q;
        for(int i = 0;i < k;i++){
            if ( lists[i] != NULL){
                Q.push(lists[i]);
            }
        }
        ListNode guard(-1);
        ListNode* tail=&guard;
        while(!Q.empty())
        {
            ListNode* toAdd = Q.top();
            Q.pop();
            tail->next = toAdd;
            tail = tail->next;
            if (toAdd->next) Q.push(toAdd->next);//如果新加入的Node还指向另一个指针，那么把新的指针加入到其中来
        }
        return guard.next;
    }
};


```

###参考
[1] [C++ STL priority_queue](http://blog.csdn.net/sup_heaven/article/details/8036982)

[2] [[LeetCode] Merge k Sorted Lists](http://blog.csdn.net/a83610312/article/details/9565541)