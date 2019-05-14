# Go note

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

The underlying array will be nil if you call variadic without any of argument.

```go
func main() {
	s := []int{4, 5, 6}
	fmt.Println(sum(1, 2, 3), sum(s...))
}

func sum(nums ...int) int {
	s := 0
	for _, n := range nums {
		s += n
	}
	return s
}
```

|Result|
|------|
|6 15  |

## Map

```go
func main() {
	m := make(map[string]int)
	m["ID_01"] = 20
	fmt.Println(m)

	m = map[string]int{"ID_02": 30}
	fmt.Println(m)
}
```

|Result       |
|-------------|
|map[ID_01:20]|
|map[ID_02:30]|

## Interface 

Interface is like `protocol` in Swift.
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



