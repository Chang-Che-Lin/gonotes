#Go note

# fmt

```go
fmt.Printf()
fmt.Println()
```

# String

```go
func main() {
	fmt.Println(`This is a "raw string"`)
}
```

|Result|This is a "raw string"|
|------|----------------------|

# Slice

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

|Result|6 15|
|------|----|

# Interface 

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

|Result|Type: int, Value: 10|
|------|--------------------|
