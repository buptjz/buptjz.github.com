---
layout:       post
title:        "C++ String的用法"
description: 
banner: 
categories: 
- 语言
---
<br />

##Level-1  基础知识
####基本使用

string类型支持长度可变的字符串。需要使用string要引入

```c
#include <string>
using std::string
```



####string的定义和初始化

```c
string s1;				//默认构造函数，s1是空串
string s2(s1);			//将s2初始化为s1的一个副本
string s3("value");		//将s3初始化为一个字符串字面值副本
string s4(n,'c');		//将s4初始化为字符串'c'的n个副本
```


####string读与写

```c
string s;				//空字符串
cin >> s;				//读取string写入s，忽略开头所有的空白格，读取字符直到再次遇到空白符
cout << s << endl;		//写到output中
```


####string的常用操作
<div class="row">
    <div class="span2">
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

- string对象的加法被定义为**连接**，可以使用+和+=操作符
- string对象的关系操作符，这些操作符实际上是比较每个string对象的字符
- 下标操作数可用作左值，例如str[index] = '*'


####string中字符的处理
- isalnum(c)判断是否是字母
- isalpha(c)判断是否是数字
- isupper(c)判断是否是大写
- tolower(c)如果是大写，返回小写形式
- 还有一些，请查表，不一一例举

---
##Level-2  使用进阶
###眼花缭乱的string find 函数
string 提供了非常丰富的查找函数
<table>
   <tr>
      <td>函数名 </td>
      <td>描述&#160;</td>
   </tr>
   <tr>
      <td>find</td>
      <td>查找</td>
   </tr>
   <tr>
      <td>rfind</td>
      <td>反向查找</td>
   </tr>
   <tr>
      <td>find_first_of</td>
      <td>查找包含子串中的任何字符，返回第一个位置</td>
   </tr>
   <tr>
      <td>find_first_not_of</td>
      <td>查找不包含子串中的任何字符，返回第一个位置</td>
   </tr>
   <tr>
      <td>find_last_of</td>
      <td>查找包含子串中的任何字符，返回最后一个位置</td>
   </tr>
   <tr>
      <td>find_last_not_of</td>
      <td>查找不包含子串中的任何字符，返回最后一个位置</td>
   </tr>
</table>
所有的查找函数都返回一个size_type类型，这个返回值一般都是所找到字符串的位置如果没有找到，则返回string::npos

有一点需要特别注意，所有和string::npos的比较一定要用string::size_type来使用，不要直接使用int 或者unsigned int等类型

**其实string::npos表示的是-1**

###截取子串 substr
Returns a newly constructed string object with its value initialized to a copy of a substring of this object.

The substring is the portion of the object that starts at character position pos and spans len characters (or until the end of the string, whichever comes first).

很简单，就是从S的某个位置开始截取指定长度的字符串的一个副本，请看下面这例子

```c
string str="We think in generalities, but we live in details.";
string str2 = str.substr (12,12);   // "generalities"
unsigned pos = str.find("live");    // position of "live" in str
string str3 = str.substr (pos);     // get from "live" to the end
cout << str2 << ' ' << str3 << '\n';
```

基本的操作+查找+子串，基本涵盖了我们日常使用80%，更多内容请见参考

##Level-3  常用高级方法

###反转

```c
std:: void reverse(s.begin(), s.end());//原地反转
s1.assign(s.rbegin(), s.rend());//反转到别的 string 里面，一样简单：
```

###参考
- C++ Primer 中文版第4版
- [Nicolai M.Josuttis C++ string 用法详解](https://www.byvoid.com/blog/cpp-string/)
- [substr](http://www.cplusplus.com/reference/string/string/substr/)




