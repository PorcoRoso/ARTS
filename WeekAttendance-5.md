## Week Attendance 5.

### Algorithm
---
leetcode 874. Walking Robot Simulation
#### Problem：
1、给定一个命令数组commands，里面有3种指令，-2是左转90度，-1是右转90度，[1,9]的数字是往前走的步数；
2、另外给定一个障碍数组obstacles，里面是点的位置；
要求，机器人按照commands的数组走，遇到obstacles数组的障碍，则在它前一个点，按照指令继续走，求此间的机器人走过的最大面积。
#### Example：
##### Example 1:

Input: commands = [4,-1,3], obstacles = []
Output: 25
Explanation: robot will go to (3, 4)
##### Example 2:

Input: commands = [4,-1,4,-2,4], obstacles = [[2,4]]
Output: 65
Explanation: robot will be stuck at (1, 4) before turning left and going to (1, 8)
#### Solution
```go
func max(x, y int) int {
	if x > y {
		return x
	} else {
		return y
	}
}

func robotSim(commands []int, obstacles [][]int) int {
	dirs := [][2]int{{0,1},{1,0},{0,-1},{-1,0}} // North, East, South, West

	obstacle := map[int]int{}
	for i, v := range obstacles {
		if len(v) != 0 {
			obstacle[obstacles[i][0]*100000 + obstacles[i][1]] = 1
		}
	}

	x, y, dir := 0, 0, 0
	square := 0
	for _, v := range commands {
		if v == -1 {
			dir++
		} else if v == -2 {
			dir--
		} else {
			for ; v > 0; v-- {
				d := dir % 4
				if d < 0 {
					d += 4
				}
				if _, ok := obstacle[(x+dirs[d][0])*100000 + y+dirs[d][1]]; ok {
					break
				}
				x += dirs[d][0]
				y += dirs[d][1]

				square = max(square, x*x + y*y)
			}
		}

	}

	return int(square)
}

```
leetcode 45.Jump Gamp II
#### Problem
给定数组，从第一个元素开始跳跃，最少多少步到达数组末尾。跳跃是按照元素值跳。
#### Example
Input: [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2.
    Jump 1 step from index 0 to 1, then 3 steps to the last index.
#### Solution
```go
// 当前能到达的最远距离为cur，上一次能到达的最远距离为last
func jump(nums []int) int {
	cnt := 0
	cur, last := 0, 0 // 当前能达到的最远位置，上一次能达到的最远位置
	for i := 0; i < len(nums); i++ {
		if i > last { // 当前位置大于last时，计数一次，更新last
			cnt++
			last = cur
		}

		if cur < i + nums[i] { // 一直更新为最大
			cur = i + nums[i]
		}
	}
	return cnt
}
```
## Review

---
Five big question about microservice to answer this year 今年有关微服务需要回答的五大问题
对微服务的fuss(小题大做)，导致了系列问题，这也是作者在帮别人优化项目和开发微服务中经常被问的5个问题。
##### 是什么导致微服务这么流行？
这要归功于Netflix在2009年开始从单数据中心往基于云的微服务迁移。彼时，微服务这个单词都不存在，2014年Adrian Cockcroft公开分享了在Netflix的云架构经历后，这个词才开始流行。微服务是面向服务架构的，松散耦合元素组成的，有限上下文的新模型。
Netflix承载1.37亿的订阅用户，如果不是微服务将数字内容全天候传输给用户，这么大的用户扩张几乎无法完成。后续的科技巨头，像Amazon、eBay等都走了微服务这条路。
##### 微服务怎么帮助生意成功的？Loosely coupled elements
松耦合，是机构普遍性成长起来的强基石。松耦合允许你开发、测试、发布、部署、集成、维护、更新都各自独立，不需要改动其余的代码。
实际好处如下：
1）开发更有效率，各产品小组开发自己的，互不依赖和干扰；
2）快速扩展、快速上市；
3）各部分质量更好，能快速查找各部分bug；
4）对于技术选择非常灵活，可以用Java，JavaScript或Python，对技术栈的选择自由；
5）可重用，登录或文件上传等微服务可在公司内共享。
##### 微服务真的小吗？
实际上，大小不一。
一般，较小的微服务设计部署支持都是一个人实施，但是，大量的微小组件可能导致未知的项目复杂性。
##### 为什么微服务会变成噩梦？
微服务能帮助公司变大变强，但是能力越强责任越大。微服务赋予（imposed by）的责任有：
1）公司重组，迁移（migrating to）到微服务对整个业务文化和架构有全局影响。需要创建自由的自驱动团队，他们对微服务的开发到部署生命周期有全部的所有权。
2）管理多个独立单元，多个独立单元如果没有合适的组织、部署、管理起来，可能会是一个巨大的噩梦。你需要有高水平的专门知识，从构建微服务开始，就去思考架构并在松耦合元素上构建鲁棒的系统。有一些解决方案如`Kubernetes`，能够治理微服务。但是企业使用kubernetes也会有相当大的挑战：安全，网络，存储，模拟器，复杂性。
3）微服务间通信处理，微服务需要通过http rest api（同步方式）或消息队列（异步方式）进行服务间交互。频繁的通信会导致性能较差。需要小心选择流的方式，以减轻劣势的影响。
4）失败的高风险，较大增量的组件网络交互会增加出错概率。
5）系统支持的问题，成百的组件，以不同语言、api、数据库、配置文件交织在一起，在项目支持的时候，是一个棘手的问题。虽说一个微服务的改动不会影响其他独立组件，但是实际上，一处更新可能会要求其他几个微服务的重编译，以立马保证兼容性。
##### 你现在真的需要微服务吗？
答案与你业务的雄心壮志及公司的实际大小有关。尽管存在各种挑战，但微服务架构被证明是提供大量服务的企业，大型部门或业务部门的理想解决方案。快速扩展并快速实现客户驱动变革的能力带来了显着的竞争优势。
但是，如果你经营一家小公司并且要构建一个简单的业务方案或快速启动以验证你的idea，经典的简单方案会更适合你。

原地址：https://medium.com/@rsachenko/five-big-questions-about-microservices-to-answer-this-year-e638dc1a1f9e

### Tips
---
本周刷题时，对 Walking Robot Simulation进行解题时，对其中障碍点的判断采取了简化方案，及如何快速判断一个点对在不在点对集合里，这里我采用了将点对的（x,y）做x*10000+y作为map的k,v随意，因此对点集的判断就变为了对map里k是否存在的判断。

### Share
---
上述tip能够快速查找一个点对是否在点集里。
但是，在提交代码时，有一个测试案例始终无法通过，得到的值币预期的小一点，查看案例，发现障碍点对特别的多，于是猜测是因为x*10000+y导致测试案例的值重复，有些障碍点没有绕过，将10000改为100000，即差距拉大，即解决问题。

本周尝试刷了greedy的hard题，感觉不到解题套路，自己的方案貌似有效，但最终都是在实现中途发现只能解决部分问题，看别人的方案，都技巧性太强，还没找到一个解决方案，最近开始频繁刷hard题总结方案。









