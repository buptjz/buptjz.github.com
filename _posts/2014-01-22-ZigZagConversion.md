---
layout:       post
title:        "【算法】ZigZag Conversion"
description: 
banner: 
categories: 
- algorithm
---

<br />

#ZigZag Conversion

[leetcode上的一道题](http://oj.leetcode.com/problems/zigzag-conversion/)

###**题目描述:** 
 The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

P # A # H # N

A P L S I I G

Y # I # R

And then read line by line: "PAHNAPLSIIGYIR"

Write the code that will take a string and make this conversion given a number of rows:

string convert(string text, int nRows);

convert("PAYPALISHIRING", 3) should return "PAHNAPLSIIGYIR". 

###**解析:**

最直观的想法是按照题目的意思，真的把数组按照Z字扫描排列，再横着扫描一遍了事。
但是你画出来几个例子之后，会找到两种扫描结果对应的索引关系，具体的公式请读者自行推断


###答案：

以下的代码在本地运行没有问题，但是在leetCode中会出现如下的错误

Input: 	"ABCD", 2

Output: 	"ACBD,P�"

Expected: 	"ACBD"

```
string convert(std::string s, int nRows) {
    int length = (int)s.length();
    if (length == 0 || nRows == 1) return s;//边缘条件又忽略了 "A", 1
    
    int isMid;
    int dis = 2 * nRows - 2;
    int index;//原数组的下标
    int count = 0;//返回数组的下标
    char *retChar = (char *)malloc(sizeof(char) * length);
    
    for (int i = 0; i < nRows; i++) {
        index = i;
        isMid = false;
        while (index < length) {
            retChar[count++] = s[index];
            if (i == 0 || i == nRows -1) {//第一行和最后一行
                index += dis;
            }else{
                if(isMid) index += 2 * i;
                else index += dis - 2 * i;
                isMid = !isMid;
            }
        }
    }
    string ret = string(retChar);
    return ret;
}

```
**具体原因尚不明确！**
考虑到用到char来生成string可能导致结束字符出错了，做了改动之后最终的代码如下：

```
using namespace std;
string convert(std::string s, int nRows)
{
    int length = (int)s.length();
    if (length == 0 || nRows == 1) return s;//边缘条件又忽略了 "A", 1
    
    int isMid;
    int dis = 2 * nRows - 2;
    int index;//原数组的下标
    int count = 0;//返回数组的下标

    string ret = string(s);
    for (int i = 0; i < nRows; i++) {
        index = i;
        isMid = false;
        while (index < length) {
            ret[count++] = s[index];
            if (i == 0 || i == nRows -1) {//第一行和最后一行
                index += dis;
            }else{
                if(isMid) index += 2 * i;
                else index += dis - 2 * i;
                isMid = !isMid;
            }
        }
    }
    return ret;
}

```


###需要注意的问题：

- 边缘条件，考虑输入nRows的值为1的情况了没有？
- 做题前，先写测试用例！