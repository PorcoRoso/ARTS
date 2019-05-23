## Week Attendance 9.

### Algorithm
---
leetcode 153. Find Minimum in Rotated Sorted Array
#### Problem：
一个升序数组，经过旋转一次后变成了新旋转数组，求数组的最小值。如[0,1,2,4,5,6,7]旋转后可能为 [4,5,6,7,0,1,2]。
#### Example：
##### Example 1:
Input: [3,4,5,1,2] 
Output: 1

##### Example 2:
Input: [4,5,6,7,0,1,2]
Output: 0

#### Solution
解法：二分法，判断在左还是右，left与和中间值比较即可
- 解法一

```go
func findMin(nums []int) int {
    left, right := 0, len(nums)-1

	if nums[left] > nums[right] {
		for left != right -1 {
			mid := (left + right)/2
			if nums[left] < nums[mid] {
				left = mid
			} else {
				right = mid
			}
		}
        return min(nums[left], nums[right])
	}
	return nums[0]
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
Is China Winning the 5G Battle?
比较敏感的文章，从另一个角度来看看别人的想法。作者的想法如下：
铺天盖地的报道声明美国在5G无线技术发展和部署中落后于中国。但实际上，媒体一直都是自愿受害者，他们是被中国政府代理人兜售的国家赞助宣传的受害者。（ps:美国媒体都被中国愚弄了）
中国政府费力宣传的原因是因为“五眼联盟FVEY”禁止华为向FVEY成员提供5G设备。FVEY和CIA披露华为是被中国政府机构所资助的，中国正在寻求说服西方应该重新考虑部署华为产品的禁令，除非西方想落后更多。（ps:中国政府一直在各方游说）
中国在5G方面落后于美国和大多数西方国家。在主要城市，美国已经可以从至少3家以上供应商购买5G连接设备；而中国，仅有三家供应商且计划于2020年提供5G服务，也就是说目前5G在中国是无法购买的。实际上，中国铁塔要到2023年才能完成主要城市的5G部署，且他们并不计划在全国范围内推广，因为部署无处不在的5G完成时，6G已经可能可用了；而美国的5G覆盖部署是在2020年底。（ps:嗯，有这么成熟了么？那华为呢？）与中国的宣传相反，禁止华为不会对这些部署计划造成滞后影响。（ps:5G美国比中国成熟多了，我坐等20年底看到你们全面部署，至今都没什么消息说你们搞了5G设备）
讽刺的是华为禁令会削弱华为输出解决方案的能力，并且显著阻碍中国自己的5G部署。华为的5G方案需要核心组件及来自美国供应商的芯片。由于商务部的出口限制，这些供应商不得不停止向华为售卖零件。华为正在抢着寻找替代方案，而中国政府则试图让美国人相信禁令会削弱美国的电信网络。（ps:华为依赖美国芯片，芯片禁止出口了，会威胁影响美国5G进展）
FVEY成员，包括澳大利亚，加拿大，新西兰，英国，美国都得出结论：在本国通信网络中使用华为5G技术，将会对国家安全带来风险。FVEY一直认为华为在其5G方案中为中国政府构建了监控之眼，允许中国在华为平台上监听或者停止网络。
下一次你读到关于中国在5G领域领先的标题时，我们会沾沾自喜，并质疑为什么中国政府这么努力确保地球上的每个网络都有由他们资助的供应商建造的设备。如果你去年购买了华为200亿部手机之一，你应该考虑改用苹果或者三星。（ps:安全是一个产品除了通信之外的最本质诉求，如果对这个有怀疑，用户在用脚投票）

### Tips
---
本周在go夜读群里看到一位牛人博客，执行力超强，每年都会列出读书计划和博客计划，关键是做什么都有输出，反思自己，基本上做完就算了，不去深入浅出。因此，从现在开始行动，列出计划，2019年写10篇博客，5本书。
书单如下：
- 原则
- 能力陷阱
- 数学之美
- 人类简史
- 时间简史
博客：
10篇技术博客，区块链技术、组件、golang核心知识点，不堆砌知识，重在实践出真知。

### Share
---
上周末参加了2天的产品经理培训，作为一个跨界程序员，工作中主导了多个项目的设计开发工作，有纯后端的，也有Web或者H5的，私以为有交互的才是产品经理的活儿。两天下来，理论加上实践，对产品经理充满了理解和同情，同时暗暗发冷，因为自己的产品中处处有产品经理的影儿，我们硬着头皮没有章法做的东西，契合了用户体验，但是在用户需求、产品定位和推广上，与产品经理相去甚远，哪些是核心需求，哪些是微创新，我们没有严格的区分。
今天特意整理了下课堂笔记，用[思维导图](https://github.com/PorcoRoso/ARTS/blob/master/share/pmInAction.svg)的方式呈现出来，结合课上的体验，还是有深刻印象的。










