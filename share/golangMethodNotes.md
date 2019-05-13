##golang struct的指针接收者和值接收者比较
在学习reflect过程中，看到demo如下：
```go
// 定义一个结构体用来进行测试
type Cat struct {
	name string
}

// 接收者声明，接收者为值类型
func (s Cat) Read() {
	fmt.Println(s.name)
}

func (s Cat) Write(name string) {
	s.name = name
}

// 接收者声明，接收者为指针类型
func (s *Cat) WriteValue(name string) {
	s.name = name
}

// 获取一个对象的字段和方法
func GetMembers(i interface{}) {
	// 获取 i 的类型信息
	t := reflect.TypeOf(i)

	for {
		// 进一步获取 i 的类别信息
		if t.Kind() == reflect.Struct {
			// 只有结构体可以获取其字段信息
			fmt.Printf("\n%-8v %v 个字段:\n", t, t.NumField())
			// 进一步获取 i 的字段信息
			for i := 0; i < t.NumField(); i++ {
				fmt.Println(t.Field(i).Name)
			}
		}
		// 任何类型都可以获取其方法信息
		fmt.Printf("\n%-8v %v 个方法:\n", t, t.NumMethod())
		// 进一步获取 i 的方法信息
		for i := 0; i < t.NumMethod(); i++ {
			fmt.Println(t.Method(i).Name)
		}
		if t.Kind() == reflect.Ptr {
			// 如果是指针，则获取其所指向的元素
			t = t.Elem()
		} else {
			// 否则上面已经处理过了，直接退出循环
			break
		}
	}
}
```
结果如下：
```go
*reflect.sr 3 个方法:
Read
Write
WriteValue

reflect.sr 1 个字段:
name

reflect.sr 2 个方法:
Read
Write
```
出现疑问，*Cat struct指针接收者的方法有`3`个，struct值接收者的方法只有`2`个，这与我之前的理解不一致。遂查找资料进行研究。
### 1 方法 VS 函数
golang中方法与函数是有较大差异的。
- `方法` 有名字，必须隶属于某一个类型，不能当做值看待；方法所属类型，是方法声明中的接收者。
-- （如上述代码中的Cat结构体，也叫值接收者（Receiver），也可以是*Cat）
-- 方法的接收者必须是自定义的数据类型，不能使接口类型或接口指针类型
- `函数` 是独立的程序实体，名字可有可无，可当做值传来传去。

### 2 差异
截取上述代码片段，用于分析：
```go
// 接收者声明，接收者为值类型
func (s Cat) Read() {
	fmt.Println(s.name)
}

func (s Cat) Write(name string) {
	s.name = name
}

// 接收者声明，接收者为指针类型
func (s *Cat) WriteValue(name string) {
	s.name = name
}
```
#### 2.1 值接收者 VS 指针接收者
``Cat即为值接收者``  
- 在方法内部对变量的改变不影响结构 1

``*Cat即为指针接收者`` 
- 会在方法内部改变结构内部变量的值 2

特殊的，
``Cat即为值接收者`` 调用指针方法，会改变结构内的值 11
``*Cat即为指针接收者`` 即使调用值方法，也不会改变结构内的值 21
总结来说，``指针方法``无论如何都``会改变``结构内的值，``值方法``无论如何也``无法改变``结构内的值。
```
func Test() {
	cat1 := Cat{"no one1"}          // 初始化为"no one1"
	cat1.Write("cat1")
	fmt.Println(cat1.name)          // 1 不改变name值，为“no one1”

	cat1.WriteValue("cat11")
	fmt.Println(cat1.name)          // 11 改变name值，为“cat11”

	cat2 := &Cat{"no one 2"}        // 初始化为"no one2"
	cat2.WriteValue("cat2")
	fmt.Println(cat2.name)          // 2 改变name值，为“cat2”

	cat2.Write("cat21")
	fmt.Println(cat2.name)          // 21 不改变name值，为“cat2”
}
```
#### 2.2 值方法 VS 指针方法 总结
- ``1`` 值方法的接收者是类型值的一个副本，因此在该方法上对副本修改，不会体现在原值上；除非这个类型本身是某个引用类型（如切片或字典）的别名类型；这也从理论上解释了上面1和21无法改变结构的值的现象。
- ``2`` 接收者方法集合有谁，也就是写作本文疑问的起点，为什么`*Cat`的有3个，`Cat`只有2个。
-- 值接收者的方法集合：所有值方法
-- 指针接收者的方法集合：所有值方法 + 所有指针方法
- ``3`` 值接收者`cat1`可以调用`*Cat`指针接收者的指针方法`WriteValue`，是golang自动转译导致可以这么调用，`cat1.WriteValue("cat11")`会被自动转译为`(&cat1).WriteValue("cat11")`，即：先取`cat1`的指针值，然后在该指针值上调用`WriteValue`

### 3 指针接收者实现interface方法
#### 3.1 问题
指针接收者实现interface方法，使用值接收者去调用，满足上述2.2的第3条，但使用`接口对象`调用（案例中给的是Animal），则会有编译错误，且看测试案例。
```go
type Animal interface {
	print()
    printPtr()
}

// 定义一个结构体用来进行测试
type Cat struct {
	name string
}

// 实现Animal接口的print函数，这里是值接收者实现的
func (s Cat) print() {
	fmt.Println(s.name)
}
// 实现Animal接口的printPtr函数，这里是指针接收者实现的
func (s *Cat) printPtr() {
	fmt.Println(s.name)
}

func Test() {

	cat3 := Cat{"no one3"}
	cat3.print()                   // 3 打印没有问题

	var cat31 Animal
	cat31 = Cat{"no one31"}
	cat31.print()                   // 31 编译不过

	var cat32 Animal
	cat32 = Cat{"no one32"}
	cat32.printPtr()                  // 32 编译不过
}
```
31和32都编译不过，编译错误如下：
```shell
../goSrcRead/reflect/test.go:51:7: cannot use Cat literal (type Cat) as type Animal in assignment:
	Cat does not implement Animal (printf method has pointer receiver)
```
#### 3.2 问题分析
看提示Cat未实现Animal接口，Cat和Animal不是同一个类型，而是*Cat和Animal是一个类型。
#### 3.3 解决方案
```
func Test() {

	cat3 := Cat{"no one3"}
	cat3.print()

	var cat31 Animal
	cat31 = &Cat{"no one31"}
	cat31.print()                     // 31 修改为指针后，通过，打印“no one31”

	var cat32 Animal
	cat32 = &Cat{"no one32"}
	cat32.printPtr()                  // 32 修改为指针后，通过，打印“no one32”
}
```

### 参考文献
1. [极客时间Go语言核心36讲](https://time.geekbang.org/column/article/18035)
2. [Golang 方法的结构指针接收者和结构值接收者](https://blog.csdn.net/suiban7403/article/details/78899671)

