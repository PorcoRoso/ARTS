## Week Attendance 5.

### Algorithm
---
DP专场
leetcode 746. Min Cost Climbing Stairs
#### Problem：
爬楼梯，可以爬一层，也可以爬两层；假设cost数组是你每一步的消耗，找到最小消耗的到达楼顶的方式。
#### Example：
##### Example 1:
Input: cost = [10, 15, 20]
Output: 15
Explanation: Cheapest is start on cost[1], pay that cost and go to the top.

##### Example 2:
Input: cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
Output: 6
Explanation: Cheapest is start on cost[0], and only step on 1s, skipping cost[3].
#### Solution
```go
func min(x, y int) int {
	if x < y {
		return x
	} else {
		return y
	}
}
// dp
// step[i] = min(step[i-1]+cost[i-1], step[i-2]+cost[i-2])
func minCostClimbingStairs(cost []int) int {
	step := []int{}
	step = append(step, 0)
	step = append(step, 0)
	for i := 2; i <= len(cost); i++ {
		tmp := min(step[i-1]+cost[i-1], step[i-2]+cost[i-2])
		step = append(step, tmp)
	}

	return step[len(cost)]
}
```
leetcode 139. Word Break
#### Problem
给定一个字符串和一个词典，判断这个字符串是否可以分割成词典里的单词。
##### Example 1
Input: s = "leetcode", wordDict = ["leet", "code"]
Output: true
Explanation: Return true because "leetcode" can be segmented as "leet code".
##### Example 2
Input: s = "applepenapple", wordDict = ["apple", "pen"]
Output: true
Explanation: Return true because "applepenapple" can be segmented as "apple pen apple".
             Note that you are allowed to reuse a dictionary word.
##### Example 3
Input: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
Output: false
#### Solution
```go
func isContain(s string, wordDoct []string) bool {
	for _, v := range wordDoct {
		if s == v {
			return true
		}
	}
	return false
}

// catsand, ["cats", "and"]
// dp[7] = dp[6] && s[6:7], dp[5] && s[5:7], ..., dp[0] && s[0:7]中任一位true，则dp[7]为true
func wordBreak(s string, wordDict []string) bool {
	dp := make([]bool, len(s)+1)
	dp[0] = true
	for i := 1; i <= len(s); i++ {
		for j := 0; j < i; j++ {
			dp[i] = dp[j] && isContain(s[j:i], wordDict)
			if dp[i] { // 只要存在一个，即表示满足条件
				break
			}
		}
	}
	return dp[len(s)]
}
```
leetcode 213. House Robber II
#### Problem
打劫房子，一条街上的房子，每间都存有一定的金币，所有的房子以一个环的方式围在一起，首尾相连。如果抢劫两家相邻的房子，则会触发报警，问怎么才能在不触发报警的情况下，抢到最多的金币？
##### Example 1
Input: [2,3,2]
Output: 3
Explanation: You cannot rob house 1 (money = 2) and then rob house 3 (money = 2),
             because they are adjacent houses.
##### Example 2
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
#### Solution
1、dp[i] = max(dp[i-1], dp[i-2]+nums[i])，但这题扩展了下，要求首尾不能同时被抢，因为首尾也算相邻；
2、则解决方案为：分别去掉第一个房间和最后一个房间，两种情况里的最大者
3、去掉第一个房间：[1,n-1)
4、去掉最后一个房间：[2,n)
```go
func rob(nums []int) int {
	if len(nums) == 0 {
		return 0
	} else if len(nums) == 1 {
		return nums[0]
	}
	return max(robFrom(nums, 0, len(nums)-1), robFrom(nums, 1, len(nums)))
}

func robFrom(nums []int, left, right int) int {
	if right - left <= 1 {
		return nums[left]
	}
    dp := []int{}
	dp = append(dp, nums[left])
	dp = append(dp, max(dp[0], nums[left+1]))

	for i := left+2; i < right; i++ {
		dp = append(dp, max(dp[len(dp)-1], nums[i]+dp[len(dp)-2]))
	}
	return dp[len(dp)-1]
}

func max(a, b int) int {
    if a > b {
        return a
    } else {
        return b
    }
}
```
## Review

---
本周正好因为要用到fabric里的ACL，因此翻译总结了下官方关于ACL的介绍。https://github.com/PorcoRoso/ARTS/blob/master/review/aclDetails.md

原地址：https://hyperledger-fabric.readthedocs.io/en/release-1.2/access_control.html

### Tips
---
go slice几种用法
1、arr := [CONST]int{} CONST必须是常量，不可以是现求的数组长度len(nums)这种；
2、var arr []int = make([]int, len(nums)) make一个切片，长度是nums数组的长度；
3、arr := []int{} arr = append(arr, 1)

### Share
---
最近在总结个人的知识体系，感觉每一块的都知道一点，但是不成体系。导致一个问题：一看觉得懂，一问就打鼓，一用就糊涂。希望今年能把知识点补全。
![知识体系](https://github.com/PorcoRoso/ARTS/blob/master/share/knowledgSstructure.jpg)









