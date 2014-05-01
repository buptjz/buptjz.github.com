---
layout:       post
title:        "热点技术探索：大规模相似检索"
description: 
banner: 
categories: 
- Computer Vision
- Machine Learning

---

<br></br>

###传统的大规模图像检索方法

这两篇文章中有比较好的介绍

- [利用Minhash和LSH寻找相似的集合](http://www.cnblogs.com/bourneli/archive/2013/04/04/2999767.html)
- [相似度计算](http://blog.sina.com.cn/s/blog_6634c1410100w56x.html)

####基本思想：

#####1、min-hash得到摘要

- 思路转化：比较两幅图像或者两篇文章的相似度问题转化为比较两个只包含0，1元素的集合的相似度，集合的相似度是Jaccard相似度。`\(JS(A,B)=\frac{|A\cap{B}|}{|A\cup{B}|}\)`
- 根据这样一个神奇的公式`\(Pr[m(S_i) = m(S_j)] = E[\hat{JS}] =JS(S_i,S_j)\)`，使用min-hash函数将一幅图片或者一篇文章转化为一个数（对该文章中的每一个单词id使用hash函数后得到一个新的id序列，这个序列中的第一个出现1的行号，就是min-hash的值）
- 这样我们可以使用k个hash函数，得到k个值，将原本的高维向量映射到了低维
- Min-hash在压缩原始集合的情况下，保证了集合的相似度没有被破坏。

#####2、LSH缩小查找范围

- 其基本思路是将相似的集合聚集到一起，减小查找范围，避免比较不相似的集合
- 对每一列c（即每个集合）我们都计算出了n行minhash值，我们把这n个值均分成b组，每组包含相邻的r=n/b行。对于每一列，把其每组的r个数都算一个hash值出来，把此列的编号记录到hash值对应的bucket里。如果两列被放到了同一个bucket里，说明它们至少有一组(r个)数的hash值相同，此时可认为它们有较大可能相似度较高（称为一对candidate）。最后在比较时只对落在同一个bucket里的集合两两计算，而不是全部的两两比较。

---

###近期的大规模相似检索文章
内容主要来自这篇文章《Web-Scale Near-Duplicate Search: Techniques and Applications》

1. ####《Partial-Duplicate Image Re- trieval via Saliency-Guided Visual Matching》

	- 特点：通过视觉显著性（saliency）模型进行比较，消除背景中的噪声。
	- 优点：这种方法使得索引和匹配都集中在显著性区域，更能够符合用户的预期。**显著值和空间约束**都能够被用来进行相似性度量，并且**能够高效的进行二级索引**，对于大规模的partial duplicate search非常有利。
	- 缺点：内存的开销大

2. ####《Web-Scale Image Retrieval Using Compact Tensor Aggregation of Visual Descriptors》

	- 特点：本文描述了目前存在的各种视觉描述子的概况，介绍了相关的索引技术，包括哈希、词袋以及基于树的表示方法。（hashing, bag-of-words, and tree-based representation）引出内存开销问题并提出一种生成高度压缩签名（highly compact signatures）的方法，包括张量聚合，PCA，kernel PCA等一些列算法。
	- 优点：改进了Fisher Vector族 描述子，提高它的可区分性，以及特征签名的大小（feature discrimina- tive power and the size of feature signature）。

3. ####《Nested-SIFT for Efficient Image Matching and Retrieval》

	- 特点：深入研究SIFT描述子。提出了一个非常优雅的方法：生成sift组，嵌入几何信息，最终讲一个group压缩到一个64比特的二维签名中，叫做nested-SIFT。
	- 优点：
		- Nested—SIFT使用sift描述子的嵌套关系，很自然的将不同尺度的局部关键点组合在一起，生成一个特征签名。
		- 嵌入空间信息的Nested—SIFT可区分性更强。
		- 使用SimHash进行压缩后，在视觉搜索中效率更高
		- 实验结果表明这种方法提高搜索的准确度，减少了内存消耗，提高搜索速度
	- 缺点：生成Nested-SIFT会有一定的计算消耗

4. ####《Scalable Mobile Video Retrieval with Sparse Projection Learning and Pseudo Label Mining》

	- 特点：使用机器学习技术，学习稀疏投影矩阵（sparse projec- tion matrices）。这种虚席方法可以选择性的使用外部信息，比如WikiPedia上的知识和Google搜索结果中的摘要，创建一个语义相关的投影矩阵，生成一个压缩签名，以满足手机媒体检索的诸多限制。
	- 优点：手机内存空间小，计算资源有限，传统的将高维特征映射到低维的投影矩阵在手机内存是放不下的。而我们的稀疏投影矩阵是能够在手机上使用的。

###总结

数据驱动的技术已经持续热了一段时间。这里面的学问是：如果我们有一个巨大的数据池，传统的的识别难题能够轻易的解决，因为识别问题可以转化成为检索问题。
Web-Scale near duplicate检索技术非常重要，它是解决识别等问题的的关键，已经逐步成为解决大规模多媒体内容的“标准步骤”。

---

###Refferences
POLICY, N. (2013). Web-Scale Near-Duplicate Search: Techniques and Applications. IEEE MultiMedia.