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
대부분의 기계적인 스타일 이슈를 자동으로 수정하기 위해 당신의 코드에 대해 `gofmt` tool을 실행하라. 대부분의 Go code에는 `gofmt` tool이 사용되고 있다. 이 문서에서는 비 기계적인 스타일 요소에 대해 접근한다. 
Run `gofmt` on your code to automatically fix the majority of mechanical style issues. Almost all Go code in the wild uses gofmt. The rest of this document addresses non-mechanical style points.

An alternative is to use goimports, a superset of gofmt which additionally adds (and removes) import lines as necessary.
