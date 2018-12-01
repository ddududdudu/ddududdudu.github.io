### `nil` 이란 무엇일까?
`nil`은 `golang`의 built-in identifier 중 하나이다. 처음 `golang`을 접하면 다른 개발 언어의 `NULL` 이라는 개념과 혼동하기 쉽다.  
하지만 `golang`에서 nil은 `map`, `slice`, `interface`, `pointer`, `channel`, `function` type 들의 default value(zero value) 이며, `nil` 자체는 값이기 때문에 당연히 type을 가지게 된다. (유명한 `typed-nil 이슈`는 이 때문에 발생한다.)  
심지어 `nil`은 값이기 때문에 당연히 `keyword`가 아니다. 즉, `nil` 자체를 정의할 수 있다. (해당 scope에서의 shadowing)
```go
package main

import (
    "fmt"
    "reflect"
)

func main() {
    fmt.Println(reflect.TypeOf(nil), nil)

    nil := "test"
    fmt.Println(reflect.TypeOf(nil), nil)
}
```
[`C`와 같은 언어에서 `NULL`이 0으로 정의 된 매크로]()임을 생각해보면 그리 이상한 일은 아니다.  
위 [테스트 코드](https://play.golang.org/p/jqjHTQAhZE4)에서 `nil` 이란 값에 대한 type을 reflection을 이용해 확인해보면 `nil` 임을 알 수 있다.  
즉, `nil`은 `nil` 이라는 type(타입이 정해지지 않음)과 value(값이 정해지지 않음)를 가지는 pre-built indentifier로 정의할 수 있다.

### Typed `nil` issue
`nil` 이라는 값은 default type이 없다. (default type이 `nil` 이다.)  
`123`이라는 literal을 통해 `int` type을 추론할 수 있고, `"test"` 라는 literal을 통해서는 `string` type을 추론할 수 있지만,
`nil` 이라는 값은 `map`이나 `slice` 등 하나의 type이 아닌 다수의 type의 값이 될 수 있다. 즉, value 만으로는 type을 추론할 수 없다.  
