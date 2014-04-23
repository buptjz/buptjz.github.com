---
layout:       post
title:        "【算法总结】排列组合问题与回溯法"
description: 
banner: 
categories: 
- algorithm
---

<br />


###学以致用
DX同学遇到了一个实际的问题，一共90个数找到N个数的和是一个确定的数，使用回溯法解决了这个90个数的组合问题，相当有成就感！感叹变成的力量太强大了！
[工程在这里](https://github.com/buptjz/KSum)

---
#排列组合问题与回溯法

###排列组合问题与回溯法核心

排列组合问题都是枚举类型的问题，这类问题的关键是采取怎样的枚机策略保证

- 完整性：不能遗漏任何一种情况
- 准确性：枚举出的一种情况是正确的
- 效率：一般来说不会有太大的效率差异，因为枚举本身输出情况就很多，比如n！中情况

其中最常用的是回溯法(英文是Backtracking)，这篇[演算法笔记](http://www.csie.ntnu.edu.tw/~u91029/Backtracking.html)讲解的比较清晰，基本是依循《算法设计手册》中的回溯法一章整理的。
回溯法的是来解决多维向量的枚举问题，其核心：

- 递归的枚举每个维度的值
- 既然是递归，那就用终止条件。一般的终止条件是找到了满足条件的解，或者已经超出了解的范围，无需再递归下去。
- 找到某一维的候选值。解排列组合的重中之重。常用的技巧是使用标志位来记录某一维是否可用。
- make_move和unmake_move在对每个候选值递归调用前后发生，这里的unmake_move非常的重要，保证了回溯法的正确！


###排列算法Permute举例
#####一.排列不重复的元素

Given a collection of numbers, return all possible permutations. 

{% highlight c %}
void permuteHelper(vector<vector<int>> &retVec,vector<int> &num,vector<int>&tmp,int length,vector<bool>isVisited){
    if ((int)tmp.size() == length) {
        retVec.push_back(tmp);
        return;
    }
    for (int i = 0; i < length; i++) {
        if (isVisited[i]) continue;
        tmp.push_back(num[i]);
        isVisited[i] = true;
        permuteHelper(retVec, num, tmp, length, isVisited);
        tmp.pop_back();
        isVisited[i] = false;
    }
    
}
vector<vector<int> > permute(vector<int> &num) {
    vector<int> tmp;
    vector<vector<int>> retVec;
    int length = (int)num.size();
    vector<bool>isVisited = vector<bool>(length,false);
    permuteHelper(retVec, num, tmp, length,isVisited);
    return retVec;
}
{% endhighlight %}

上述使用backtrack的想法是按位选取，第一位有n个选择，第一位确认后，第二位有（n-1）个选择，依次类推，并且使用了深度优先遍历，正如算法设计手册上说的一样，深度优先遍历能够减少空间的使用，空间的减少带来的问题是我们每次递归后，要做unmake操作，如tmp.pop_back();和isVisited[i] = false;

难点是使用isVisited标志某一个是否使用过了，如果使用过则不再使用

#####二、排列包含重复元素的数组

Given a collection of numbers that might contain duplicates, return all possible unique permutations. 

{% highlight c %}
void permuteUniqueHelper(int level, vector<int>&num, vector<int>&tmp,
                         vector<vector<int> >&ret, vector<bool>&isVisited,int lenght)
{
    if(level == lenght)//递归终止条件
    {
        ret.push_back(tmp);
        return;
    }
    //依次选择该位置上得每一个候选者
    int lastNumber = num[0] - 1;//保证lastNumber的初始值比数组中最小值小
    for(int i = 0; i < num.size(); ++i)
    {
        //如果本候选者和上一个候选者重复，那么跳过这个候选者
        if(isVisited[i] || num[i] == lastNumber)continue;
        isVisited[i] = true;
        lastNumber = num[i];
        tmp.push_back(num[i]);
        permuteUniqueHelper(level + 1, num, tmp, ret, isVisited,lenght);
        isVisited[i] = false;
        tmp.pop_back();
    }
}
vector<vector<int> > permuteUnique(vector<int> &num)
{
    vector<vector<int> > retVec;
    sort(num.begin(), num.end());//原地排序
    vector<bool> isVisited(num.size(), false);
    vector<int> tmp;
    int lenght = (int)num.size();//这里的length可以取其它值k，变为n选k的全排列
    permuteUniqueHelper(0, num, tmp, retVec, isVisited,lenght);
    return retVec;
}
{% endhighlight %}

与上一道题非常相似，两点变化：

- 数组先排序
- 使用lastNumber来记录上一个选择的候选者，如果本候选者和上一个候选者一样，那么跳过本候选者。
- （参考一）给出的解法虽然可以，但是不能做n选k的排列问题，只能处理n选n得排列问题

#####三、计算下一个数
Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers. 

######使用字典序法：
对给定的字符集中的字符规定一个先后关系，在此基础上按照顺序依次产生每个排列。例如字符集{1,2,3}，按字典序生成的全排列是:123,132,213,231,312,321
例如：839647521的下一个排列

- Step1：从最右开始，找到第一个比右边小的数字4（因为4<7, 而7>5>2>1），再从最右开始，找到4右边比4大的数字5（因为4>2>1而4<5）。4、5也即从右往左找到的第一个对严格的升序对。
- Step2：交换4、5。
- Step3：倒置5右边的7421为1247.即得到下一个排列839651247.

该方法支持数据重复，且在C++STL中被采用。因为需要交换的次数较多，字典序法的效率较低。



###LeetCode上的相关问题
- [Permutations](http://oj.leetcode.com/problems/permutations/)
- [Permutations II](http://oj.leetcode.com/problems/permutations-ii/)
- [Next Permutation](http://oj.leetcode.com/problems/next-permutation/)
- [Permutation Sequence](http://oj.leetcode.com/problems/permutation-sequence/)
- [Subsets](http://oj.leetcode.com/problems/subsets/)
- [Subsets II](http://oj.leetcode.com/problems/subsets-ii/)
- [Combinations](http://oj.leetcode.com/problems/combinations/)
- [Combination Sum](http://oj.leetcode.com/problems/combination-sum/)
- [Combination Sum II](http://oj.leetcode.com/problems/combination-sum-ii/)

###参考
- [Crystal的关于leetCode上的排列组合问题整理](http://cuijing.org/interview/summary-of-permutation-and-combination-in-leetcode.html)
- [演算法笔记之Backtracking](http://www.csie.ntnu.edu.tw/~u91029/Backtracking.html)