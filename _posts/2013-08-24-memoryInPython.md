---
layout:       post
title:        "Python的内存管理 "
description: 
banner: 
categories: 
- 语言
---

<br />
最近在学DeepLearning相关的内容，找到了[DeepLearning.net这个网站]((http://deeplearning.net/software/theano/tutorial/python-memory-management.html#python-memory-management))，上面的tutorial主要是介绍深度学习算法，上面的实现是用Python语言，用到的库主要有numpy和Theano。numpy大家比较熟悉了，不再细说；Theano是一个进行数学计算的库，用户可以更好的定义、计算、优化有关矩阵的数学计算。


###Theano有一些重要的优势：

- 与NumPy紧密结合
- 透明的使用GPU，声称能加速140x
- efficient symbolic differentiation，不知道如何翻译，我用起来感觉就是很容易的就能定义数学函数，求解导数很方便。

本文摘译了其中关于Python内存管理的部分，文章重点是如何在数据量较大的时候，合理的使用Python，不至于导致内存的不足，译文暂且舍弃了原文的具体推导细节，只关注了在大数据场景下Python的使用原则。

###大数据场景下Python的使用原则

- 为了加速内存分配（以及重新使用），Python**使用了大量list来保存“小对象”**。每一个list存储相似大小的对象：举例而言，有一个list保存1～8 bytes大小的对象，另一个list保存9~16bytes大小的对象，依此类推。**当一个“小对象”需要被创建的时候，会有两种情况，要么重新使用上述某个list中的一个空的block，要么重新分配一个新的Block**。
- 关于Python如何管理那些list，如何使用内存块、内存池进行调度，并不是本文关心的内容。如果你想直到细节，可以参见**Evan Jones’ideas on how to improve Python’s memory allocation**。我们探讨的重点是**那些lists从来不会缩减**。
- 实际上，一个item（大小是x）被销毁（因为没有指向它的引用而被释放）的时候，它所占的内存不会被返回到Python的全局内存池中（更不会返回系统中），而是仅仅打上Free标签并且被添加到item大小是x的free list中。如果另外一个大小匹配的object需要创建，dead objects的内存会被重新使用。如果没有死亡的对象，那么新的内存被创建。
- 如果小对象所占据的内存从来不被释放，那么必然会导致小对象lists只能是越滚越大，而从不缩小。而且你的程序中，占大部分内存的是大量的小对象。

###那么我们怎么做呢？

- 针对一个任务，只分配必要数量的小对象，避免分配不必要的小对象；使用仅有少量元素产生的循环。
- 在一个list中你创建许多小对象的时候，他们占据了了小对象lists，即便最后这个list死掉，死掉的对象们（现在全在free lists中）仍然占据了许多内存。
- 有人说了，那些被释放掉的对象所占据的内存依然可以被Python使用，free lists这种机制看起来不是一个问题。这话对了一半！在Windows下这句话是对的，因为在win下Python会把内存返回给系统的heap上；如果你使用的是Linux，你会看到程序占用的内存会持续增长，从不减少。
- 那么说了这么多，到底我们该如何掌握一条原则来立于不败之地呢？那就是，不要一次性的把数据全部加载进来，而是分次加载，每次处理要清空上一次的数据！如果你认真阅读了上文，那么原因不言自明。


###干货分享

memory_profiler这个模块是用来在运行时查看Python程序内存使用的情况，使用非常简单。

使用@profile装饰器来装饰需要查看的函数，运行时使用 -m memory_profiler，就会逐行的打印内存占用情况！
![代码](/img/posts/memory_profiler.png) 
     