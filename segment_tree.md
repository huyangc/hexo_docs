---
title: 数据结构：线段树
categories: 数据结构
tags: [数据结构,线段树] 

---

**转载请注明出处**：[blog.justkitt.com](http://blog.justkitt.com)

在讲具体的数据结构前，首先使用一道算法题来进行数据结构的引入。题目是leetcode上的[307. Range Sum Query - Mutable](https://leetcode.com/problems/range-sum-query-mutable/)，题目大意如下：

<!-- more -->
有一个整形数组nums，找到下标[i , j] 范围内的所有数的和sum，同时，允许更新某个位置上的值，即有一个函数update(int index, int val)表示把nums中index上的那个数更新成val。函数sumRange(int i,int j)用来表示计算从i到j的和。有如下例子：

```
有nums = [1,3,5]
sumRange(0,2) = 9
update(1,2) -> nums = [1,2,5];
sumRange(0,2) = 8
```
在解决这道题的时候，因为有之前那个不可更改的范围和的[例子](https://leetcode.com/problems/range-sum-query-immutable/)，我自然而然地想到还是可以用一个dp数组来保存中间结果，其中``dp[i]``表示的是从0位置开始到i位置的所有数组数据的和，那么``sumRange(i,j)=dp[j]-dp[i-1]``然后为了不要判断边界条件，所以我把dp[0]置为0，这样就可以直接写成``sumRange(i,j) = dp[j+1]-dp[i]``。然后在更新的时候，需要更新的内容就是从i开始到结尾的所有的数。这样的更新时间复杂度是O(n)，然后，超时了。

显然以上方法是不优秀的，然后就开始考虑怎样优化。然后就看到了线段树的内容。线段树的典型构建方式如下图所示：
![线段树](http://images.cnitblog.com/blog/707568/201501/162324097145023.png)

每次都按中间值划分，然后在每个节点中，一般有四个固定的内容：用来标识头尾的start和end，用来标识左右节点的left和right，这是固定内容，剩下的内容可以自行定制。表明在原数组的start-end这段范围内含有什么内容。在这题中，我们要计算的是一个范围的和是多少，所以我们增加一个标志位sum来表示。我们定义线段树的节点为SegmentTreeNode，该节点的定义如下代码所示：

```C++
class SegmentTreeNode{
public:
	int start,end;
	SegmentTreeNode* left;
	SegmentTreeNode* right;
	int sum;   //特殊定制记录信息
	SegmentTreeNode(int start,int end):start(start),end(end),left(nullptr),right(nullptr),sum(0){}
};
```
有了线段树的节点信息，我们需要考虑的就是怎样构建一棵线段树，首先可以看到，一个线段树一般是从中间划分开，即若有``mid = (start+end)/2`` 那么一般该节点的左子结点的start和end是 start mid,然后该节点的右子节点的start和end分别是 mid+1和end，所以我们下面可以通过一个递归的方式进行树的构建。
```C++
SegmentTreeNode* buildTree(vector<int>& nums,int start,int end){
	if(start>end)
		return nullptr;
	root = new SegmentTreeNode(start,end);
	int mid = begin+(end-start)/2;
	if(start == end){
		root->sum = nums[start];
		return root;
	}
	root->left = buildTree(nums,start,mid);
	root->right = buildTree(nums,mid+1,end);
	root->sum = root->left->sum+root->right->sum;
	return root;
}
```
以上代码紧紧跟着之前所描述的左子结点和右子节点的构造和递归过程，然后递归终止条件就是start和end相等，这种情况呢，就是上图中的叶子结点只有一个数的时候。
有了构建树的过程，就有树的查询过程，这种查询过程，就是给一个范围和一个树的根节点，用于数据的查询，所以可以知道函数声明如下：``int queryTree(int i,int j,SegmentTreeNode* root)``，有几种情况下面需要讨论一下：
- 如果root中的start和end刚好就是i和j，那么刚好，直接返回root->sum就好了
- 如果root中的start和end的中间数是比i还小，说明要查询的范围是在root的右边
- 如果root中的start和end的中间数比j还大，那么说明要查询的范围是root的左边
- 如果root中的start和end的中间数在i和j中间，那么说明需要  queryTree(i,mid,root->left)+queryTree(mid+1,j,root->right)

按照以上讨论的情况，很容易得到如下的代码：
```C++
int queryTree(int i,int j,SegmentTreeNode* root){
	if(root == nullptr)
		return 0;
	if(root->start == i&&root->end == j)
		return root->sum;
	int mid = (root->start+root->end)/2;
	if(mid>=j) return queryTree(i,j,root->right);
	if(mid<i) return queryTree(i,j,root->left);
	return queryTree(i,mid,root->left)+queryTree(mid+1,j,root->right);
}

```

然后需要面对的稍微有一点复杂的问题就是更新的问题，更新之前的描述说是把index上的数值变成val，那么这之间必然存在一个``diff = val-oldval``，然后线段树上的那个叶结点的所有祖先节点都需要更新它们的sum值，加上diff。这也是一个递归问题，首先找到某个start和end相等并且等于i的root，然后更新root的sum，然后返回这个diff，然后在递归的每一层，都用``root->sum+=diff``做一个更新就好了。代码如下：
```C++
int modifyTree(int i,int val,SegmentTreeNode* root){
	if(root == nullptr) return 0;
	int diff;
	if(root->start == i&&root->end == i){
		diff = val-root->sum;
		root->sum = val;
		return diff;
	}
	int mid = (root->start+root->end)/2;
	if(mid>i)
		diff = modifyTree(i,root->left);
	else
		diff = modifyTree(i,root->right);
	root->sum+=diff;
	return diff;
}
```

然后这道题就很容易解决了，使用线段树的解决代码如下：
```C++
class SegmentTreeNode{
public:
    SegmentTreeNode* left;
    SegmentTreeNode* right;
    int sum;
    int start,end;
    SegmentTreeNode(int start,int end):start(start),end(end),left(nullptr),right(nullptr),sum(0){}
};
class NumArray {
    SegmentTreeNode* root;
public:
    NumArray(vector<int> &nums) {
        int begin = 0;
        int end = nums.size()-1;
        root = buildTree(nums,begin,end);
    }

    void update(int i, int val) {
        modifyTree(i,val,root);
    }

    int sumRange(int i, int j) {
        return queryTree(i,j,root);
    }
private:
    SegmentTreeNode* buildTree(vector<int>& nums,int begin,int end){
        if(begin>end)
            return nullptr;
        auto root = new SegmentTreeNode(begin,end);
        if(begin == end){
            root->sum = nums[begin];
            return root;
        }
        int mid = begin+(end-begin)/2;
        root->left = buildTree(nums,begin,mid);
        root->right = buildTree(nums,mid+1,end);
        root->sum = root->left->sum+root->right->sum;
        return root;
    }
    int queryTree(int begin,int end,SegmentTreeNode* root){
        if(root == nullptr)
            return 0;
        if(root->start == begin&&root->end == end) return root->sum;
        int mid = (root->start+root->end)/2;
        if(end<=mid) return queryTree(begin,end,root->left);
        if(begin>mid) return queryTree(begin,end,root->right);
        return queryTree(begin,mid,root->left)+queryTree(mid+1,end,root->right);
    }
    int modifyTree(int i,int val,SegmentTreeNode* root){
        if(root == nullptr) return 0;
        int diff;
        if(root->start == i && root->end == i) {
            diff = val - root->sum;
            root->sum = val;
            return diff;
        }
        int mid = (root->start + root->end) / 2;
        if(i > mid) {
            diff = modifyTree(i,val,root->right);
        } else {
            diff = modifyTree(i,val,root->left);
        }
        root->sum = root->sum + diff;
        return diff;
    }
};


// Your NumArray object will be instantiated and called as such:
// NumArray numArray(nums);
// numArray.sumRange(0, 1);
// numArray.update(1, 10);
// numArray.sumRange(1, 2);
```