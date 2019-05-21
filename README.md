# Go note

- [fmt](#fmt)
- [String](#string)
- [Slice](#slice)
- [Map](#map)
- [Interface](#interface)
- [For](#for)
- [Function](#function)
- [JSON](#json)
- [Sort](#sort)
- [Crypto](#crypto)
- [Runtime](#runtime)
- [Sync](#sync)
- [Channel](#channel)
	- [Pipeline](#pipeline)

---

## Resources

- [GoDoc](https://godoc.org/)

- [Go by Example](https://gobyexample.com/)

- [JSON-to-Go](https://mholt.github.io/json-to-go/)

## Other Stuff

- [Golang Proverbs](https://go-proverbs.github.io/)

- The `x` package is like the experimental standard library which hasn't migrated to the standard library.

## Command 

Check if there's a race condition in our code.
> go run -race main.go 

## Types

byte = uint8

## Method Set

[Language spec](https://golang.org/ref/spec#Method_sets)
> The method set of a type determines the interfaces that the type implements and the methods that can be called using a 
> receiver of that type.

```go
type Greetable interface{ greeting() }

type Person struct {
	firstName string
	lastName  string
}

func (p Person) fullName() string { return p.firstName + " " + p.lastName }
func (p *Person) greeting()       { fmt.Printf("%s says hi!\n", p.fullName()) }

func main() {
	person := Person{"英九", "辣寶貝"}
	person.greeting()
	(&person).greeting()
	greet(&person)
	// greet(person) // This will cause compiler error.
}

func greet(g Greetable) {
	g.greeting()
}
```

|              |Type  |Interface            |
|--------------|------|---------------------|
|**Method Set**|T, \*T|Same type as receiver|


## fmt

```go
// Print
fmt.Printf()
fmt.Println()
// Format string
fmt.Sprintf()
```

## String

```go
func main() {
	fmt.Println(`This is a "raw string"`)
}
```

|Result                |
|----------------------|
|This is a "raw string"|

## Slice

```go
func main() {
	s := []int{4, 5, 6}
	printSum(1, 2, 3)
	printSum(s...)
	printSum()
}

func printSum(nums ...int) int {
	s := 0
	for _, n := range nums {
		s += n
	}
	fmt.Printf("Is nil : %-5v, sum = %2d\n", nums == nil, s)
	return s
}
```

|Result|
|------|
|Is nil : false, sum =  6|
|Is nil : false, sum = 15|
|Is nil : true , sum =  0|

> printSum()

The underlying array will be nil if the variadic argument is empty.

## Map

```go
func main() {
	m := make(map[string]int)
	m["ID_01"] = 20
	fmt.Println(m)

	m = map[string]int{"ID_01": 20, "ID_02": 30}
	delete(m, "ID_01")
	fmt.Println(m)
}
```

|Result       |
|-------------|
|map[ID_01:20]|
|map[ID_02:30]|

## Interface 

Go interface is like `protocol` in Swift.
Every type in Go is of interface `interface{}`.

```golang
func main() { 
	myFunc(10) 
}

func myFunc(i interface{}) {
	switch i.(type) {
	case int:
		fmt.Printf("Type: %T, Value: %v", i.(int), i)
	default:
		fmt.Println("Unknown Type.")
	}
}
```

|Result              |
|--------------------|
|Type: int, Value: 10|

---

```go
type circuclar interface {
	area() float64
	circumference() float64
}

type circle struct {
	radius float64
}

func (c circle) area() float64 { return math.Pi * c.radius * c.radius }

func (c *circle) circumference() float64 { return math.Pi * c.radius * 2 }

func printCircleInfo(c circuclar) {
	fmt.Printf("Area = %.3f, circumference = %.3f\n", c.area(), c.circumference())
}

func main() {
	vCircle := circle{10}
	rCircle := &circle{10}

	printCircleInfo(&vCircle)
	printCircleInfo(rCircle)
}
```

|Result           |
|-----------------|
|Area = 314.159, circumference = 62.832|
|Area = 314.159, circumference = 62.832|

The `circumference()` in struct `circle` uses pointer receiver,
so we must pass a pointer of `circle` as the argument of `printCircleInfo()`.

## For

```go
func main() {
	var i = 0
	for i < 3 {
		i++
		fmt.Println(i)
	}
}
```

|Result              |
|--------------------|
|1|
|2|
|3|

```go
func main() {
	for i := 0; i < 3; i++ {
		fmt.Println(i)
	}
}
```

|Result              |
|--------------------|
|0|
|1|
|2|

```go
func main() {
	i := 0
	a := make([]int, 5)

	for range a {
		i++
		fmt.Printf("%d ", i)
	}
}
```

|Result              |
|--------------------|
|1 2 3 4 5	     |


```go
func main() {
	a := []int{1, 2, 3}

	for i, v := range a {
		fmt.Printf("%d.%d ", i, v)
	}
}
```

|Result              |
|--------------------|
|0.1 1.2 2.3	     |

## Function

```go
func main() {
	func() {
		fmt.Println("This is a anonymous function")
	}()
}
```

|Result|
|----------------------------|
|This is an anonymous function|

## JSON

```go
type person struct {
	Name string `json:"RealName"`
	Age  int    `json:"Age"`
}

func main() {
	p1 := person{"scchn", 26}
	p2 := person{"gbchn", 25}

	if data, err := json.Marshal([]person{p1, p2}); err == nil {
		var people []person

		fmt.Println(string(data))

		if err = json.Unmarshal(data, &people); err == nil {
			fmt.Printf("%+v\n", people)
		} else {
			fmt.Println(err)
		}
	} else {
		fmt.Println(err)
	}
}
```

|Result|
|------|
|[{"RealName":"scchn","Age":26},{"RealName":"gbchn","Age":25}]|
|[{Name:scchn Age:26} {Name:gbchn Age:25}]|

> Name string \`json:"RealName"\`

The JSON tag after var declaration is like `CodingKey` in Swift:

## Sort

```go
type Person struct {
	Name string
	Age  int
}

func (p *Person) info() string {
	return fmt.Sprintf("%s(%d)", p.Name, p.Age)
}

type ByAge []Person

func (s ByAge) Len() int           { return len(s) }
func (s ByAge) Swap(i, j int)      { s[i], s[j] = s[j], s[i] }
func (s ByAge) Less(i, j int) bool { return s[i].Age < s[j].Age }

func main() {
	people := []Person{
		Person{Name: "James", Age: 22},
		Person{Name: "Jordan", Age: 19},
		Person{Name: "Jack", Age: 39},
	}

	printInfo(people)
	sort.Sort(ByAge(people))
	printInfo(people)
}

func printInfo(ps []Person) {
	for _, p := range ps {
		fmt.Print(p.info(), " ")
	}
	fmt.Println()
}
````

|Result|
|-|
|James(22) Jordan(19) Jack(39)| 
|Jordan(19) James(22) Jack(39)|


## Crypto

```go
import "golang.org/x/crypto/bcrypt"

func main() {
	password := "password"

	if bs, err := bcrypt.GenerateFromPassword([]byte(password), bcrypt.MinCost); err == nil {
		fmt.Println(string(bs))

		err := bcrypt.CompareHashAndPassword(bs, []byte(password))

		if err == nil {
			fmt.Println("Password correct")
		} else {
			fmt.Println("Wrong password")
		}
	}
}
```

|Result|
|-|
|$2a$04$XTBZQ4r/XLhF0K/5kqwBF.SwbWIdmAw3cJEQf5aTa1CLBsD.hOuLG|
|Password correct|


## Runtime

```go
func main() {
	fmt.Println("ARCH      :", runtime.GOARCH)
	fmt.Println("OS        :", runtime.GOOS)
	fmt.Println("CPUs      :", runtime.NumCPU())
	fmt.Println("Goroutines:", runtime.NumGoroutine())
}
```

|Result|
|-|
|ARCH      : amd64 |
|OS        : darwin|
|CPUs      : 4	   |
|Goroutines: 1	   |


## Sync 

`WaitGroup` is like `DispatchSemaphore` in Swift.

```go
func main() {
	var wg sync.WaitGroup

	wg.Add(1)

	go from1To10(&wg)
	from11To20()

	wg.Wait()
}

func from1To10(wg *sync.WaitGroup) {
	for i := 1; i <= 10; i++ {
		fmt.Print(i, " ")
	}
	wg.Done()
}

func from11To20() {
	for i := 11; i <= 20; i++ {
		fmt.Print(i, " ")
	}
}
```

|Result|
|-|
|11 12 13 14 15 16 17 18 19 20 1 2 3 4 5 6 7 8 9 10|

```go
var (
	mux = sync.Mutex{}
)

func main() {
	f(1000, inc1)
	f(1000, inc2)
	f(1000, inc3)
}

func inc1(num *int64) { *num++ }

func inc2(num *int64) { atomic.AddInt64(num, 1) }

func inc3(num *int64) {
	mux.Lock()
	*num++
	mux.Unlock()
}

func f(times int, inc func(*int64)) {
	counter := int64(0)
	wg := sync.WaitGroup{}

	wg.Add(times)

	for i := 0; i < times; i++ {
		go func() {
			inc(&counter)
			wg.Done()
		}()
	}

	wg.Wait()

	fmt.Println("Counter =", counter)
}
```

|Result        |
|--------------|
|Counter = 985 |
|Counter = 1000|
|Counter = 1000|

## Channel

Channels are allocated with [make](https://golang.org/ref/spec#Making_slices_maps_and_channels) and may be closed with the built-in function [close](https://golang.org/ref/spec#Close).

- Resources
	- https://blog.golang.org/pipelines


Buffered & Unbuffered:

```go
func main() {
	// unbuffered channel
	c := make(chan string)
	go getData(c)
	fmt.Println(<-c)
	close(c)
	// buffered channel
	c = make(chan string, 1)
	getData(c)
	fmt.Println(<-c)
}

func getData(c chan string) {
	time.Sleep(time.Duration(500) * time.Millisecond)
	c <- "Content"
}
```

Using [select](https://golang.org/ref/spec#Select_statements):

You also can set channel timeout in `select` with `time.After`.

```go
func main() {
	c := make(chan string)

	go getData(c)

	select {
	case res := <-c:
		fmt.Println(res)
	case <-time.After(time.Second):
		fmt.Println("request timed-out")
	}

	go getData(c)

	select {
	case v, ok := <-c:
		if ok {
			fmt.Println(v)
		} else {
			fmt.Println("channel closed")
		}
	case <-time.After(time.Second):
		fmt.Println("request timed-out")
	}
}
```

Usin range:

```go
func main() {
	c := make(chan string)

	go func() {
		for i := 0; i < 10; i++ {
			getData(c)
		}
		close(c)
	}()

	for v := range c {
		fmt.Println("Received:", v)
	}

	fmt.Println("Finished")
}
```

Channel Directions:

```go
func main() {
	c := make(chan string)
	wg := sync.WaitGroup{}
	wg.Add(1)

	go recv(c, &wg)
	send(c)

	wg.Wait()
}

// send-only channel
func send(c chan<- string) {
	c <- "Content"
}

// receive-only channel
func recv(c <-chan string, wg *sync.WaitGroup) {
	fmt.Println(<-c)
	wg.Done()
}
```

### Pipeline

```go
type point struct {
	x float64
	y float64
}

type line struct {
	from point
	to   point
}

func (l line) dist() float64 {
	dx := l.to.x - l.from.x
	dy := l.to.y - l.from.y
	return math.Sqrt(dx*dx + dy*dy)
}

func main() {
	lines := []line{
		line{point{10, 10}, point{20, 20}},
		line{point{5, 10}, point{20.4, 582.1}},
	}

	for d := range cdist(gen(lines...)) {
		fmt.Println(d)
	}
}

func gen(lines ...line) <-chan line {
	out := make(chan line)
	go func() {
		for _, l := range lines {
			out <- l
		}
		close(out)
	}()
	return out
}

func cdist(in <-chan line) <-chan float64 {
	out := make(chan float64)
	go func() {
		for l := range in {
			out <- l.dist()
		}
		close(out)
	}()
	return out
}
```

|Result            |
|------------------|
|14.142135623730951|
|572.3072339224798 |

Fan-in, fan-out:

```go
func main() {
	lines := []line{
		line{point{10, 10}, point{20, 20}},
		line{point{534.16, 10.123}, point{20.4, 582.1}},
		line{point{5, 10}, point{20.4, 724.1}},
		line{point{54, 27}, point{75.4, 4.452}},
		line{point{55, 527}, point{275.2, 360.45}},
	}
	in := gen(lines...)
	out := merge(cdist(in), cdist(in))

	for dist := range out {
		fmt.Println("dist =", dist)
	}
}

func merge(ins ...<-chan float64) <-chan float64 {
	var wg sync.WaitGroup
	out := make(chan float64)

	wg.Add(len(ins))

	for _, in := range ins {
		go func(c <-chan float64) {
			for v := range c {
				out <- v
			}
			wg.Done()
		}(in)
	}

	go func() {
		wg.Wait()
		close(out)
	}()

	return out
}
```

|Result                   |
|-------------------------|
|dist = 14.142135623730951|
|dist = 714.2660358717892 |
|dist = 768.8348497102612 |
|dist = 276.0922717136429 |
|dist = 31.08652930129062 |

Another one:

```go
func main() {
	jobs := []<-chan int{calc(0, 10), calc(11, 20), calc(0, 10), calc(11, 20)}
	c := fanIn(jobs...)
	s := 0
	for range jobs {
		s += <-c
	}
	fmt.Println("s =", s)
}

func calc(from, to int) <-chan int {
	c := make(chan int)
	sum := 0
	go func() {
		for i := from; i <= to; i++ {
			sum += i
		}
		c <- sum
	}()
	return c
}

func fanIn(inputs ...<-chan int) <-chan int {
	c := make(chan int)
	for _, input := range inputs {
		go func(input <-chan int) {
			c <- <-input
		}(input)
	}
	return c
}
```

|Result |
|-------|
|s = 420|
