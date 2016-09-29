---
title: 动态规划:最长递增子序列
categories: 动态规划
tags: [算法,动态规划] 

---
<script type="text/javascript"
   src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

**转载请注明出处**：[blog.justkitt.com](http://blog.justkitt.com)

# 问题描述
求一个数组的最长递增子序列长度，例如给定一个数组{1,-1,2,-3,4,-5,6,-7}，那么其最长递增子序列（之一）为：{1,2,4,6}，长度是4。
<!-- more -->
# 动态规划解法一
动态规划的问题，首先就要知道状态是什么，怎样定义状态，在这个问题中，我们定义状态dp[i]表示以nums[i]结尾的最长递增子序列的长度。所以下面的思路就非常自然而然了。
**思路：**考虑到一个数组的最长子序列和其子数组之间的关系，即对于数组[1,-1,2,-3,4,-5,6]它的最长子序列和整个数组的最长子序列之间的关系，就看-7这个元素和前面的数的大小比较，如果大的话，那么是相应子序列的长度+1，否则就和原来的最大长度一样。

## 状态转移方程
$$
dp[i] =
        \\begin{cases}
        1,  & \\text{for k in [0,i] && nums[k]>nums[i]} \\\\
        max(dp[k])+1, & \\text{for k in [0,i] && nums[k]<nums[i]}
        \\end{cases}
$$
## 边界条件
dp[0] = 0
ret = dp[nums.size()]
## 代码
```C++
int LIS(vector<int>& nums){
	vector<int> dp(nums.size(),1);
	for(int i = 0;i<nums.size();++i){
		int maxLis = 0;
		for(int j = 0;j<i;++j){
			if(nums[i]>nums[j]){
				if(maxLis<dp[j])
					maxLis = dp[j];
			}
		}
		dp[i] = maxLis+1;
	}
	int ret = 0;
	for(int i = 0;i<dp.size();++i){
		ret = max(ret,dp[i]);
	}
	return ret;
}
```
# 动态规划解法二
**思路：**首先考虑到最长递增子序列一定是数组排好序版本的一个子序列，也就是说，对于数组{1,-1,2,-3,4,-5,6,-7}，它的排序版本：{-7,-5,-3,-1,1,2,4,6}，那么[1,2,4,6]必然是后一个数组的其中之一子序列。所以这个问题就可以转变成求一个数组和它的排好序的版本的数组的[最长公共子序列问题](http://blog.justkitt.com/2016/08/08/LCS/)。