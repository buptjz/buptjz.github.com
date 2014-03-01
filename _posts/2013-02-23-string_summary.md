---
layout:       post
title:        "C++ String的用法"
description: 
banner: 
categories: 
- 语言
---
<br />

string类型支持长度可变的字符串。需要使用string要引入

```
#include <string>
using std::string
```



###string的定义和初始化

```
string s1;				//默认构造函数，s1是空串
string s2(s1);			//将s2初始化为s1的一个副本
string s3("value");		//将s3初始化为一个字符串字面值副本
string s4(n,'c');		//将s4初始化为字符串'c'的n个副本
```
###string读与写

```
string s;				//空字符串
cin >> s;				//读取string写入s，忽略开头所有的空白格，读取字符直到再次遇到空白符
cout << s << endl;		//写到output中
```
###string的常用操作
<div class="row">
    <div class="span4">
<table>
   <tr>
      <td>s.empty()</td>
      <td>如为空串，返回true，否则false</td>
   </tr>
   <tr>
      <td>s.size()</td>
      <td>返回字符的个数</td>
   </tr>
   <tr>
      <td>s[n]</td>
      <td>返回第n个字符</td>
   </tr>
   <tr>
      <td>s1 + s2</td>
      <td>将s1和s2连接成一个新字符串，返回新生成的字符串</td>
   </tr>
   <tr>
      <td>s1 = s2</td>
      <td>把s1的内容替换为s2的副本</td>
   </tr>
   <tr>
      <td>v1 == v2</td>
      <td>比较两字符串的内容，相等返回true</td>
   </tr>
   <tr>
      <td>!=,<,<=,>和>=</td>
      <td>保持这些字符串的惯有含义</td>
   </tr>
</table>
	</div>
</div>