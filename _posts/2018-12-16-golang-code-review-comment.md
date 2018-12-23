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

> Unseeded `math/rand`의 경우 `default source`를 사용하기 때문에, 프로그램이 실행 되는 동안의 값들이 이미 결정(deterministic) 되어 있다. `Seed()`를 이용해 새로운 source를 생성하는 경우에는 concurrent-safe 하지 않다는 점을 유의해야 한다. Security-sensitve한 부분인 경우 `crypto/rand'를 사용하자.


