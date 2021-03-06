## Week Attendance 4.

### Algorithm
---
LeetCode 860 Lemonade Change

#### 题目

在柠檬摊，每个柠檬卖5块。客户依次购买一个，每位客户支付5/10/20块的一种，如果无法找零合适的前给用户，则找零失败。（注：刚开始你是没有钱的）

##### Example 1

输入：[5,5,5,10,20]

输出：true

##### Example 2

输入：[5,5,10,10,20]

输出：false

#### 思路

1、每次只可能给用户找5或10块，优先找10块，然后才是找5块。是一个贪心算法问题，问题可转化为，如何找零以便能够服务更多的客户。

2、实现上，用了fiveCnt和tenCnt标记找零的剩余。

```go
func lemonadeChange(bills []int) bool {
    if len(bills) <= 1 {
		return true
	}

	fiveCnt := 0
	tenCnt := 0
	for _,v := range bills {
		if v == 5 {
			fiveCnt++
		} else if v == 10 {
			if fiveCnt <= 0 {
				return false
			}
			fiveCnt--
			tenCnt++
		} else if v == 20 {
			if tenCnt > 0 && fiveCnt > 0 {
				tenCnt--
				fiveCnt--
			} else if fiveCnt >= 3 {
				fiveCnt--
				fiveCnt--
				fiveCnt--
			} else {
				return false
			}
		}
	}
	return true
}
```

Review

---
The Consumerization of Enterprise Tech 企业技术的消费化

作者对企业怎样采用新的技术或者工具做了分析，先员工习惯，再打入公司内部成为协作工具。

什么是企业技术的消费化：企业采用类用户的解决方案解决商业问题，而且这些技术通常是通过用户通道购买的。这个趋势在增长。其实企业使用新技术的动力不强且慢，但是分布在不同地域工作的员工有选择技术的自由。自己能通过信用卡购买解决的技术那就买买买了，这在自下而上驱动的公司很常见。

``Dropbox``是一个典型的企业技术消费化的例子，体现在两方面：一，市场技巧独一无二（用免费的云存储替代市场消费来获取用户）；二，特洛伊木马式的商业模式。Dropbox是一个用户产品，但是有清晰的企业用途，用户使用广泛，最后公司的IT对此无能为力智能购买它。

自此，``Zoom``、``Slack``、``Notion``有样学样，``Accompany``也是一个例子。其app连接了用户日历和地址簿，为用户提供待访人员服务。从公司日历和地址簿读取数据会引起IT注意，但是如果来自于用户压力够大，这个服务也就可能被官方批准了。``LinkedIn``、``Uber``、和``Lyft``也是这一套路，把个人用户转化为企业用户。

硬件行业如是。

随着消费者和企业技术不断融合，我们的工作生活会更自然和更少限制，生产力融入生活中，每个工具将必须有世界一流的用户体验才能参与竞争。但是基于此的数据隐私和安全，是重要的考虑点。IT经理需要快速评估和集成新解决方案，以为员工提供数据隐私和安全保障。

原地址：<https://medium.com/@tiffinewang/the-consumerization-of-enterprise-tech-344ba7fbfc58>

### Tips
---
最近学习数据结构与算法，看到一个思考题：在高并发分布式系统中如何生成全局唯一的ID？

#### 场景

数据库分库分表时，假设一张用户表变8张，每张用户表的ID就不能通过自增方式产生了，不然肯定会产生重复。为此，需要实现一个ID生成器，高性能、支持并发、全局唯一。

#### 几种方案

| 方案       | 介绍                                                         | 优势                                     | 劣势                                                         |
| ---------- | ------------------------------------------------------------ | ---------------------------------------- | ------------------------------------------------------------ |
| 数据库自增 | 数据库单库单表时，利用数据库auto_increment来生成唯一递增ID   | 简单，保持定长增量                       | 高并发下性能不佳，主键产生的性能上限是数据库服务器单机的上限；水平扩展难，分布式数据库环境下无法保证唯一性 |
| UUID       | 一般语言自带UUID实现，如Java的UUID.randomUUID().toString()   | 本地生成不需远程调用，不重复，水平扩展好 | ID有128bit，占用空间大，需转存成字符串类型，索引效率低；生成的ID没带Timestamp，无法保证趋势递增 |
| Snowflake  | twitter开源，核心是产生一个long型ID，使用其中41位作为毫秒数，10位为机器编号，12位为计数顺序；单机每秒理论上1000*2^12个即400W个ID | 高性能低延迟；应用独立，按时间有序       | 需要独立开发和部署                                           |
| redis      | redis的EVAL,EVALSHA命令，redis是单进程的；在每个节点上通过lua脚本生成唯一ID | 高性能                                   | 需要独立机器                                                 |


### Share
---
不会讲故事的程序员不会是一个合格的产品经理。最近几个业务项目立项和报价，发现给领导汇报，语言生硬，一个好好地业务被讲成了技术汇报，导致领导无法理解而问题一堆，直接被吐槽说不会讲故事。

程序员的语言是正向简单化的，而演讲者的语言式反向丰富化的，就像"我爱你"，程序员会简化为一个词：“爱”，而演讲者会说：“我就像个神经病一样爱着你”，差距。

从每次讲PPT开始，学者如何将故事！

