---
title: 动态规划:纸牌博弈
categories: 动态规划
tags: [算法,动态规划] 

---
<script type="text/javascript"
   src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

**转载请注明出处**：[blog.justkitt.com](http://blog.justkitt.com)

# 问题定义
有一组数nums，两个人A和B，这两个人在玩一个游戏。假设A先，A从nums中的第一个数或最后一个数中选一个，然后B从剩下的nums中的第一个数和最后一个数选一个，如此往复直到nums中不剩数。这两个人都非常聪明，他们都会采取最优策略保证自己拿到的最终结果最大。问题是这两个人的最大的结果是多少。
<!-- more -->

# 思路
首先要确定这是个动态规划的问题，为什么可以得到这个结论？我们来找一些规律就知道了。对于nums是[-1,0,100,2]来说，我们假设A取了-1，那么B就会取[0,100,2]中的最优解，如果A取了2，那么B就会取[-1,0,100]中的最优解。所以A取-1还是2取决于[0,100,2]和[-1,0,100]这两个情况中，A作为后选人能选到的最大值是多少。这里就需要有两个状态，``f[i][j]``表示先选的那个人在``nums[i-j]``这段能选到的最大值，``s[i][j]``表示的是后选的那个人在``nums[i-j]``这段能选到的最大值。那么就有如下状态转移方程：
$$
f[i][j] = max(nums[i]+s[i+1][j],nums[j]+s[i][j-1])
s[i][j] = min(f[i][j-1],f[i+1][j])
$$
上面两个方程我再解释一下：
- 第一个表示我作为先选的人，我在i，j这段选择时，我选择nums[i]还是nums[j]取决于在选完之后，我作为后选的人，在[i+1,j]和[i,j-1]这两段中能选得的最大值。
- 第二个表示我作为后选的人，因为之前的人很聪明，我肯定选的是先前那个人选择的剩下的情况，即两段中最小的那种情况。

# 代码
有了如上状态转移方程，我们的代码就比较好写了。代码如下：
```C++
int cardGame(vector<int>& nums){
	int n = nums.size();
	vector<vector<int>> f(n,vector<int>(n,0));
	vector<vector<int>> s(n,vector<int>(n,0));
	for(int j = 0;j<n;++j){
		dp[j][j] = nums[j];
		for(int i = j-1;i>=0;--i){
			f[i][j] = max(nums[i]+s[i+1][j],nums[j]+s[i][j-1]);
			s[i][j] = min(f[i][j-1],f[i+1][j]);
		}
	}
	return max(f[0][n-1],s[0][n-1]);
}
```