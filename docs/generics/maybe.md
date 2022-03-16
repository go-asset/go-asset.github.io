# Maybe

## Example

```go
package main

import (
	"fmt"

	"golang.org/x/exp/constraints"

	"github.com/go-asset/generics/list"
	"github.com/go-asset/generics/maybe"
)

func main() {
	result := list.FoldlIter(
		maybe.Just(0),
		readFromUser(),
		add[int],
	)

	resultStr := list.FoldlIter(
		maybe.Just(""),
		readFromUserStr(),
		add[string],
	)

	fmt.Println(result)
	fmt.Println(resultStr)
}

/*
 * Generic function, to add values (concatenation for string).
 */

func add[T constraints.Ordered](c maybe.Maybe[T], value maybe.Maybe[T]) maybe.Maybe[T] {
	if c.IsNothing() {
		return value
	}

	if value.IsNothing() {
		return c
	}

	return maybe.Just(c.Value() + value.Value())
}

/*
 * Helper functions to generate content, like if it's coming from a user.
 */

func readFromUser() chan maybe.Maybe[int] {
	ch := make(chan maybe.Maybe[int])

	myList := []maybe.Maybe[int]{
		maybe.Just(12),
		maybe.Just(2),
		maybe.Nothing[int](),
		maybe.Just(8),
	}

	go func() {
		for _, v := range myList {
			ch <- v
		}

		close(ch)
	}()

	return ch
}

func readFromUserStr() chan maybe.Maybe[string] {
	ch := make(chan maybe.Maybe[string])

	myList := []maybe.Maybe[string]{
		maybe.Just("asd"),
		maybe.Just("123"),
		maybe.Nothing[string](),
		maybe.Just(",./"),
	}

	go func() {
		for _, v := range myList {
			ch <- v
		}

		close(ch)
	}()

	return ch
}
```
