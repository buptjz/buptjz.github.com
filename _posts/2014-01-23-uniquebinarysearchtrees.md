---
layout:       post
title:        "【算法】Unique Binary Search Trees"
description: 
banner: 
categories: 
- algorithm
---

<br />

#Unique Binary Search Trees

[leetcode上的一道题](http://oj.leetcode.com/problems/zigzag-conversion/)

###**题目描述:** 
Given n, how many structurally unique BST's (binary search trees) that store values 1...n?给定数字n，给出n个数可以组成多少个二叉搜索树

例如，给定n=3，一共有5棵不同的树
![代码](/img/posts/uniqueBST.png)


###**解析:**

直觉：
根节点共有n种选择，选定根节点后，根节点左右孩子的节点数就确定下来了，例如根节点选择第x个数，那么左孩子有x-1个节点，左+右共有n-1个节点，所以右孩子有(n-1) - (x-1) = n-x个节点。这是一个递归的过程，也可以看做是动态规划的过程，要知道n个节点有多少种情况，就要知道1，2，3……，n-1个点分别有多少种情况。

###答案：

下面首先给出递归的算法：


```
int numTreesHelper(int n){
    if (n == 0 || n == 1) return 1;
    
    int retValue = 0;
    for (int i = 1;i <= n; i++) {
        retValue += numTreesHelper(n - i) * numTreesHelper(i - 1);
    }
    return retValue;
}

int numTrees(int n) {
    if (n == 0) {
        return 0;
    }
    return numTreesHelper(n);
}
```
既然是动态规划问题，自然的想到优化的方式，就是采用自底向上的。既然计算n需要1，2，3，……，n-1，那么我按照1，2，3的顺序依次计算，这样避免程序重复求解相同的子问题。


```
int numTrees(int n) {
    if (n == 0) return 0;
    int a[n];
    a[0] = 1;
    a[1] = 1;
    for (int i = 2; i <= n; i++) {
        int ithTreeNum = 0;
        for (int j = 0; j < i; j++) {
            ithTreeNum += a[j] * a[i-j-1];
        }
        a[i] = ithTreeNum;
    }

    return a[n];
}

```