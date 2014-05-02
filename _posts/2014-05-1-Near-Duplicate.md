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

主要介绍图像领域的大规模相似检索

很多相关算法都是由传统的文档相似性比较推广而来的，本文首先介绍传统的大规模图像检索方法，再介绍近期的几篇热点论文，最后对simhash这个技术做了简要的补充

###一、传统的大规模图像检索方法

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

###二、近期的大规模相似检索文章
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

###三、SimHash

[海量数据相似度计算之simhash和海明距离](http://www.lanceyan.com/tech/arch/simhash_hamming_distance_similarity.html)这篇文章介绍的简明扼要，这里直接将核内容粘贴过来

> 为此我们需要一种应对于海量数据场景的去重方案，经过研究发现有种叫 local sensitive hash 局部敏感哈希的东西，据说这玩意可以把文档降维到hash数字，数字两两计算运算量要小很多。查找很多文档后看到google对于网页去重使用的是simhash，他们每天需要处理的文档在亿级别，大大超过了我们现在文档的水平。既然老大哥也有类似的应用，我们也赶紧尝试下。simhash是由 Charikar 在2002年提出来的，参考[《Similarity estimation techniques from rounding algorithms》](dl.acm.org/citation.cfm?id=509965) 。 介绍下这个算法主要原理，为了便于理解尽量不使用数学公式，分为这几步：

1. **分词**，把需要判断文本分词形成这个文章的特征单词。最后形成去掉噪音词的单词序列并为每个词加上权重，我们假设权重分为5个级别（1~5）。比如：“ 美国“51区”雇员称内部有9架飞碟，曾看见灰色外星人 ” ==> 分词后为 “ 美国（4） 51区（5） 雇员（3） 称（1） 内部（2） 有（1） 9架（3） 飞碟（5） 曾（1） 看见（3） 灰色（4） 外星人（5）”，括号里是代表单词在整个句子里重要程度，数字越大越重要。
2. **hash**，通过hash算法把每个词变成hash值，比如“美国”通过hash算法计算为 100101,“51区”通过hash算法计算为 101011。这样我们的字符串就变成了一串串数字，还记得文章开头说过的吗，要把文章变为数字计算才能提高相似度计算性能，现在是降维过程进行时。
3. **加权**，通过 2步骤的hash生成结果，需要按照单词的权重形成加权数字串，比如“美国”的hash值为“100101”，通过加权计算为“4 -4 -4 4 -4 4”；“51区”的hash值为“101011”，通过加权计算为 “ 5 -5 5 -5 5 5”。
4. **合并**，把上面各个单词算出来的序列值累加，变成只有一个序列串。比如 “美国”的 “4 -4 -4 4 -4 4”，“51区”的 “ 5 -5 5 -5 5 5”， 把每一位进行累加， “4+5 -4+-5 -4+5 4+-5 -4+5 4+5” ==》 “9 -9 1 -1 1 9”。这里作为示例只算了两个单词的，真实计算需要把所有单词的序列串累加。
5. **降维**，把4步算出来的 “9 -9 1 -1 1 9” 变成 0 1 串，形成我们最终的simhash签名。 如果每一位大于0 记为 1，小于0 记为 0。最后算出结果为：“1 0 1 0 1 1”。

![](http://www.lanceyan.com/wp-content/uploads/2013/08/simhash.png)

得出的重要结论：

> 通过大量测试，simhash用于比较大文本，比如500字以上效果都还蛮好，距离小于3的基本都是相似，误判率也比较低。但是如果我们处理的是微博信息，最多也就140个字，使用simhash的效果并不那么理想。看如下图，在距离为3时是一个比较折中的点，在距离为10时效果已经很差了，不过我们测试短文本很多看起来相似的距离确实为10。如果使用距离为3，短文本大量重复信息不会被过滤，如果使用距离为10，长文本的错误率也非常高，如何解决？

###总结：

- 按照Charikar在论文中阐述的，64位simhash，海明距离在3以内的文本都可以认为是近重复文本。当然，具体数值需要结合具体业务以及经验值来确定。 
- simhash还可以用于信息聚类、文件压缩
- 选择和设计文本的去重算法？常见的有余弦夹角算法、欧式距离、Jaccard相似度、最长公共子串、编辑距离等，但是只适合于小数据集
- simhash传统的用来判断两篇文章的相似度，将两篇文章映射到低维空间上，并且保持它们互相之间的相似度，但是它很难应用在图像比较上，因为图像的特征是用实数来表示的，尽管可以将其量化，但是两幅相似图像量化后的特征集合交叠的比率仍旧很小，远远小于文档，因为两幅图像不相似的区域的噪声特征非常大
- 但是如果使用bundle features，那么如果两个相似区域的bundle features会非常相同，我们就可以使用simhash了
- 所以，min-hash的使用场景是特征比较多，相似度比较显著的情况下。

---
###Refferences
POLICY, N. (2013). Web-Scale Near-Duplicate Search: Techniques and Applications. IEEE MultiMedia.