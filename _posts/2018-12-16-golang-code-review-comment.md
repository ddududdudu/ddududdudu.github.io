---
layout: post
title: "[golang] Code Review Comment"
description: Golang project에 대한 code review 진행 시 중점적으로 보아야 할 common mistake.
categories: blog
tags: [golang]
image:
feature:
date: 2018-12-16T16:08:50-04:00
---

팀에서 개발 언어를 `golang`으로 지정하고 코드 리뷰에 대한 절차와 방법을 고민하면서, 동료의 코드를 리뷰할 때 중점적으로 확인해야 할 부분에 대한 레퍼런스를 찾아보고 있다. 로직에 대한 부분은 개발자 당사자 만큼의 이해가 동반되지 않는 이상, 해당 코드가 구현 되기까지 고민 되었던 여러 요소에 대한 맥락을 알기 어렵다. 그래서 `golang` 이라는 언어로 코딩된 코드 자체에 대한 리뷰로 시작 해보려고 한다. 다행히 이런 고민에 대한 문서가 `Go Wiki`에 [CodeReviewComments](https://github.com/golang/go/wiki/CodeReviewComments) 라는 이름으로 존재한다. 문서에서는 코딩 스타일 가이드가 아님을 밝히고 있지만, 이런 기본적인 룰은 잠재된 많은 문제점을 미리 줄여줄 수 있기 때문에 (내부 합의 후) 따르는 게 좋을 것 같다. (시간이 되면 [`Effective Go`](https://golang.org/doc/effective_go.html)도 정리 해보아야겠다.)

### Gofmt
대부분의 기계적인 스타일 이슈를 자동으로 수정하기 위해 당신의 코드에 대해 `gofmt` tool을 실행하라. 대부분의 Go code에는 `gofmt` tool이 사용되고 있다. 이 문서에서는 비 기계적인 스타일 요소에 대해 접근한다. `gofmt`의 superset 격인 `goimport` tool을 사용하는 것이 대안일 수 있다. 
> `gofmt`를 사용하는 것은 여러 개발자에 의해 관리 되는 프로젝트에 기본적인 통일성을 부여할 수 있는 좋은 방법이라고 생각한다. 여러 가지 사소한 논쟁을 방지할 수 있고(e.g. tab or spaces) 다른 사람의 코딩 결과물에 대한 이질감을 줄여주기 때문에 문제에 집중할 수 있게 해준다. `goimport`는 코드에서 사용 중인 패키지에 대한 `import` 구문이 누락되거나, 사용하지 않는 패키지에 대한 `import` 문이 존재 하는 경우 자동으로 추가/삭제 해주는 툴이고, 여기에 더해 `gofmt`툴의 역할까지 해준다. 요즘은 IDE에서 커밋 생성 시 해당 툴을 자동으로 실행할 수 있게 해주기 때문에 편리하게 이용할 수 있다.

### Comment Sentences
https://golang.org/doc/effective_go.html#commentary 를 보라. 다소 과하다 싶더라도 선언에 대한 코멘트는 `완전한 문장`이어야 한다. 이러한 접근은 `godoc`을 위한 포맷팅에 도움이 된다. 코멘트는 설명하고자 하는 함수 또는 변수의 이름으로 시작해야하며, 하나의 문장으로 종료 되어야 한다.
```go
// Request represents a request to run a command.
type Request struct { ...

// Encode writes the JSON encoding of req to w.
func Encode(w io.Writer, req *Request) { ...
```
> 주석이 필요 없을 정도로 코딩을 하라고는 하지만 당장 어제 짠 코드를 보고도 '이걸 내가 했던가?..' 하는 나로서는 코멘트의 중요함은 아무리 강조해도 모자름이 없다. 내가 만든 패키지를 타인에게 공개하고 이를 `godoc`을 통해 문서화 해본 경험은 없지만, 잘 정리된 단순한 코멘트 만으로 패키지를 문서화할 수 있다는 점은 큰 장점인 것 같다.

### Contexts
`context.Context` type은 security cendentials, tracing information, deadlines, 그리고 cancelation signal 등을 API와 프로세스 경계를 가로질러 전달할 수 있다. Go 프로그램은 들어오는 RPC 및 나가는 HTTP 요청에 이르기까지 전체 function call chain에서 `Context`를 명시적으로 전달한다.  

`context.Context`를 사용하는 대부분의 함수는 이를 첫 번째 파라미터로 받아들인다.
```go
func F(ctx context.Context, /* other arguments */) {}
```  

특정한 요청을 한 적 없는 함수의 경우 `context.Background()`를 사용할 수는 있지만, 당신이 필요하지 않다고 판단한 경우에도 `Context`를 전달하는 측면의 오류가 있을 수 있다. 보통의 경우 `Context`를 전달하지만, 대체할 수 있는 방법에 문제가 있다고 판단하는 경우에만 `context.Background()`를 직접 사용하라.  

`struct` type에 `Context` 멤버를 추가하지 말라. 대신 함께 전달해야 할 필요가 있는 각 타입의 메서드에 `ctx`를 parameter로 추가하라. 단 하나의 예외는 표준 라이브러리 또는 third-party 라이브러리의 인터페이스에 대한 signature를 일치시켜야 할 때 뿐이다.  

function signature에`Custom context` type을 만들거나 `Context`이외의 인터페이스를 사용하지 말라.

만약 프로그램 전체에 유통 되어야 하는 데이터가 존재하는 경우 이를 파라미터로 전달하거나, 리시버의 멤버로 정의하거나 전역 변수를 사용하라. 정말로 그래야 한다고 생각하는 경우에만 `Context`의 value로 추가하라.

`Context`는 불변이다. 따라서 같은 deadline, cancellation signal, credentials, trace 정보 등을 공유하기 위해 같은 `ctx` 인스턴스를 2회 이상의 요청에 대해 전달하는 것에 문제가 없다.
> Go 1.7에 추가된 `context`에 대한 내용이다. 말 그대로, 하나의 동작 체인(e.g. HTTP 서버의 경우 request를 처리하고 response를 전달 하기 까지)에서 유툥되는 정보의 맥락을 가리킨다. 하나의 `context`를 메서드의 인자로 넘겨줌으로써 전체 맥락을 공유할 수 있다. 따라서 특정 함수에서 `context.Background()`를 사용하여 새로운 `context`를 생성하는 것은 전체 프로그램의 동작에 있어서 `context`의 유통을 깨뜨리는 결과를 만들 수 있음을 주의해야 한다.

### Copying
예상하지 못한 aliasing을 피하기 위해, 다른 패키지로부터의 `struct`를 복사하는 경우 주의할 필요가 있다. 예를 들어, `bytes.Buffer` type은 작은 string에 대한 최적화로서 []byte를 포함하며, 이 byte slice가 참조하는 작은 byte array를 가진다. 만약 `bytes.Buffer`를 copy하는 경우 slice의 복사본에 의해 byte array에 대한 aliasing이 존재하게 되어 이로인한 의도치 않은 결과를 초래할 수 있다.  
보통의 경우, 포인터 리시버를 사용하는 메서드가 포함 된 `type T`의 경우 복사하지 말라.
> 이 경우는 당장 눈에 띄지 않더라도 문제가 발생할 소지가 크고, 이슈가 발생 했을 때 파악하기 쉽지 않기 때문에 처음 부터 유념하고 개발을 하는 것이 좋다.

### Declaring Empty Slices
Empty slice를 정의할 때 아래와 같이 `nil` slice를 정의하는 것을 추천한다.
```go
var t []string
```
아래 처럼 `non-nil` slice를 정의 할수도 있다.
```go
t := []string{}
```
두 slice의 경우 `len`과 `cap`이 모두 `zero`이므로 기능적으로는 동일하다. 하지만 `nil` slice가 선호되는 스타일이다.  
JSON 인코딩의 경우 처럼 `nil` slice 보다 `ㅜnon-nil` slice가 선호되는 경우가 있을 수 있다. (`nil` slice는 `null`로 인코딩 되지만 `non-nil` slice는 array `[]`로 인코딩 됨)  
`interface`를 디자인 하는 경우 `nil` slice와 `non-nil` slice, `zero-length` slice간의 차이를 만들지 않도록 주의해야 한다. 이는 사소한 프로그래밍 에러를 만들 수 있다.  
`nil`에 대한 자세한 논의는 [`Francesc Campoy's talk Understanding Nil`](https://www.youtube.com/watch?v=ynoY2xz-F8s)을 참고하자.
> `non-nil` slice 보다 `nil` slice를 선호하는 이유가 뭘까. length와 capacity가 0으로 동일하고, 따라서 `for`와 같은 loop에서도 동일하게 처리 되며, 요소를 추가하기 위해 `append`를 사용할 때도 동일하게 동작한다. 해당 슬라이스가 `nil`이냐 아니냐가 존재할 뿐이다. slice는 실제로 데이터를 담고있는 array를 가리키는 메타 정보를 가지고 있는 일종의 포인터이므로, 결국 '가리키고 있는 메모리가 있냐 없냐'라는 표현의 문제로 보인다. 즉, '초기화 되지 않아 가리키는 메모리 공간이 없는' `nil` slice이냐, '초기화 되어 가리키는 메모리 공간이 있지만 비어있는' `non-nil` slice이냐.

### Crypto Rand
한 번 사용 후 버리는 키 값 일지라도 생성할 때 `math/rand` 패키지를 사용하지 말라. 시드 되지 않았고(unseeded) 완전하게 예측 가능하다. `time.Nanoseconds()`로 시드 하는 경우는 약간의 복잡도가 존재한다. 대신에 `crypto/rand`의 `Reader`를 사용하고, 만약 텍스트 형태의 아웃풋을 원하는 경우는 hexadecimal 또는 base64로 출력하라.
```go
import (
    "crypto/rand"
    // "encoding/base64"
    // "encoding/hex"
    "fmt"
)

func Key() string {
    buf := make([]byte, 16)
    _, err := rand.Read(buf)
    if err != nil {
        panic(err)  // out of randomness, should never happen
    }
    return fmt.Sprintf("%x", buf)
    // or hex.EncodeToString(buf)
    // or base64.StdEncoding.EncodeToString(buf)
}
```

> Unseeded `math/rand`의 경우 `default source`를 사용하기 때문에, 프로그램이 실행 되는 동안의 값들이 이미 결정(deterministic) 되어 있다. `Seed()`를 이용해 새로운 source를 생성하는 경우에는 concurrent-safe 하지 않다는 점을 유의해야 한다. Security-sensitve한 부분인 경우 `crypto/rand` 패키지를 사용하자.

### Doc Comments
모든 최상위, exported symbol은 중요한 unexported type이나 function 정의와 같이 `doc comment`를 가져야 한다. Commentary convention에 대한 자세한 내용은 [commentary](https://golang.org/doc/effective_go.html#commentary)를 참고하자.
> `Comment Sentences`와 이어지는 내용이다.

### Don't Panic
[errors](https://golang.org/doc/effective_go.html#errors)를 참고하자. 일반적인 에러 핸들링을 위해 `panic`을 사용하지 말라. `error`와 `multiple return value`를 사용하자.
> `go`에서는 `exception`이라는 개념이 없기 때문에, `panic`과 `multiple return value`를 이용해 에러를 핸들링한다. `multiple return value`라는 것은 간단한데, function이나 method에서 리턴 하고자 하는 값에 추가해 `error`를 리턴 하는 것이다. 일반적으로 `error`를 마지막 리턴 값으로 사용한다. 또 하나의 에러 핸들링 방법인 `panic`은 명시적으로 built-in function `panic()`을 이용하거나, 혹은 런타임 에러 등에 의해 발생한다. `go compiler`에 발견될 수 없는 런타임 에러가 발생 하는 경우, 즉시 프로그램의 실행이 중지 되고, 모든 `defered` 함수들이 실행 된 후, stack trace 메시지를 출력하면서 종료된다. `panic()`을 호출하는 경우 명시적으로 이러한 일련의 동작을 하도록 의도할 수 있다. 하지만 명심해야 할 것은, `panic`이 발생했다는 것은 곧 실행 중인 프로그램의 정지를 의미(정확히는 해당 go-routine)한다는 것이다. 또 다른 built-in function인 `recover()`를 이용해 이러한 `panic`을 핸들링 해볼 수 있지만(`recover()`의 역할은 단순히 `panic`의 발생 여부를 확인하는 것 뿐만이 아니라, `panic`이 발생한 프로그램의 `제어권`을 다시 가져오는 것이다), `정말로, 제대로` 처리할 수 있을지 확신을 갖는 것은 굉장히 어려운 일이며, 제대로 처리 되지 못한 `panic`의 후속으로 이루어질 수 있는 오동작이 어떤 결과를 초래할지는 알 수 없다. 즉, 단순히 에러를 처리 하는 것에는 큰 영향이 없겠지만, `panic`을 핸들링한 후 정상적인 실행 환경으로 간주하여 계속해서 프로그램을 동작 시키는 것에는 주의해야 한다. 외부에서 패키지 형태로 import 하여 사용하는 경우, 패키지 내부에서 발생한 패닉을 caller에게 전파시키지 않기 위해 `panic-recover`를 사용하는 모습은 괜찮을 것 같다.

### Error Strings
`Error` 문장은 보통 다른 문장들과 함께 출력 되기 때문에 일반적인 명사나 약어로 시작하지 않는 이상 반드시 대문자화 하지 않아야 하고, 마침표로 종료 되지 않아야 한다. `fmt.Errorf("Something bad")`가 아닌 `fmt.Errorf("something bad"`로 사용해야 하는 이유인데, `log.Printf("Reading %s: %v", filename, err)`와 같은 포맷에서 이상한 대문자 문장이 중간에 끼지 않게 하기 위함이다. 이러한 룰은 로깅에는 적용하지 않는데, 암묵적으로 줄 단위로 처리되며 다른 메시지와 조합되지 않기 때문이다.
> 사용 중인 IDE인 goland에서 warning이 뜨는 것이 싫어서 저 룰에 따르고는 있었지만, 처음에는 뭔가 불편했었다. 하지만 역시 저러한 이유가 존재했던 것 처럼, 에러 메시지는 여러 layer를 거쳐 전달 되거나 혹은 추가로 wrapping 하거나 하는 등의 특성을 가질 수 밖에 없으므로 결국에는 인정하고 있다.

### Examples
새로운 패키지를 추가할 때, 실행 가능한 Example 또는 전체 call sequence를 보여주는 간단한 테스트 데모와 같은 의도한 사용 예제를 포함하라.
[Testable Example() function](https://blog.golang.org/examples)을 참고하자.
> `Example`은 흔히 사용하는 `Test`와 마찬가지로 `go test` built-in tool에 의해 동작한다. `Test`와의 차이점은 Example이란 prefix로 시작하며, 별도의 인자를 받지 않고, Example naming rule에 따라 `godoc`에 의해 적절한 패키지, 함수의 예제로 문서화 될 수 있다는 것이다. 위에 소개했던 Documentation 관련 항목들과 같이, 이용하면 유용해지는 항목이다.

### Goroutine Lifetimes
`goroutine`을 생성할 때, 이것이 언제 종료 되는지, 종료 되긴 하는건지를 명확히 하라.  
  
`goroutine`은 `channel`에 의한 송수신에 의해 블록 되거나 누수가 발생할 수 있다. `Garbage collector`는 `goroutine`이 block 된 `channel`에 의해 관리할 수 없게 되더라도(unreachable) 이를 종료 시키지 않을 것이다.   
  
`goroutine`이 누수되지 않더라도, 더 이상 사용하지 않는 `goroutine`을 동작 상태로 두는 것은 또 다른 미묘하고 알아채기 힘든 문제들을 유발할 수 있다. Closed `channel`에 송신하는 것은 `panic`을 일으킨다. 결과를 필요로 하지 않게된 이후 사용 중인 입력 값의 수정은 데이터 경합을 일으킬 수 있다. 또한 임의의 긴 시간 동안 `goroutine`을 동작중인 상태로 내버려 두는 것은 예상치 못한 메모리 사용을 일으킬 수 있다.   
  
`goroutine`의 lifetime이 명확해질 수 있도록 동시성이 필요한 코드는 충분히 간단하게 유지하도록 노력하라. 만약 그것이 명확하지 않다면, 언제/왜 `goroutine`이 종료 되는지 문서화하라.
> `golang`을 사용하는 개발자라면 누구나 인정 하듯, `goroutine`은 간단하게 동시성을 제공해주는 강력한 도구이다. 많은 부분들이 `go runtime`에 의해 관리되고 추상화 되어 있어 많은 신경을 쏟지 않아도 될지라도, 생성한 `goroutine`이 언제 어떻게 종료 되는지, 동시에 접근되는 자원에 대한 충분한 고려가 되고 있는지는 결국 개발자의 판단이 필요한 부분이다.

### Handle Errors
[Effective Go - Errors](https://golang.org/doc/effective_go.html#errors)를 보라. 만약 function이 `error`를 리턴 한다면, `_`로 무시하지말고, 그 동작이 성공했는지를 확실히 하기 위해 `error`를 확인하라. `error`를 다루고, 리턴하고, 정말로 예외적인 경우에는 `panic`을 발생하라.
> `golang`을 사용하면 수 많은 `error` 처리 구문으로 가득한 코드를 발견하게 된다. 실제 로직 보다도 에러 처리가 많은 경우도 있었다. 이게 최선인지, 나만 이런건지 많이 찾아보았지만 이게 최선이고 나만 이런 것도 아니었다. `error`를 무시한 대가는 소중한 새벽잠과 주말을 잃게 되는 결과로 나타날 수 있다. 명시적으로 `error`를 전달하고, 처리하자.

### Imports
이름 충돌을 피하기 위해 패키지를 re-naming 하는 경우를 제외하고 패키지의 이름을 바꾸는 것을 지양하라. 좋은 패키지 이름은 re-naming을 필요로 하지 않는다. 패키지 이름 충돌이 발생하는 경우, 가장 지역적인 혹은 프로젝트에 국한 된 import에 대해 rename하라.  

`Import`들은 그룹으로 구성하며, import 그룹 사이에는 공백으로 구분하라. Standard library pckage들은 항상 첫 그룹에 위치 하라.
```go
package main

import (
	"fmt"
	"hash/adler32"
	"os"

	"appengine/foo"
	"appengine/user"

        "github.com/foo/bar"
	"rsc.io/goversion/version"
)
```
`goimport`가 당신을 위해 이를 해줄 것이다.
> `golang`은 '뭘 이런 것 까지?' 라는 생각을 하게 되는 순간이 자주 있다. 이것은 외부 패키지를 사용하기 위한 `import` 구문에 대한 내용인데, `golang`의 짖궂은 점 중 하나가 이런 것이다. 하지만 중요한 것은 `golang`은 이러한 것을 단순히 개발자의 짐으로 남겨두지 않고, 항상 이것을 자동화 할 수 있는 수단을 함께 제공한다. `gofmt` 항목에서 소개했던 `goimport` 툴을 쓰기만 하면 된다. built-in 툴이므로 이미 존재하며, 이 것 외에 생산적이지 않은 여러 요소들을 통일하여 준다. 쓰지 않을 이유가 없다.

### Import Blank
`Side effect`만을 위해 import 되는 패키지는(`_` "pkg" 문법을 사용하는 import) 프로그램의 main package 혹은 실제로 그들을 필요로 하는 테스트 내에서만 import 되어야 한다.
> 우선 `side effect`가 뭔지 알아야 한다. DB를 사용 하는 경우 가장 흔히 이 패턴을 사용하게 되는데, `import _ "github.com/go-sql-driver/mysql"` 와 같은 경우다. 'mysql' 패키지는 'database/sql' 패키지가 내부적으로 사용하는 'mysql'의 'driver' 패키지이다. 프로그램에서 직접적으로 'mysql' 패키지를 사용하지 않기 때문에 `_`를 이용해 무시하고(그렇지 않을 경우 에러 발생), 단순히 'driver' 패키지를 import 함으로써 해당 패키지의 내부에서는 `init()` 함수, package scope의 변수 초기화 또는 이를 이용한 트릭 유사한 방법의 동작(e.g. `var _ = os.Remove("path/to/remove)`)을 수행할 수 있다. 이를 `import side effect`라고 표현한다. 이러한 맥락에서 보면 위 코멘트를 이해할 수 있는데, 이러한 `side effect` 동작은 분명히 해당 프로그램의 초기화 시점에 이루어져야 함이 분명하기 때문이다. `main` 패키지의 경우 해당 프로그램의 실질적인 entry point 이므로 적절한 위치이고, `test` 또한 해당 테스트가 동작하는 시점에서의 entry point이기 때문다.

### Import Dot
`import . "pkg"` 형태의 import는  순환 참조로 인해 테스트를 패키지의 일부로 포함할 수 없을 때와 같이 'test' 작성 시 유용하게 사용할 수 있다.
```go
package foo_test

import (
	"bar/testutil" // also imports "foo"
	. "foo"
)
```
위와 같은 경우('bar/testutil' package가 'foo' package를 import 한다는 가정), test file이 순환 참조로 인해 foo 패키지에 포함 될 수 없다. 따라서 `import .` 형태를 사용하여 실제로는 아닐지라도 'foo' 패키지가 마치 이 파일의 일부인 것 처럼 사용할 수 있다. 이 단 하나의 경우를 제외하고는 `import .` 형태를 사용하지 말라. 만약 그렇지 않을 경우 'Quux'와 같은 이름이 이 패키지의 최 상위 식별자 이름인지 혹은 import 한 패키지에 속하는 것인지 불명확하고, 이는 프로그램의 가독성을 떨어뜨릴 수 있다.
> 일단 이 항목에서 말하고자 하는 바는 `테스트 작성 시 순환 참조가 발생하는 경우를 제외하고는 . import를 사용하지 말라` 이겠고 이에는 동의하지만, 사실 이 항목에서는 조금 의아한 부분이 있다. 정확히는 예를 든 케이스에 대한 의견인데, 일단 나는 테스트를 작성할 때, `###_test` 형태의 패키지 명을 사용하고 `외부 패키지`에서 `테스트 하고자 하는 패키지`를 `사용하는 관점`으로 테스트를 작성한다. 정말로 이 패키지를 사용할 때 노출 되는 외부 인터페이스를 기준으로 테스트 되는 것이 합당하다고 생각하기 때문이다. 동일한 패키지에 테스트를 포함 시키는 이유는 unexported 요소들에 대한 테스트가 필요한 경우인데, 애당초 사용할 수 있는 flow가 없는 부분을 테스트 하려고 하는 것이 맞는 것일까? 이 항목에서 이야기 하려는 주제는 아니었겠지만, 외부에서 테스트 할 수 없는 코드는 동작하는 코드라고 볼 수 없다는 생각을 가지고 있는 나로서는 애당초 이러한 경우 자체가 거의 없어야 하는게 맞는 것 같다.

### Indent Error Flow
일반적인 코드에 대해 최소한의 들여쓰기를 유지도록 노력하고, `error` 핸들링의 경우 이를 첫번째로 다루라. 이는 일반적인 코드 동작 경로에 대해 시각적으로 검색할 수 있게 해주어 가독성을 높이게 해준다. 예를 들어 아래와 같이 쓰지 말라:
```go
if err != nil {
	// error handling
} else {
	// normal code
}
```
대신에, 아래와 같이 작성하라:
```go
if err != nil {
	// error handling
	return // or continue, etc.
}
// normal code
```
만약 if 구문이 아래와 같이 초기화 구문을 가진다면:
```go
if x, err := f(); err != nil {
	// error handling
	return
} else {
	// use x
}
```
그럴경우 이것은 아래와 같이 스스로의 라인을 가지는 단축 선언으로 변경할 필요가 있을 수 있다:
```go
x, err := f()
if err != nil {
	// error handling
	return
}
// use x
```
> 가독성의 문제인데, 같이 일하는 동료 뿐만 아니라 당장 내일의 나를 위해서라도 가독성은 가능하면 추구하는게 좋다. 솔직히 말하자면, 짧고 아름다운 코드 보다는 다소 길더라도 단순하고 바로 이해 가능한 코드가 좋다고 생각하는 편이다. `golang`의 경우 수 많은 error handling으로 인해 생각보다 코드가 길어지고 지저분해 보이는 경향이 있는데, 받아들이자.









