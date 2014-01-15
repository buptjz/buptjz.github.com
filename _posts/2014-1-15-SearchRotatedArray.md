---
layout:       post
title:        "【算法】旋转数组查询"
description: 
banner: 
categories: 
- algorithm
---

<br />

#旋转数组查询问题

###**题目描述:** 
给一个已经排序的数组，包含了n个整数，将其进行旋转若干次，写一个函数能够在旋转数组中查找某一个元素
###**解析:**
对排序好的数组进行旋转，并在旋转后的数组*(rotated array)*进行查找是常考的一道题，[leetcode上的这道题](http://oj.leetcode.com/problems/search-in-rotated-sorted-array-ii/)和本题基本一致。
旋转数组的特点是对于中间的某一个点来说，它的左串和右串必然有一个排序好的。
###需要注意的两点：
- 旋转数组可能是排好序的，这是一种特殊情况，编写代码的时候要把这种情况考虑进去。
- 旋转数组中可能包含重复的元素

###解题思路：
最直观的解决方法是线性的查找，不过这显然没有用到旋转数组部分已排序的特点。对于一个非旋转数组，我们可以用二分查找，这里我们很容易会去想是否能将二分查找的思路用在旋转数组中。**当我们有了思路时，我们不妨举一些简单的例子来看一看会遇到什么情况。**

例如下面这种情况：
<div class="row">
    <div class="span4">
        <table>
           <tr>
           		<td>3</td>
           		<td>4</td>
           		<td>5</td>
           		<td>6</td>
           		<td>1</td>
           		<td>2</td>
           </tr>
        </table>
    </div>
</div>
当我们想要查找2的时候，会先找到5，这个时候2<5，按照原来二分查找的思路，会在左侧继续查找，但是旋转后，2可能同时出现在5的左右两侧。~~继续观察我们会发现，如果2出现在右串，那么整个串end位置上必须>2,**并且**，并且<5，因为如果末端的数>中间的数，说明中间开始往后是一个顺串，根本不会存在小于中间值的数。通过这种观察，我们可以判断末端是否大于中间来砍去一半的搜索量.~~
实际上这个条件非常容易出错，例如笔者在写本文的时候之前以为正确的答案就出现了问题。Cracking the Code Interview给出的答案非常好，根据数组第一个值和中间值的比较，分为了三种情况:

- A[left] < A[mid]:左边正确排序（右边也可能正确排序）
- A[left] > A[mid]:右边正确排序（左边不可能正确排序，右边可能重复）
- A[left] == A[mid]:此处又分两种情况：（1）A[right] == A[mid],此时两边都要搜索；（2）A[right]!=A[mid]稍加分析可以看出此时A[left]到A[mid]之间的所有元素都是重复的，所以此时在右边搜索，具体代码如下：

```
int searchInRotateArray(int *A,int start,int end,int target){
    if (A == NULL || start > end) return -1;//base case
    
    int mid = start + ((end - start) >> 1);
    if (A[mid] == target) return mid; //找到，立即返回
    
    if (A[mid] == A[start] && A[mid] == A[end]) {
        int result = searchInRotateArray(A, start, mid-1,target);
        if (result == -1) {
            //先在左边找，如果没找到，再去右边找，不能用||，因为这里return的是-1而不是0
            result = searchInRotateArray(A, mid+1, end,target);
        }
        return result;
    }else if(A[start] < A[mid]) {//左串排好序的
        if (target < A[mid] &&  target >= A[start]) {
            //x夹在左、中之间，那么右串都是在A[start]~A[mid]范围之外的，所以不用考虑
            return searchInRotateArray(A, start, mid-1,target);
        }else{
            return searchInRotateArray(A, mid+1, end,target);
        }
    }else if(A[start] > A[mid]){//右串排序了,很关键
        if (target <= A[end] &&  target > A[mid]) {
            //x夹在右、中之间，那么左串都是>A[end],<A[mid]的，所以不用考虑
            return searchInRotateArray(A, mid+1, end,target);
        }else{
            return searchInRotateArray(A, start, mid-1,target);
        }
    }else if(A[start] == A[mid]){
        //特殊条件：左串是重复的，而右串非重复，也就是A[start]和A[mid]中间所有的数都是重复的，那么只在右边找
        return searchInRotateArray(A, mid+1, end,target);
    }
    
    return -1;
}

void testSearchInRotateArray(){
    int a[5] = {3,4,5,1,2};
    int r1 = searchInRotateArray(a, 0, 4, 3);
    printf("Expected %d and get %d\n",1,r1);
    
    int b[10] = {1,1,1,1,1,1,0,1,1,1};
    int r2 = searchInRotateArray(b, 0, 9, 0);
    printf("Expected %d and get %d\n",6,r2);
}
```