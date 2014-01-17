---
layout:       post
title:        "【算法】在特殊二维数组查询"
description: 
banner: 
categories: 
- algorithm
---

<br />

#特定二维数组查询问题

Cracking the Code Interview 11.6
###**题目描述:** 
给一个二维的数组，数组中的每一行和每一列都是递增的，怎样找到某个元素的坐标？
例如下面这种情况：
<div class="row">
    <div class="span4">
        <table>
           <tr>
           		<td>1</td>
           		<td>3</td>
           		<td>5</td>
           		<td>7</td>
           		<td>9</td>
           </tr>
           <tr>
           		<td>2</td>
           		<td>5</td>
           		<td>6</td>
           		<td>8</td>
           		<td>10</td>
           </tr>
           <tr>
           		<td>4</td>
           		<td>5</td>
           		<td><b>7</b></td>
           		<td>10</td>
           		<td>11</td>
           </tr>
           <tr>
           		<td>6</td>
           		<td>6</td>
           		<td>8</td>
           		<td>14</td>
           		<td>15</td>
           </tr>
           <tr>
           		<td>7</td>
           		<td>9</td>
           		<td>12</td>
           		<td>16</td>
           		<td>17</td>
           </tr>
        </table>
    </div>
</div> 
###**解析:**

在这个特定的数组里，很容易想到的是二分查找。例如我们要在数组中找16这个数。在上例中，我们先定位到中间的元素7，16>7,我们继续在7的右侧和下侧寻找，我们可以一直在对角线上找，比如我们再找14，因为16>14，所以在14的右下，继续找17，在17的左上，这样我们就定位到两个新的数组中，如此进行二分查找。

**另一种巧妙的方法**是从右上角开始寻找，这种想法非常巧妙，需要的时间是O(m+n),至于为什么会想到这个呢？因为右上或者左下的点有这样的特点，以右上为例，其下方均是大于它的点，左侧均是小于它的点，意味着我们用它进行比较后，无论情况如何，**每次比较至少能够过滤掉一行**。其实直观上很难想，遇到题还是举例多试一些情况比较好。

具体代码如下：

```

#include <stdio.h>
#include "arrayAndString.h"

void searchInMatrix(int matrix[][5],int width,int height,int target,int *pos_x,int *pos_y){
    //查找到的位置会返回给*pos_x和*pos_y,如果没有找到，返回(-1,-1)
    if (matrix == NULL) return ;
    int x1 = 0;
    int x2 = width - 1;
    while (x2 >= 0 && x1 <= height - 1) {//确保x和y在矩形内
        if (target == *(*(matrix+x1)+x2)){
            //case1: 找到了
            *pos_x = x2;
            *pos_y = x1;
            return;
        }else if(target < matrix[x1][x2]){
            //case2:target要小，在左侧的一个位置去找
            x2--;
        }else{
            //case2:target要大，在下方的一个位置去找
            x1++;
        }
    }
    *pos_x = -1;
    *pos_y = -1;
}

void testSearchInMatric(){
    int a[4][5] ={1,2,3,4,5,2,3,4,6,7,3,4,5,8,9,4,7,8,9,10};
    //显示数组
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 5; j++) {
            printf("%d\t",a[i][j]);
        }
        printf("\n");
    }
    int x,y;//用来保存搜索到的位置
    searchInMatrix(a,5,4,8,&x,&y);
    printf("find in (%d,%d)\n",x+1,y+1);
}
```