---
layout:       post
title:        "泊松图像编辑入门宝典"
description: 
banner: 
categories: 
- Computer Vision
---

<br />

粗暴的意译了[Poisson Image Editing](http://www.ctralie.com/Teaching/PoissonImageEditing/)，原文非常简单易懂，可以作为阅读原Paper之前的入门读物。

原文作者Chris Tralie

***


##简介

泊松图像编辑是一种自动的“无缝融合”两张图像的技术。Microsoft Research UK的Patrick Perez，Michel Gangnet, and Andrew Blake的[这篇文章Poisson Image Editing](http://bybjhso.xeds.eu/clone/poisson.pdf)中首次提出。为了更好的了解它所解决的问题，我们看下面这个例子：

如果我们想把这张同学合影图像……

![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/Examples/beach.jpg) 

与大白鲨图像……

![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/Examples/GreatWhiteShark.jpg) 

融合在一起：

![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/Examples/sharkBefore.png) 

直接融合显然效果很垃圾，问题所在：看起来太假！两张图像都有水，但是水的阴影不一样，一个偏蓝一个偏绿，切割图像的边缘因此非常明显。我们可以在PS中进行一些简单的尝试，把大白鲨调亮一点，但是无论怎样调整，边界还是清晰可见。有没有一种方法能够自动的无缝拼合这两张图像？

![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/Examples/sharkAfter.png) 

当然，既然我已经写到这了，答案肯定是YES！（就是泊松图像编辑）。在这个教程的目标是通过一种简单可操作的方式来解释这个算法，最后我附上一个user-friendly的程序，允许大家自己去尝试。事不宜迟，让我们开始做一些数学题

**注意**：本文用到的图像是部分我和我朋友拍摄的，部分来自Wikimedia ，[点击这里](http://www.ctralie.com/Teaching/PoissonImageEditing/PictureAttributions.txt)查看Wikimedia的资源列表


##在边界条件下进行梯度匹配

算法的全部内容可以归结到一个非常简单的idea。为了更好地说明，我们先仔细的定义一下我们的问题：我们改变的图像是A（背景图像），我们剪切&粘贴的图像是B（前景）

![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/imageAimageB.png)


在粘贴的时候，我们的诉求是：允许图像B改变颜色，但是仍然能够保留B的完整的“细节”。细节包括B中全部的边缘、角点、过度等等。如果你阅读了我的教程[Edge, Corner, and Blob Detection](www.ctralie.com/Teaching/EdgeCornerBlob)，你已经知道了从图像中提取这些细节的一些方法，每一种方法的第一步都是计算**图像梯度**。图像梯度是描述图像的一种数学表达，描述的是像素与相邻像素的相对变化（本质上是像素与其相邻像素的差值）。我们需要寻找的就是相对描述子，因为图像A与图像B之间的不统一主要是因为他们颜色上的绝对差。
因此，更为严格的泊松图像编辑的目标是：允许改变绝对信息（图像B的颜色），但是在粘贴之后尽可能的保留B的相对信息（图像梯度）。下面就是鲨鱼图像的梯度，相对信息大概就长这个样子。 

Image

![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/Examples/GreatWhiteShark.jpg)

Gradient Magnitude

![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/Examples/sharkGradient.png)

如果将图像梯度孤立的去看，它是缺乏约束的。做个类比，我让你在纸上画一个路径，“向前走2cm，向左转30度，向前走300cm”，但是我没有告诉你起始位置。你画的路线可能开始于任何一个位置任何方向，仍然满足我的描述。右梯度约束的相对信息跟这个是一个道理。我们需要固定指定像素的RGB值，然后求解我们的问题。才能让我们的图像B更加像图像A。我们将图像B的**边缘像素固定，其像素值为图像A的像素值**，然后求解其余的在选取内的像素值，约束是**保持图像B的原始梯度**。下一节我会给出一些具体的公式，如何求解的具体推到，希望现在你至少知道了我们需要做什么。

##离散解和基于稀疏矩阵的实现
现在让我列出来解决这个问题的公式。约定：被粘贴的图像是A，剪切&粘贴的是B，求解得到的新的粘贴图像是H（H是B的升级，能更好的与A融合）。最简单的部分：边界限制。我们之前已经提到了，H的边界应该与A边界完全一致，所以我们可以匹配选取内部的像素，向内地融合他们，用数学表示：

![代码](/img/posts/PIE公式1.png)  

我们已经知道了H边界的解（同样也是B的边界）。现在我们需要求解H内部的像素值。我们希望内部像素的梯度值等于B内部像素的梯度值。一个点上图像梯度的简单定义：该像素与所有像素的差值的和。

![代码](/img/posts/PIE公式3.png)  

如果一个相邻像素是（1）边界像素，那么它的值是固定的；（2）超出选区边界，被排除。下面的差分方程总结了H的每一个像素点的所有情况（从Paper中摘取出来的改写了一下，我认为这个写法更有意义）

![代码](/img/posts/PIE公式2.png)  


其中（x,y）是2D网格上感兴趣的像素点的位置。N是相邻像素的数量（包含边界像素，N<=4）。Ω是B选区（不包含边界），partial Ω是边界，(dx, dy)是可能的相邻像素点位置，包括 {(-1, 0), (1, 0), (0, -1), (0, 1)}。看起来有点乱，但其实非常容易理解，让我来用语言描述一下这个公式：

   * 等式左侧是计算未知像素点H(x, y)的空间梯度，计算的方式是将H(x,y)与每一个相邻的像素点做差值，并加和。每一个差值的形式都是H(x, y) - other(x', y’)，其中(x', y’)是其他像素点的位置。至于为什么减去两次，是因为一次减去内部相邻像素，一次减去边界的相邻像素。（因为边界像素是固定的，而内部像素是未知数，所以我们分别对待）

   * 等式右边就是简单的计算B图像在(x,y)处的梯度值，它与我们新的图像H的像素值匹配。

   * 对于RGB图像，这些公式会分别处理R,G,B三个通道。


希望你理解了上述内容。没理解也没有关系，只要知道基本的思路：我们尝试求解图像H，H能够与背景图A更好的匹配。H的边界与A完全一样，而H相邻像素之间的差异与B相邻像素之间的差异匹配。



下一步我们需要为H中的每一个点列出等式，注意到我们列出了一组线性方程，包含k个未知数，k是我们需要求解的H中像素数量，最直接的方案是把所有的方程放到一个矩阵中，然后反转矩阵。然而，k的值相当的大，对于200X200的选取而言，k是40,000。反转一个40000X40000的矩阵太庞大了。我们注意到矩阵是极为稀疏的，因为每一个点至多有4个相邻像素，（并且是正定的）。每一行至多有5个非零的元素，其余是0。针对这样的特性，可以采用迭代矩阵求解算法。为了简便，我决定使用 Jacobi Method来求解稀疏线性方程组。Jacobi Method是梯度下降算法的一个特例。它的基本思路是：


  1. 以A x = b 的形式建立矩阵等式。A是我上述定义的等式的矩阵，x是我们待求解的值（本例中是H图像的像素值），b是等式需要等于的值。如果你有一个稀疏矩阵，对它进行压缩是一个好主意（我的程序仅仅用数据结构存储哪些非零的条目）

  2. 初始化x，使之全为0。

  3. 计算Ax的积。

  4. 计算b-Ax的差值，这个差值衡量的是当前猜测的x的值和正确值之间的误差。

  5. 将差值（b-Ax）追加到x上。这就是我们让猜测向着正确方向前进的“梯度下降”的步骤。

  6. 重复步骤3-5，直到x和（b-Ax）之间的差值足够小。

如果A是正定的（这个例子中A是正定的），这个过程能够保证收敛到x的正确的解，并以指数速度收敛。我写的关于 Jacobi method方法的代码在MatrixSolver.java中。迭代方法的最开始，是一个正确的边界+一片漆黑的内部，然后慢慢的从外部想内部填充像素。下面这个图例展示了 从1%, 到 33%,到 66%, 到 100%收敛的过程。


![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/convergence0.png) 
![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/convergence33.png)
![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/convergence66.png) 
![代码](http://www.ctralie.com/Teaching/PoissonImageEditing/convergence100.png) 


在求解本问题的时候，有比Jacobi Method方法更好的技术，但是因为Jacobi Method方法简单直观，易于理解和实现，所以我就是用了它。对于大的选取，性能上会有一点不好（对于150px X 150px的选取，可能要等上一两分钟），对于小的选区，相当的块。

**注意**：Paper中泊松编辑更为严格。它一开始就认为图像A和B是在二维空间上连续的方程，并建立了一个偏微分方程来描述的，在最小二乘意义上的图像B和A的梯度。其结果就是你会得到一个叫做“泊松方程”的东东。但实际上你不要理解偏微分方程就能够理解这个技术如何在离散像素上工作，所以我在讨论中省略了这个部分。对数学感兴趣的读者应该看一下论文中的这个章节，毕竟，泊松方程无处不在，你会看到它在这个问题上的简明的运用。

##结果与讨论

- 请参见[原文](http://www.ctralie.com/Teaching/PoissonImageEditing/)

***

##参考
- [雅克比迭代法](http://zh.wikipedia.org/wiki/%E9%9B%85%E5%8F%AF%E6%AF%94%E6%B3%95)
- [正定矩阵](http://zh.wikipedia.org/wiki/%E6%AD%A3%E5%AE%9A%E7%9F%A9%E9%98%B5)
- [豆瓣上正定矩阵的讨论](http://www.douban.com/group/topic/4943161/)