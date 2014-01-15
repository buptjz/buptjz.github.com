---
layout:       post
title:        "‘我有一点不成熟的看法不知当讲不当讲’"
description: 
banner: 
categories: 
- exp
---


给央视的一个创业节目做了一个非常简单的评分软件，采用CS模式，客户端是iPad，服务器端用得是Django，借机长点EXP  

<br />

###iPad客户端  

- **异步请求** 我觉得任何时候都应该用异步请求，保证用户的体验，而不能因为数据量小就用同步请求
- **URLConnection的引用计数** 这个问题很早就有疑惑，之前在公司就不太晓得咋处理，今天再次发生，充分说明了不求甚解的优秀品质！今天终于搞明白了，[仙人指路](http://stackoverflow.com/questions/1632168/when-to-call-release-on-nsurlconnection-delegate)
- **Custom Button点击时高亮** 怎么设置高亮的图片，傻傻的没找到，用代码写，后来发现了在Button属性中得State Config设置成**Highlighted**，再设置Image属性就可以了
- **Retina双版本图像自动生成** 用[UncorkedStudiosExportHelpers](http://uncorkedstudios.com/blog/best-photoshop-export-action-ever)这个PS脚本，帮我节省了很多的工作,用其中的第二个action,只需要输入名字就能保存图片
- **Django修改时区** 存入数据库如果采用自动的时间戳，显示的时候会发现实践不对，应该加以修改。在settings.py里面把TIME_ZONE设为‘Asia/Shanghai’
- **依然存在的问题 自定义状态栏** *让状态栏能够显示一些提示信息* 用了很多别人的例子，都遇到了问题，其中最显著的就是，竖屏的时候没有问题，横屏的时候，文字方向旋转了90度，没有发现别人有这个问题>O<


###服务器  
- **Django的特点** 我觉得Django非常适合快速开发，特别是中型项目的快速开发，为啥是中型，对于小型项目，显得有点臃肿了，多少还是需要配置一些东西的。Django中得模板的类似于继承的方式可以快速编写相似的页面，非常适合那种模式化的网站
- 服务器压力测试,用**apache-jmeter-2.10**,短小精悍易上手，实测Django自带的测试服务器应对百位数的并发还是应付得来的。
- Django的Admin管理页面给了我们通过浏览器来控制、查看后台数据库的可能性，管理页面可以定制，非常方便，实际上这次我没有写任何的HTML代码，完全利用Admin来进行数据维护。
- [Django的入门教程](https://docs.djangoproject.com/en/1.6/intro/tutorial02/)是学习使用Admin的绝佳例子
- `python manage.py runserver --noreload 0.0.0.0:8000`  
(1)其中的--noreload是告诉Django服务器启动后，如果我们改动了python代码，服务器不会自动的更新，而是使用内存中加载进去的python代码.
(2)0.0.0.0是让Django能够接收全部的ip地址，而不是只能使用127.0.0.1

###设计师应该知道的iPad和iPhone设计知识  
- 权威中得权威 甚至带有一定的潮流导向 [iOS Human Interface Guidelines](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html#//apple_ref/doc/uid/TP40006556)
- 最基本的分辨率的知识，[民间总结](http://www.iosres.com/)和[官方文档](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/mobilehig/IconMatrix.html)
- 需不要状态栏，状态栏的高度是多少，如果有状态栏，那么设计的时候要减去状态栏的高度，很容易忽略
- 按钮需不需要多种状态，如果需要，要为每种状态设计不同样式图片
- 命名的知识，@2x这些，让程序员来弄是很麻烦的，提前和程序员沟通好或者让程序员帮着写一个小得脚本来命名图像，程序员是很乐意的，但是你丢给他一堆命名混乱的图片，不骂娘才怪
- 图像的各种编码方式，JEPG，PNG8，PNG24，网页的时候应该用什么，iOS中又该用什么

###程序员应该知道的美术知识  
- **忘掉一切审美** 我没有开玩笑，开发程序是不需要审美的，我们常常陷入一种误区，就是写代码的时候考虑太多UI的内容，包括动画，交互流程，这些问题非常容易让我们陷入细节的泥潭中，浪费了大量的时间，到时候产品经理一句话，界面一改，你这些都是白忙活。架构快速成型，细节决定成败，细节多了，必败！
- **先让程序能跑，再让它跑得快，最后才是跑的帅气！**
- 忘掉前两句话，因为我要得继续往下写……
- 用到最多的是图片，花一点时间了解一下**图像透明度（alpha）**的知识，非常有用
- 还有时间就再了解一下AE动画是怎么做出来的，可以让设计师演示给你看，包括主要是关键帧动画，位移旋转缩放，美术设计的工作其实也有点乏味
- 多跟美术师互动，他们的工作中有大量重复性的内容，试着写点脚本帮助他们，绝对带来意想不到的效率


###实践经验  
- 动手之前，先思考，思考同时，要记录
- 自己的小项目也写好接口，前后台的接口都写在一份文档里，输入输出都写清楚
- 最好的文档一定是官方的文档，如果你遇到问题了，好久也不能解决，去看看吧，从不会让你失望
- 使用Evernote记录所有的开发中遇到的问题、备案、问题的解决方法，学习到得知识

