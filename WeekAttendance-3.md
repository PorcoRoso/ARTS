## Week Attendance 3.

### Algorithm
---
LeetCode 53. Maximum Subarray
题目：给定一个数组，数组有正有负，求数组中最大的子数组的和。
例子：
```
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```
思路：本题思路比较多，但最近在学习分治思想，因此可以考虑下这种做法。
1、对数组求最大子数组的和，可分解为求左半部分的最大值，右半部分的最大值，及左右合并后的最大值。
2、每次处理问题是上次的一半，时间复杂度为0(NlogN)
```go
package divideConquer

func maxSubArray(nums []int) int {
	if len(nums) == 1 {
		return nums[0]
	} else {
		return maxSubArrayDivide(nums, 0, len(nums)-1)
	}
}

func max(a, b int) int {
	if a > b {
		return a
	} else {
		return b
	}
}

// 分为三部分：左半部分最大值，右半部分最大值，左半部分右 + 右半部分左
// 分析参考：https://blog.csdn.net/HaixWang/article/details/80719804
func maxSubArrayDivide(nums []int, from, to int) int {
	if from == to {
		return nums[from]
	}
	middle := (from+to) / 2
	left := maxSubArrayDivide(nums, from, middle)
	right := maxSubArrayDivide(nums, middle+1, to)

	// 左半部分的最大 + 右半部分最大
	// leftEnd从middle处往左累加，leftSuffix记录这其中的最大值
	leftEnd, leftSuffix := nums[middle], nums[middle]
	rightBegin, rightPrefix := nums[middle+1], nums[middle+1]

	for i := 1;middle - i >= from; i++ {
		leftEnd += nums[middle - i]
		leftSuffix = max(leftSuffix, leftEnd)
	}

	for i := 2;middle + i <= to; i++ {
		rightBegin += nums[middle+i]
		rightPrefix = max(rightPrefix, rightBegin)
	}

	join := leftSuffix + rightPrefix
	return max(left, max(right, join))
}
```

### Review
---
Must be time for a new phone.
文章中，作者探讨了`什么时候需要购买一台新手机`这一主题。
作者手机价值是手提电脑的2倍，这已经是一台archaic（古老的）机器了，作者觉得已经满足自己通讯的诉求了，但是这并不是手机厂商锁希望的。由此，抛出问题：How could I still be using such an old phone? （怎么能还在用这个老手机？）
确实作者的手机已经有点不够便捷而且每天需要充电，作者也在想这也许该是换新手机的时间了，此刻矛盾了：要么忍受，要么享受。这引发了作者的思考：`How fast does it need to be? How many features do we need? How old is too old?要多快？要多少新功能？多老才算老？`
首先，作者认为手机功能还算好，能打电话，能发短信；
其次，作者是最小需求主义者，只保留有用的APP，比如健身、音乐、日历、协作软件等。
但是，新手机并不能点燃他。因为从多个数字化场景，作者都能看到这点。小时候并没有这个，爸爸也不会在另一件屋子里用短信叫他吃饭；朋友也都移除了社交APP或禁用通知功能；作者多年使用IT产品，并不想回到使用纸质地图的年代。但是，是否可以喘口气，手机、游戏机、耳机，都停一停。

原地址：https://medium.com/@JThorn/must-be-time-for-a-new-phone-c23d2d7cb821

### Tips
---
接触go一段时间后，安利给同事，需要协作一个项目，原本的go代码包依赖问题越来越突出，每次我都是增量加包，并不痛苦，但是对于他来说，一个接一个的依赖，甚是难受。因此思考Java通过pom来管理依赖，go下怎么管理？
遂百度之，发现了go mod工具，基本上使用了下，确实可以解决问题。
在此，反思一个问题，还是要稍微想着怎么懒，怎么方便，不然无法提高生产力！


### Share
---
今天从medium中找了一篇科技相关的文章，一改之前翻译的风格，试着去理解文章的意思，看不懂的单词先google，获得大意后，再将整段google，纠正自己的理解，发现在平时看英文时，会忽略一些小单词的含义，往往会使自己的理解偏差极大，以后需要注意不能忽视一个小单词。



