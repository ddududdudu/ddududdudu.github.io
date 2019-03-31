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

### 초기화
`go mod init {package_name}` 커맨드를 이용하여 현재 경로에서 `package_name`을 root로 가지는 `module`을 초기화 한다. 즉 내부 패키지에 접근하는 경우 `import {package_name}/...`으로 할 수 있다. 실행 결과로 해당 경로에 `modules` 프로젝트임을 나타내는 `go.mod`와 `go.sum`이 생성 된다.

#### go.mod
```
module com.study.me/modules

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
위 `go.mod`의 내용에서 `//indirect`라는 주석은 이 패키지가 코드에 직접적으로 명시된 의존성이 아닌 다른 패키지에 의해 간접적으로 참조하고 있는 패키지 임을 알려준다.  
`github.com/go-sql-driver/mysql` 패키지의 경우 버전이 `v1.4.1`로 깔끔하게 표기 되어 있는 반면, `github.com/denisenkom/go-mssqldb` 패키지의 경우 `v0.0.0-20181014144952-4e0d7dc8888f`로 표기 된 것을 볼 수 있는데, 이는 tagging 되지 않은 버전을 참조하기 위해 임의로 생성한 `pseudo version`이기 때문이다.  
`modules`에 의해 관리되는 패키지의 정보를 확인하고 싶을 경우 `go list -m` 커맨드를 이용할 수 있다.  

#### go.sum
현재 프로젝트에서 의존성을 가지는 패키지들(`go.mod`에서 관리되는)의 특정 버전에 대한 hash 값을 관리하는 파일이다. 악의적이거나 의도치 않은 변경 등에 대비하고, 처음 다운로드한 패키지와 bit level로 동일함을 보장하기 위해 사용한다. `go.mod`와 함께 pair로 관리 되어야 한다.

### 의존성 추가
새로운 패키지에 대한 의존성을 추가하고 싶을 경우 `go 커맨드`를 수행하면 자동으로 `go.mod`와 `go.sum`에 반영된다. 하지만 이 경우 각 패키지의 최신 commit 혹은 release를 기준으로 설정하기 때문에, 특정 버전임을 명시해서 의존성을 추가하고자 하는 경우 `go get {package}@{release or commit}'을 이용한다.
