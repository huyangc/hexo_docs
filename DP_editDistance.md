---
title: 动态规划:字符串编辑距离(edit distance)
categories: 动态规划
tags: [算法,动态规划] 

---
<script type="text/javascript"
   src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

**转载请注明出处**：[blog.justkitt.com](http://blog.justkitt.com)

# 问题描述
有两个字符串A和B，现在将A经过三种变换可以得到B，即插入、删除和修改，这三种操作的代价分别为c0,c1和c2，问题就是A到B的变换所需要的最小代价是多少。
<!-- more -->
# 思路
典型的动态规划问题，娇哥曾经说过，字符串的问题大部分都是动态规划的问题，那么这个问题要怎么解决呢？动态规划问题首先定义状态，然后定义状态转移方程，然后确定初始状态和终止状态，然后就可以得到终止状态下的结果输出。

## 定义状态
我们定义状态``dp[i][j]``表示A从0-i和B从0-j这两段的最小编辑距离。状态的定义需要注意的是，状态只能和前面的情况有联系不能和后面的情况有联系。

## 转移方程
有了状态，那么就定义状态转移方程，状态转移方程的定义有时候会需要“找规律”，但是这道题的规律比较明显，很容易知道，当``A[i] == B[j]``的时候，从``dp[i-1][j-1]``到``dp[i][j]``是不需要任何编辑的，所以``dp[i-1][j-1] = dp[i][j]``，但是当他们不相等的时候，就需要考虑是插入、删除还是编辑的代价最短了，那么自然而然地有三种情况：插入，删除和编辑。插入的情况是什么？
- 插入是A在和B的前j-1个比，然后再在A的基础上进行插入一个字符，插入的字符是B的第j位，所以插入的代价是``dp[i][j-1]+c0``
- 删除是A的前i-1个和B的j个比，因为把A删除了一个字符，所以删除的代价是``dp[i-1][j]+c1``
- 编辑是A的前i-1个和B的j-1个比，然后把A的第i位变成B的第j位。所以编辑的代价是``dp[i-1][j-1]+c2``

由以上分析，可以得到状态转移方程如下：
$$
dp[i][j] =
        \\begin{cases}
        dp[i-1][j-1],  & \\text{if A[i-1] == B[j-1] } \\\\
        min(min(dp[i][j-1]+c0,dp[i-1][j]+c1),dp[i-1][j-1]+c2) & \\text{other}
        \\end{cases}
$$

# 代码
有了状态转移方程，代码就很好写了。代码如下
```C++
int findMinCost(string A, int n, string B, int m, int c0, int c1, int c2) {
    vector<vector<int>> dp(n+1,vector<int>(m+1,0));
    for(int i = 1;i<=m;++i){
        dp[0][i] = dp[0][i-1]+c0;
    }
    for(int i = 1;i<=n;++i){
        dp[i][0] = dp[i-1][0] + c1;
    }
    for(int i = 1;i<=n;++i){
        for(int j = 1;j<=m;++j){
            if(A[i-1] == B[j-1])
                dp[i][j] = dp[i-1][j-1];
            else
                dp[i][j] = min(min(dp[i-1][j]+c1,dp[i-1][j-1]+c2),dp[i][j-1]+c0);
        }
    }
    return dp[n][m];
}
```
