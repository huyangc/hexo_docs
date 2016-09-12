---
title: 动态规划:不同子序列（Distinct Subsequences）
categories: 动态规划
tags: [算法,动态规划] 

---
<script type="text/javascript"
   src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

**转载请注明出处**：[blog.justkitt.com](http://blog.justkitt.com)

# 问题描述
有两个字符串s和t，找出s的所有不同的子序列，这些子序列是t。例如对于s="rabbbit",t = "rabbit"，那么s中去掉三个b的随便一个都可以构成t，所以s的所有不同的子序列是t的个数是3。
<!-- more -->
# 解题
## 思路
字符串子序列的问题一般都是动态规划的问题（by娇哥），那么动态规划的问题就分三步开始
### 首先定义状态（即定义动态规划的状态存储数组dp）
用一个二维数组来表示状态（非常自然，因为有两个字符串）dp[i][j]，其中i表示的是t字符串的长度，j表示的是s字符串的长度。其意义表示：s[0-j]中的子序列是t[0-i]的不同子序列的个数。
### 其次定义状态转移方程
状态转移方程的思想是当前子序列的个数dp[i][j]肯定至少是s在上一个位置时的子序列的个数，如果s和t在i和j位置有相同字符的话，那么其子序列的个数应该是上一个位置的个数加上dp[i-1][j-1]这个位置的时候的个数。所以有如下状态转移方程：
$$
dp[i][j] =
        \\begin{cases}
        dp[i][j-1],  & \\text{if t[i-1] \!= s[j-1]} \\\\
        dp[i][j-1]+dp[i-1][j-1], & \\text{if t[i-1] = s[j-1]}
        \\end{cases}
$$
### 定义初始状态和终止状态
对于dp[0][0]，有个空串，所以是1
对于dp[0][1-m]，至少有个空串，所以是1
对于dp[1-n][0]，t至少有一个字符，但是s没有字符，所以是0
应该返回的是dp[n][m]

### 代码
```C++
int distinctSubseq(string& s,string& t){
	int n = t.size();
	int m = s.size();
	vector<vector<int>> dp(n+1,vector<int>(m+1));
	dp[0][0] = 1;
	for(int i = 1;i<=n;++i){
		dp[i][0] = 0;
	}
	for(int i = 1;i<=m;++i){
		dp[0][i] = 1;
	}
	for(int i = 1;i<=n;++i){
		for(int j = 1;j<=m;++j){
			dp[i][j] = dp[i][j-1]+(t[i-1] == s[j-1]?dp[i-1][j-1]:0);
		}
	}
	return dp[n][m];
}
```