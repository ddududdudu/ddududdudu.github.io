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

### 
