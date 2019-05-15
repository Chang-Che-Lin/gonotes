# Go note

- [fmt](#fmt)
- [String](#string)
- [Slice](#slice)
- [Map](#map)
- [Interface](#interface)
- [For](#for)
- [Function](#function)
- [JSON](#json)

---

## Types

byte = uint8

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

The underlying array will be nil if the variadic argument is empty.

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

The `circumference()` in struct `circle` use pointer receiver,
so we must pass a pointer of `circle` as argument for `printCircleInfo()`.

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

---

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
|This is a anonymous function|

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
			fmt.Println(people)
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
|[{sc chn 26} {gb chn 25}]|

The tag after var in struct is like `CodingKey` in Swift:
> Name string \`json:"RealName"\`

