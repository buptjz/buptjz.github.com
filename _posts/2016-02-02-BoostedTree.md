---
layout:       post
title:        "GBDT学习"
description: 
banner: 
categories: 
- Machine Learning
---

###Boosted Tree

- 利用基函数的线性组合，其中基函数是决策树
- 前向分步算法
- decision dump 是一个特殊的决策树

#### 模型
Boosted Tree可以表示为决策树的加法模型：

`
\begin{equation}
\hat{y_i}=\sum _{k=1}^Kf _k(x _i)
\label{eq:model}
\end{equation}
`

前向分步算法：首先确定初始的提升树`\\(f_0(x)=0\\)`，在第t步的模型是：

`
\begin{equation}
\hat{y_i}^{(t)}=\sum _{k=1}^tf _k(x _i)=\hat{y _i}^{(t-1)} + f _t(x _i)
\label{eq:modelt}
\end{equation}
`

其中`\\(f _t(x _i)\\)`是第t轮学到的新的函数。


#### 目标函数
`
\begin{equation}
Obj=\sum _{i=1}^n l(y _i, \hat{y _i}) + \sum _{k=1}^K\Omega(f _k)
\label{eq:obj}
\end{equation}
`
#### 参考
- Chen, T. (2014). Introduction to Boosted Trees. http://doi.org/10.1142/9789812771728_0012
- 统计学习方法 李航 8.3 提升树
- 台大机器学习技法 第十一章 GBDT