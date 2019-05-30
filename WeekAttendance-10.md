## Week Attendance 9.

### Algorithm
---
leetcode 153. Find Minimum in Rotated Sorted Array
#### Problem：
用stack实现queue,leetcode列出了必须实现的几个函数，包括构造函数，push/pop/peek/empty
#### Example：
##### Example 1:
MyQueue queue = new MyQueue();

queue.push(1);
queue.push(2);  
queue.peek();  // returns 1
queue.pop();   // returns 1
queue.empty(); // returns false

#### Solution
解法：两个stack即可，因为golang没有内置stack，这里用两个slice实现更简单，因为可以方便寻址[0:len]空间
- 解法一

```go
type MyQueue struct {
	inStack []int
	outStack []int
}


/** Initialize your data structure here. */
func Constructor() MyQueue {
	inStack := make([]int, 0)
	outStack := make([]int, 0)

	return MyQueue{
		inStack: inStack,
		outStack: outStack,
	}
}


/** Push element x to the back of queue. */
func (this *MyQueue) Push(x int)  {
	this.inStack = append(this.inStack, x)
}


/** Removes the element from in front of queue and returns that element. */
func (this *MyQueue) Pop() int {
	if len(this.outStack) == 0 {
		for i := len(this.inStack); i > 0; i-- {
			this.outStack = append(this.outStack, this.inStack[len(this.inStack)-1])
			this.inStack = this.inStack[:len(this.inStack)-1]
		}
	}
	res := this.outStack[len(this.outStack)-1]
	this.outStack = this.outStack[:len(this.outStack)-1]
	return res
}


/** Get the front element. */
func (this *MyQueue) Peek() int {
	if len(this.outStack) != 0 {
		return this.outStack[len(this.outStack)-1]
	} else {
		return this.inStack[0]
	}
}


/** Returns whether the queue is empty. */
func (this *MyQueue) Empty() bool {
	if len(this.inStack) > 0 || len(this.outStack) > 0 {
		return false
	} else {
		return true
	}
}


/**
 * Your MyQueue object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * param_2 := obj.Pop();
 * param_3 := obj.Peek();
 * param_4 := obj.Empty();
 */

```
## Review
---
#### Will Edge Computing Replace the Cloud?
    作者从几个方面分析了边缘计算是否会替代云计算，似乎边缘计算越来越成为一种趋势，但是最终还是会两者共存，离不开彼此。下面从几个大标题列明中心思想。
工业4.0导致越来越多的智能设备，边缘计算是一种分布式网络架构，可以使数据在产生源头进行处理。

- 时代在改变

   2016年科技投资人Peter Levine就预测边缘计算会替代云计算。会不会呢？
- 云计算的时代

    过去十二年，决策者才刚决定将基于硬件和软件的模式转移到基于服务的云计算模式，通过云计算保存数据、开发App；云计算意味着对于用户来说系统资源（计算、存储）很方便就能获取，通过网络访问数据中心即可；云计算以为高扩展性，资源便宜且灵活获取。
- 生活在边缘时代
    IoT设备有了相当的发展。
    有史以来增长的智能设备数量和连接的服务意味着传输至数据中心的数据太大，无法实现用户期望的无缝、实时体验。
    数据不再需要传输至数据中心进行处理，取而代之的是在边缘数据中心或者就在设备上。这在需要短时响应的用户案例中非常管用。
- 了解边缘计算
    从几个范例paradigms看看边缘计算。
    -- 智能工厂

    -- 智能汽车

    -- 智能城市

- 边缘计算与云计算只能有一个吗？
    答案是不，边缘计算不是独立的解决方案，边缘计算会成为日益复杂和多样化的网络，这个网络会围绕大型云计算中心而建立。

### Tips
---


### Share
---












