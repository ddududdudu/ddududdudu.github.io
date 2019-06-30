---
layout: post
title: "[golang] Modules"
description: "Go modules에 대한 기본적인 사용 방법"
categories: blog
tags: [golang, modules, usage]
image:
feature:
date: 2019-03-30T21:31:50-04:00
---

### Modules
`modules`는 `go`에서 패키지 의존성을 관리하기 위한 툴이다. 기존에는 표준 패키지 관리 툴이 존재 하지 않았기 때문에 관련된 여러 가지 제안이나 툴(`go glide`, `vgo`, `godep` 같은)이 존재 했었지만 개발자들의 요구를 충족하지 못해왔다. `modules`는 `1.11` 버전에 추가 되었으며, 환경 변수 `GO111MODULE`의 값이 `auto`인 경우 `$GOPATH/src` 경로에서 `go 명령어`를 수행하면 기존과 동일한 방식으로 동작하고 경로 밖에서 수행하는 경우는 `modules`의 방식으로 동작한다.   
`1.13`부터는 `modules`가 default로 동작한다.  

> `$GOROOT`와 `$GOPATH`는 더 이상 필요 없을까?  
> 
> `Go`를 처음 시작 하는 사람들에게 가장 혼란을 일으키는 부분 중 하나가 바로 `$GOROOT`와 `$GOPATH`를 설정해야 하는 일일 것이다.  
> 나름 모던 언어인데도 불구하고 이러한 세련되지 못함에 시작 부터 돌아서는 분도 보았고..  
> 결론 부터 얘기 하자면 (당연하게도) `$GOROOT`는 필요하고, `$GOPATH`는 설정 할 필요가 없다.  
> `$GOROOT`의 경우야 당연히 툴체인이나 기본 라이브러리에 대한 path이기 때문에 당연히 존재 해야 하지만, 프로젝트에 대한 workspace 개념 이었던 `$GOPATH`는 더 이상 설정할 필요가 없어졌다.  
> 그럼 여기서 궁금해지는 것이, `$GOPATH/src/...`의 경우 `modules`가 대체 하더라도, `$GOPATH/bin/...` 이나 `$GOPATH/pkg/...`는 어떻게 되는 것일까?  
> 결론 부터 얘기 하자면 별도로 `$GOPATH`가 설정 되지 않은 경우 `default $GOPATH`라는 것을 사용하게 된다.  
> `default $GOPATH`는 `UNIX like system`인 경우는 `$HOME/go`, `Windows`의 경우 `%USERPROFILE%\go` 경로가 선택 된다.  
> 만약 `$GOPATH`를 별도로 설정하지 않은 상태에서(`defalut $GOPATH`를 사용하는 상태에서) `$GOROOT`가 `defalut $GOPATH`와 동일하게 설정 되어 있다면,  
> `$GOROOT`의 변경을 막기 위해 오류가 발생하게 된다. 

### Nutshell
- 초기화 : `go mod init {package.name}`
- 의존성 추가
  - `go build` or `go test` 등 패키지 빌드성 커맨드 : 현재 코드를 기준으로 의존성을 체크하고 추가가 필요한 패키지가 발견 되는 경우 해당 패키지의 `latest` 버전을 추가
  - `go get {packange}.{version}` : 이미 존재하는 패키지의 경우 지정한 버전으로 갱신, 없을 경우 신규 추가
- 의존 패키지 다운로드 : `go mod download`
- 정리 : `go mod tidy`
- 조회 : `go list -m`

### 초기화
`go mod init {package_name}` 커맨드를 이용하여 현재 경로에서 `package_name`을 root로 가지는 `module`을 초기화 한다. 즉 내부 패키지에 접근하는 경우 `import {package_name}/...`으로 할 수 있다. 실행 결과로 해당 경로에 `modules` 프로젝트임을 나타내는 `go.mod`와 `go.sum`이 생성 된다.  

#### go.mod
```go
module com.study.go/modules

require (
  ...
  cloud.google.com/go v0.34.0 // indirect
  github.com/denisenkom/go-mssqldb v0.0.0-20181014144952-4e0d7dc8888f // indirect
  ...
  github.com/go-sql-driver/mysql v1.4.1
  ...
}
```
`modules`는 `semantic versioning`을 사용한다. 간단히 이해하면 버전을 명시할 때 `v{major}.{minor}.{patch}`와 같은 포맷을 사용 하는 것을 의미하는데, `modules`에서는 저 각각의 필드별 의미를 이용해 버전 관리를 하기 때문에 이를 이해 하는 것이 중요하다.  
![ref:www.geeksforgeeks.org](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/semver.png)  
위 `go.mod`의 내용에서 `//indirect`라는 주석은 이 패키지가 코드에 직접적으로 명시된 의존성이 아닌 다른 패키지에 의해 간접적으로 참조하고 있는 패키지 임을 알려준다.  
`github.com/go-sql-driver/mysql` 패키지의 경우 버전이 `v1.4.1`로 깔끔하게 표기 되어 있는 반면, `github.com/denisenkom/go-mssqldb` 패키지의 경우 `v0.0.0-20181014144952-4e0d7dc8888f`로 표기 된 것을 볼 수 있는데, 이는 tagging 되지 않은 버전을 참조하기 위해 임의로 생성한 `pseudo version`이기 때문이다.  
`modules`에 의해 관리되는 패키지의 정보를 확인하고 싶을 경우 `go list -m` 커맨드를 이용할 수 있다.  

#### go.sum
현재 프로젝트에서 의존성을 가지는 패키지들(`go.mod`에서 관리되는)의 특정 버전에 대한 hash 값을 관리하는 파일이다. 악의적이거나 의도치 않은 변경 등에 대비하고, 처음 다운로드한 패키지와 bit level로 동일함을 보장하기 위해 사용한다. `go.mod`와 함께 pair로 VCS에 관리 되어야 한다.

### 의존성 추가와 업데이트
새로운 패키지에 대한 의존성을 추가하고 싶을 경우 패키지를 빌드 하는 성격의 커맨드들, 예를 들어 `go 커맨드`를 수행하면 자동으로 `go.mod`와 `go.sum`에 반영된다. 하지만 이 경우 각 패키지의 최신 commit 혹은 release를 기준(`go get {package}@latest`)으로 설정하기 때문에, 특정 버전임을 명시해서 의존성을 추가하고자 하는 경우 `go get {package}@{release or commit}`을 이용한다.  
> 여기서 `최신(Latest)`의 의미는 아래 우선 순위로 결정 된다.
> 1. `non-prerelease`인 stable tag
> 2. `prerelease` stable tag
> 3. `untagged` 버전 중 가장 최신

### Major 버전 업데이트


