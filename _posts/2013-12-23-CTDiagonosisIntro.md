---
layout:       post
title:        "CTDiagnosis"
description: 
banner: 
categories: 
- 玩意儿
---

<br />

和同学花了近三个月的时间完成了这个项目，收货颇为丰富，这儿算是一个简单的总结。

这是一个医疗辅助诊断软件，用户的是临床医生。软件会根据患者的基本情况和肺部CT序列图，综合判断患者可能患有肺癌的情况。

在巨大利益的诱惑下，我们充分发挥了劳动人民的辛勤与智慧，从一无所有到最终达到商用，其中艰辛，可谓是你挑着担我牵着你……

[工程在这里](https://github.com/PhoenixZhao/CTDiagnosis)，不知不觉Python代码已经有两万多，抛出其中UI部分，有效代码在一万左右。这个中型项目结合了本科研究生阶段掌握的一些理论与工程方面的知识

- 理论层面用到了SVM做机器学习，用区域增长法和快速离散曲波变换(FDCT)变换来提取图像特征
- 工程方面用全部使用Python，SQLite做数据库，PyQt做界面

###一些反思

- 一定要使用版本管理软件来协同合作。Github的private是收费的，最近发现[BitBucket](https://bitbucket.org/)免费private，赞！就是界面还有功能比Github弱挺多，数据展示不够直观
- 有条件的要使用具有**协同管理的云笔记**，我在使用Evernote的会员版，是双十一那天买一年赠一年的，多蹭别人一顿饭就回来了，是超划算！
- 做之前一定要明白做的是什么！**明确需求！**反复确认需求，跟同伴也要反复确认。周围已经有太多太多同学抱怨甲方是傻X了。我们都说大公司机构臃肿，执行率低，如果我们俩人还执行率低，那就是人的问题了
- Python非常适合做原型开发，已经有那么多人做了那么多的轮子了
- 想不明白的问题，**测试与开发，怎样并置？**有的时候为了找一个Bug觉都睡不好，有的时候为了写个测试样例，比源代码敲的都多。


---
为了不出洋相，下面的介绍采用了洋文

###What is it?
This is a software of lung cancer diagnosis.As part of a computer aided diagnosis(CAD) system, 
this software aims to assist doctocs make a diagnosis of lung cancer 
by analysing the Computed Tomography(CT) images.  

- Based on the technology of Digital Image Processing and Machine Learning,we calculate(predict) the probability 
of a person suffering from lung cancer.
- All CT images are firstly pre-processed and partitioned into regions via **Region growing** algorithm,
and then **Fast Discrete Curvelet Transforms**(FDCT) Algorithm is applied to extract the most 14 distinctive 
features from the pre-processed image. 
- Finally the extracted features ,labels together with their 
corresponding patient's info are trained by **Support Vector Machine**(SVM).
- Once the Lung cancer model is established,
it could tell the probability of a patient suffering from lung cancer with his info and 
CT images sequence.
- With the increased use of this software the prediction accuracy is improved continuously.

###Technologies

- [PyQt](http://www.riverbankcomputing.co.uk/software/pyqt/download/) for the GUI 
and [Installation Guide](http://blog.csdn.net/bookeezhou/article/details/6229011)
- scikit-learn [details on github](https://github.com/scikit-learn/scikit-learn) for SVM
- [numpy](http://www.numpy.org/) for Region Grow and CDFT
- [skimage](http://scikit-image.org/docs/dev/api/skimage.html) for Image Processing
- sqlite3 for the database
- [PyInstaller](http://www.pyinstaller.org/) for building stand-alone executables program
- [pydicom](http://code.google.com/p/pydicom/) for dealing the dicom type Image


###Special Thanks

Thanks for the project of [MP View](http://qt-apps.org/content/show.php/MP+View?content=68379),
we create our ImageViewer GUI based on that.

