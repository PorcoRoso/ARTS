## Context包脉络及源码分析

### 1 脉络

依循脉络，可以快速使用和掌握Context。

#### 1.1 Context接口

```go
type Context interface {
	Deadline() (deadline time.Time, ok bool) // 返回该context初始化时设置的截止时间，ok表示是否设置时间
	Done() <-chan struct{} // 返回只读的chan，用于接收io输入；当有输入，意味着parent context发起取消请求，该对当前context做清理操作释放资源
	Err() error // 返回取消的错误原因，因为什么原因导致Context被取消
	Value(key interface{}) interface{} // 读取绑定在context的k-v值，线程安全
}
```

上述方法使用频率最高的是`Done`，以下为一个示例：

```go
// 上一层goroutine中调用cancelFunc，触发ctx.Done()返回一个chan，导致select监听到io输入，函数退出
// 即通过Done得到一个可读取的chan时，意味着收到context取消信号了
func Stream(ctx context.Context, out chan<- Value) error {
  	for {
  		v, err := DoSomething(ctx)
  		if err != nil {
  			return err
  		}
  		select {
  		case <-ctx.Done():
  			return ctx.Err()
  		case out <- v:
  		}
  	}
}
```

``Context有两个实现，即用户可以用这两个实现，而不需要去实现该接口。``

```go
var (
	background = new(emptyCtx)
	todo       = new(emptyCtx)
)

func Background() Context { // 返回非nil值的空Context，不能被取消，没有值且没有截止日期；根Context
	return background // 用于main函数、初始化、测试等场景
}

func TODO() Context { // 返回非nil值的空Context，不能被取消，没有值且没有截止日期
	return todo // 当不知道使用哪个context，用TODO
}
```

上述两个实现是通过`emptyCtx`实现了`Context`接口。需要注意的是，`emptyCtx`的实现方法均返回空值。

```go
type emptyCtx int

func (*emptyCtx) Deadline() (deadline time.Time, ok bool) {
	return
}

func (*emptyCtx) Done() <-chan struct{} {
	return nil
}

func (*emptyCtx) Err() error {
	return nil
}

func (*emptyCtx) Value(key interface{}) interface{} {
	return nil
}

func (e *emptyCtx) String() string {
	switch e {
	case background:
		return "context.Background"
	case todo:
		return "context.TODO"
	}
	return "unknown empty Context"
}
```

#### 1.2 Context继承

上述两个parent context，通过以下四个With函数衍生`子context`，函数都以`父context`作为参数，输出`子context`和cancelFunc（取消函数）：

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
func WithValue(parent Context, key, val interface{}) Context
```

函数是基于`父context`，创建`子context`，并为`父context`提供cancelFunc方法，取消`子context`。

- ``WithCancel`` 传递一个父context作为参数，返回子context及取消函数用来取消context；

- ``WithDeadline`` 增加了一个deadline参数，表示到了这个截止时间，会自动取消context；

- ``WithTimeout`` 增加了一个Timeout参数，表示多少时间后自动取消；

- ``WithValue`` 与取消context无关，是为了生成一个绑定了k-v的context，可以通过context.Value(k)访问到。

###1.3 Context WithValue传递元数据

如上分析，这里给出一个例子：

```go
var key string="name"

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	// 为子context附加值
	valueCtx := context.WithValue(ctx, key, "【监控1】")
	go watch(valueCtx)
	time.Sleep(10 * time.Second)
	fmt.Println("可以了，通知监控停止")
	cancel()
	//为了检测监控过是否停止，如果没有监控输出，就表示停止了
	time.Sleep(5 * time.Second)
}

func watch(ctx context.Context) {
	for {
		select {
		case <-ctx.Done():
			//取出值
			fmt.Println(ctx.Value(key), "监控退出，停止了...")
			return
		default:
			//取出值
			fmt.Println(ctx.Value(key), "goroutine监控中...")
			time.Sleep(2 * time.Second)
		}
	}
}
```

例子中，通过context绑定kv的方式作为参数传递给监控函数，与直接传递参数类似，但是这个参数所起到的作用完全不一样了。

对k-v的要求，k是有可比性的，v是线程安全的，通过context.Value(k)访问v；

使用WithValue传值，一般是传必须的值。

###2 源码分析

```go
type CancelFunc func() // 取消函数
```

####2.1 WithCancel模块

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc) {
   c := newCancelCtx(parent) // 初始化的cancelCtx
   propagateCancel(parent, &c) // 当父ctx取消时，负责向子ctx传递取消
   return &c, func() { c.cancel(true, Canceled) }
}

func newCancelCtx(parent Context) cancelCtx {
   return cancelCtx{Context: parent} // 见2.1.1
}

func propagateCancel(parent Context, child canceler) {
    // 见2.1.2 若子ctx为valueCtx，则不断迭代获取context，直至找到可撤销类型Ctx，比如cancelCtx或
    // 者timerCtx.
}
```

##### 2.1.1 cancelCtx

`cancelCtx`继承自`Context`接口，是可取消的context，当被取消时，会取消所有的实现了`canceler`接口的children，也通过实现cancel方式继承了`canceler`接口:

```go
type cancelCtx struct {
	Context

	mu       sync.Mutex            // protects following fields
	done     chan struct{}         // created lazily, closed by first cancel call
	children map[canceler]struct{} // set to nil by the first cancel call
	err      error                 // set to non-nil by the first cancel call
}

// canceler是可直接被取消的ctx类型，其实现有*cancelCtx,*timeCtx
type canceler interface {
	cancel(removeFromParent bool, err error)
	Done() <-chan struct{}
}
```



```go
func (c *cancelCtx) Done() <-chan struct{} {
	c.mu.Lock()
	if c.done == nil {
		c.done = make(chan struct{})
	}
    d := c.done // 没有直接用return c.done;defer c.mu.Unlock();再return d 
	c.mu.Unlock() // defer的机制，锁的时间不会太长！
	return d
}

func (c *cancelCtx) Err() error {
	c.mu.Lock()
	err := c.err // 与上述一致！
	c.mu.Unlock()
	return err
}

// 关闭c.done通道，取消c的所有children，从c的parent移除c
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
		c.done = closedchan // 这里，使用了
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

```go
// tips: 可重用的关闭通道，但有什么好处呢？
var closedchan = make(chan struct{})

func init() {
	close(closedchan)
}
```



##### 2.1.2 propagateCancel取消传递

```go
func propagateCancel(parent Context, child canceler) {
   if parent.Done() == nil {
      return // parent is never canceled
   }
  
   if p, ok := parentCancelCtx(parent); ok { 
      p.mu.Lock()
      if p.err != nil {
         // parent has already been canceled
         child.cancel(false, p.err)
      } else {
         if p.children == nil {
            p.children = make(map[canceler]struct{})
         }
         p.children[child] = struct{}{}
      }
      p.mu.Unlock()
   } else {
      go func() {
         select {
         case <-parent.Done():
            child.cancel(false, parent.Err())
         case <-child.Done():
         }
      }()
   }
}
// 判断输入context类型，cancelCtex,timerCtx,valueCtx，如果是valueCtx，则一直循环找到cancelCtx
// 因为只有cancelCtex,timerCtx为可取消context.
func parentCancelCtx(parent Context) (*cancelCtx, bool) {
	for {
		switch c := parent.(type) {
		case *cancelCtx:
			return c, true
		case *timerCtx:
			return &c.cancelCtx, true
		case *valueCtx:
			parent = c.Context
		default:
			return nil, false
		}
	}
}
```



###3 结语

context使用有一些原则与技巧：

- 不要把Context放在结构体中，要以参数的方式传递，parent Context一般为Background

- 应该要把Context作为第一个参数传递给入口请求和出口请求链路上的每一个函数，放在第一位，变量名建议都统一，如ctx。

- 给一个函数方法传递Context的时候，不要传递nil，否则在tarce追踪的时候，就会断了连接

- Context的Value相关方法应该传递必须的数据，不要什么数据都使用这个传递

- Context是线程安全的，可以放心的在多个goroutine中传递

- 可以把一个 Context 对象传递给任意个数的 gorotuine，对它执行 取消 操作时，所有 goroutine 都会接收到取消信号。

参考

[Golang Context深入理解](https://juejin.im/post/5a6873fef265da3e317e55b6)

[Go语言实战笔记（二十）| Go Context](https://www.flysnow.org/2017/05/12/go-in-action-go-context.html)