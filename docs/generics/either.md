# Either

## Example

```go
package main

import (
	"fmt"
	"log"

	"golang.org/x/exp/constraints"

	"github.com/go-asset/generics/either"
	"github.com/go-asset/generics/list"
	"github.com/go-asset/generics/maybe"
)

func main() {
	result := list.FoldlIter(
		maybe.Nothing[int](),
		readFromUser(),
		add[string, int],
	)

	fmt.Println(result)
}

/*
 * Generic function, to add values (concatenation for string).
 */

 func add[L any, R constraints.Ordered](c maybe.Maybe[R], value either.Either[L, R]) maybe.Maybe[R] {
	if !value.IsRight() {
		log.Printf("Skip: %v\n", value.Left())

		return c
	}

	if c.IsNothing() {
		return maybe.Just(value.Right())
	}

	return maybe.Just(c.Value() + value.Right())
}

/*
 * Helper functions to generate content, like if it's coming from a user.
 */

func readFromUser() chan either.Either[string, int] {
	ch := make(chan either.Either[string, int])

	myList := []either.Either[string, int]{
		either.Right[string](12),
		either.Right[string](2),
		either.Left[string, int]("asd"),
		either.Right[string](8),
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
