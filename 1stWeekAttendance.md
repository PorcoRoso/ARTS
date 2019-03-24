## 1st Week Attendance.

### Algorithm

---

#### 347. Top K Frequent Elements

Given a non-empty array of integers, return the **k** most frequent elements.

**Example 1:**

```
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```

**Example 2:**

```
Input: nums = [1], k = 1
Output: [1]
```

**Note:**

- You may assume *k* is always valid, 1 ≤ *k* ≤ number of unique elements.
- Your algorithm's time complexity **must be** better than O(*n* log *n*), where *n* is the array's size.

#### 解题思路

Step 1. 哈希表；遍历数组元素到哈希表，key为数组元素，value为数组元素个数，按示例结果得到<1,3>,<2,2>,<3,1>，至此问题解决一半；

Step 2. 按value值排序返回最大的k个，存入数组返回即可；思路是：获取哈希表里最大的value，将key存入返回数组，并将最大的<key,value>从map里移除；k--遍历直至为0，解决问题。

#### 代码 golang

```go
func topKFrequent(nums []int, k int) []int {
	var ret []int
	maps := make(map[int]int, 0)
	for _, v := range nums {
		maps[v]++
	}

	for i:= k-1; i >= 0; i-- {
		key := topFromMap(maps)
		ret = append(ret, key)
		delete(maps, key)
	}
	return ret
}

func topFromMap(maps map[int]int) int {
	max := -1
	key := 0
	for k, v := range maps {
		if v > max {
			max = v
			key = k
		}
	}
	return key
}
```

### Review

---

一直在学习hyperledger fabric，很多英文文档都是读过就过去了，并没有留下痕迹，正好趁着这次机会，maybe能为社区也能做点贡献。

fabric 1.4发布有一段时间了，这是官方对外宣布的第一个LTS版本，从0.6到1.0，fabric经历了较大的架构调整，随后基本上以每季度一次的速度迭代到了1.4，对于目前大多数的公司来说，在短期或长期内，都会选择1.4版本作为POC或者项目实施的基础版本。

原地址：https://hyperledger-fabric.readthedocs.io/en/release-1.4/whatsnew.html

翻译地址：https://github.com/PorcoRoso/ARTS/blob/master/review/fabric.md

### Tips

---

最近参与go学习群组，每周跟随乐于分享的人学习go语言相关的奇技淫巧，觉得乐趣甚多，其中有一位滴滴的朋友在进行defer机制及逃逸分析分享的时候令人耳目一新，因为在分享的第一页，他加了一篇自我介绍。介绍包括四块：个人，因缘，能提供的，想获得的。

简简单单四点，串起来就是一个简短的故事，瞬间别人就能记住你。

### Share

---

最近go夜读学到的一个源码阅读技巧，在学习go Context包，对于一个资源的解锁，有很多地方都是显式的unlock，如下所示：

```go
func (c *cancelCtx) Done() <-chan struct{} {
	c.mu.Lock()
	if c.done == nil {
		c.done = make(chan struct{})
	}
	d := c.done
    c.mu.Unlock() //问题：为什么没有用defer，这样就不需要重新有一个变量d，写成defer c.mu.Unlock()即可 
	return d
}
```

有很多人的答案是：1、效率的考虑，2、防死锁

但是紧接着又在下面的代码里出现：

```go
func WithDeadline(parent Context, d time.Time) (Context, CancelFunc) {
	if cur, ok := parent.Deadline(); ok && cur.Before(d) {
		// The current deadline is already sooner than the new one.
		return WithCancel(parent)
	}
	c := &timerCtx{
		cancelCtx: newCancelCtx(parent),
		deadline:  d,
	}
	propagateCancel(parent, c)
	dur := time.Until(d)
	if dur <= 0 {
		c.cancel(true, DeadlineExceeded) // deadline has already passed
		return c, func() { c.cancel(true, Canceled) }
	}
	c.mu.Lock()
	defer c.mu.Unlock() // 此处用了defer
	if c.err == nil {
		c.timer = time.AfterFunc(dur, func() {
			c.cancel(true, DeadlineExceeded)
		})
	}
	return c, func() { c.cancel(true, Canceled) }
}
```

第二个学习到的点：重复使用的通道

```go
// closedchan is a reusable closed channel.
var closedchan = make(chan struct{})

func init() {
	close(closedchan)
}
...

func (c *cancelCtx) cancel(removeFromParent bool, err error) {
	if err == nil {
		panic("context: internal error: missing cancel error")
	}
	c.mu.Lock()
	if c.err != nil {
		c.mu.Unlock()
		return // already canceled
	}
	c.err = err
	if c.done == nil {
		c.done = closedchan // 可重用的channel!
	} else {
		close(c.done)
	}
	for child := range c.children {
		// NOTE: acquiring the child's lock while holding parent's lock.
		child.cancel(false, err)
	}
	c.children = nil
	c.mu.Unlock()

	if removeFromParent {
		removeChild(c.Context, c)
	}
}
```

