---
layout:       post
title:        "【算法总结】链表问题总结"
description: 
banner: 
categories: 
- algorithm
---

<br />

##经验之谈
- 链表题目中基本所有的题目的解法的时间复杂度都是O(n)，如果你设计的方法时间复杂度是O(n2)，那基本上可以排除这种解法了
- 求解链表问题，可以尝试使用转换的方法。请看下面这个问题
> 求两个无环链表是否相交？
- 一种很常见的trick是使用两个指针，一个每次走两步，另一个每次走一步
> 求一个链表中间的节点（已知链表节点的个数是双数）


##题目解析
> 给出两个单向链表的头指针h1,h2,判断两个链表是否相交

这是一个题目看起来很简单，但实际解决起来颇为麻烦的一道题。难点在于情况是否考虑周到

首先考虑到链表是否是有环的：（1）如果两个链表都是无环链表，那么只需要判断两个链表的尾指针是否相等；（2）如果链表一个有环一个无环，那么肯定不相交；（3）如果两个链表都是有环的，那么请看后文解析。

怎么判断两个链表有/无环的情况呢。用到上文所述的技巧，使用两个指针，最开始都同一个表头，指针a每次走1步，指针2每次走两步。如果量表是有环的，那么这两个指针最终一定会相遇，并且相遇的地点是在环内的某处。

知道了怎么判断有无环的方法，我们可以使用该方法检测链表h1和h2是否是有环的，如果两个链表都有环，就是这种情况：*指向h1的指针a1,b1相较于环内某点x1,指向h2的指针a2,b2指向环内的某点x2。*

那么我们可以重新遍历h2链表：从x2开始，依次向后走，如果在回到x2之前与x1相遇，则两个链表相交。