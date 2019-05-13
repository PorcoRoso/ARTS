## Week Attendance 7.

### Algorithm
---
leetcode 152. Maximum Product Subarray
#### Problem：
给定一个数组，找子数组使得数组元素乘积最大，子数组是连续子数组。
#### Example：
##### Example 1:
Input: [2,3,-2,4]
Output: 6
Explanation: [2,3] has the largest product 6.

##### Example 2:
Input: [-2,0,-1]
Output: 0
Explanation: The result cannot be 2, because [-2,-1] is not a subarray.
#### Solution
解法1： 传统解法，两个指针作为子数组的首尾，暴力遍历
解法2： 动态规划，因为有负数乘以负数的情况，所以会导致突然一个很小的负数，遇到了另一个负数，结果很大，如-2，4，-8
        dp_max[i] = max(dp_max[i-1] * A[i], dp_min[i-1] * A[i], A[i])
        dp_min[i] = min(dp_max[i-1] * A[i], dp_min[i-1] * A[i], A[i])
解法一的结果耗时88ms,2.7M内存
解法二的结果耗时4ms,2.9M内存
- 解法一

```go
func maxProduct(nums []int) int {
	if len(nums) < 1 {
		return 0
	}
	max := math.MinInt8

	for i := 0; i < len(nums); i++ {
		curMax := 1
		for j := i; j < len(nums); j++ {
			if i == j {
				curMax = nums[i]
			} else {
				curMax *= nums[j]
			}

			if curMax > max {
				max = curMax
			}
		}

	}
	return max
}
```
- 解法二

```go
func maxProduct(nums []int) int {
	if len(nums) < 1 {
		return 0
	}
	if len(nums) == 1 {
		return nums[0]
	}
	var dp_min []int = make([]int, len(nums))
	var dp_max []int = make([]int, len(nums))
	dp_min[0] = nums[0]
	dp_max[0] = nums[0]
	max_global := nums[0]
	for i := 1; i < len(nums); i++ {
		dp_max[i] = max(max(dp_max[i-1]* nums[i], dp_min[i-1] * nums[i]), nums[i])
		dp_min[i] = min(min(dp_max[i-1]* nums[i], dp_min[i-1] * nums[i]), nums[i])
		max_global = max(dp_max[i], max_global)
	}

	return max_global
}

func max(a , b int) int {
	if a > b {
		return a
	} else {
		return b
	}
}

func min(a , b int) int {
	if a > b {
		return b
	} else {
		return a
	}
}
```
## Review

---
The Microsoft Nadellaissance
### Part 1
微软CEO Nedellaissance，微软2019年4月已经成为第三家市值过万亿的美国公司，今年股票表现耀眼。在Nedella的带领下，微软从软件巨头转入云，用户比Netflix更多，云计算收入也高于Google，与过去的表现大相径庭。
在Bill Gates时代的治理和担心成为“邪恶帝国”已经成为过去，微软实际上在商业和技术上比Google，Facebook或Amazon更具伦理力量。管理者不会做出有疑问的决定，员工也不必每年抗议，没有顶级人才和高管的外流，也没有关于工作条件的争议。
### Part 2
Nadella51岁，拥有多个学位的工程师，以其图书管理员式的气质而为人所知。他做出了很多对的决定，对微软在云计算领域的感觉把握良好，就像一个很成熟的公司一样。
他今年一直在推进云产品，目标是赶上AWS的份额。核心产品Azure现在仅次于Amazon云服务，商业和企业合作伙伴众多。
We have to give credit where credit is due.Nadella五年来的转变一直是历史性的。
微软的最新财报显示，拆分业务的三个主要部门都表现良好，本季度大致贡献了相同的收入（每个月30%）
- Offic，LinkedIn，Dynamics 10.2billion
- Azure Cloud,服务器产品，企业服务 9.7
- Windows，Xbox,Surface 10.7

这意味着最多元化的商业模型凸显，游戏，网络安全，Hololens2，连接商业，开源软件。
微软奇怪的文化重组已经完成。 基本上，纳德拉表现出他所谓的公司“同理心”，以及他的团队从“固定思维模式”转变为“成长思维模式”。
### Part 3
Nadella运营微软的方法是扎实的，像睿智的祖父。尽管在一些列产品，WindowsPhone,WindowsRT,XboxOne上取得成功也因此振兴微软，Nadella不想让员工在成功中过得太舒服。这些产品是棒的，但是也是全球的，未来充满竞争。
所以Nadella的商业精明（savvy）在哪呢？Nadella砍掉对Windows的资金支持，用于构建巨大的云计算商业，过去数年回报34billion，并领先于Google，并在关键领域对抗主要的玩家AWS(Amazon Web Service)
Azure，微软的云平台，赢得了像ExxonMobil,Starbucks,Walmart这样的巨头客户。此外，LinkedIn和GitHub也是其麾下巨头，Azure理论上可以挑战AWS了。
Nadella谈吐和性情温柔，所以你不必把他当做微软第二次出现的Darth Vader，但他可能就是这样。他可能挽救了公司，这是史诗般行为。当董事会任命他为Ballmer的继任时，微软看起来被Windows的衰落所困，而Windows曾经在顶点时市场份额达到90%以上。
Nadella使自谦看起来非常复杂（sophisticated），记住他来自哪儿。Nadella从向公司买家售卖PC开始，在接管Azure前，管理Bing项目（搜索引擎）。其余的则皆是历史了。

原地址：https://medium.com/futuresin/the-microsoft-nadellaissance-95b52432f450

### Tips
---
本周把公司内网跟自己的Mac进行了打通，过程蛮曲折，主要是不懂得原理，关于原理这块择机会写篇文章作为share。这里只贴出关键点作为tip:
1、要想访问内网某台服务器，首先你得有个大致的网络拓扑图；
2、我就是遇到没有的情况，则就需要根据现有资源试了，使用了`sudo  route -n add -net 172.12.18.1 172.11.11.11`命令进行添加，但是会失败
3、这里得确认一个问题，你要添加的网络，比如说172.11.10.110必须跟上面那个网关在同一个网络里，不在则会失败，只能去找那个网关
4、网关怎么找，`traceroute 172.11.10.110`根据这中间的每一跳去试
5、在添加的过程中，因为是去试的，所以每次添加之前，都要`sudo  route delete 172.11.10.1`删除之前的，不然报错这个路由已经在路由表里有了，添加不成功
基本上，上述5步，能够把你从茫然无措中解放出来，当然最好还是要去复习下网络关于``ping``和``traceroute``这两个命令以及背后的ICMP协议。

### Share
---
中间五一，休了一周，本来前面都准备好了，但是share部分没有写完，所以就拖了一周。
本周带来的share是[golang的方法笔记和实践](https://github.com/PorcoRoso/ARTS/blob/master/share/golangMethodNotes.md)，具体是在码代码的时候，看到了文中的一个疑问，遂开始了探究，过程挺好玩，就是费时间。











