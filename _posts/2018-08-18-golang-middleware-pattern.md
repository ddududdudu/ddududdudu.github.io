---
layout: post
title: "[golang] Middleware pattern"
description: "HTTP server에서 요청을 처리할 때 common하게 처리해야 하는 기능에 대한 middleware 패턴 작성."
categories: blog
tags: [golang]
image:
feature:
date: 2018-08-18T21:31:50-04:00
---

![](http://ramarson.com/blog/wp-content/uploads/2016/10/wsgi_middleware-300x273.png)
### 기본 HTTP server
```go
type CustomHandler struct {
  http.Handler
}

func (ch CustomHandler) ServeHTTP(r http.ResponseHandler, w http.Request) {
  /* Do something here to handle request. */
}

func main() {
  mux := http.NewServeMux{}
  
  handler := CustomHandler{}
  mux.Handle("/", handle)
  
  http.ListenAndServe(":8080", mux)
}
```
HTTP 요청을 처리하기 위해 ServeHTTP method를 구현하는 CustomHandler를 정의했다. Default multiplexor를 사용하면 http.Handler나 http.HandleFunc와 같은 shortcut을 사용할 수 있어 편하다. 하지만 default multiplexor는 http 패키지에서 global scope으로 노출 되어 있기 때문에 보안적인 이슈가 있으므로 local scope의 multiplexor를 정의해서 사용하는 것이 좋다. 핸들러가 동작할 때 공통적인 기능을 함께 처리해야 하는 경우가 있을텐데 (e.g. 인증, 로깅 등) 이러한 기능을 middleware로 정의하고, 핸들러를 bind하는 시점에 필요한 middleware를 설정하여 사용하면 편리하다.

### Middleware 정의
```go
type Middleware func(http.Handler) http.Handler

func HelloMiddleware() Middleware {
  return func(handler http.Handler) handler.Handler {
    return http.HandlerFunc(w http.ResponseWriter, r *Http.Request) {
      /* Do something in middleware while processing request. */
      
      handler.ServeHTTP(w, r)
    }
  }
}
```
http.Handler를 인자로 받고 http.Handler를 리턴하는 타입을 Middleware로 정의했다. 요청을 처리하는 핸들러를 wrapping 하는 형태의 핸들러로 재정의하고 이를 리턴하면 기존 핸들러와 동일한 signature를 가지게 된다. 실제로 middleware에서 처리하고자 하는 동작은 내부에서 리턴하는 http.HandlerFunc()의 body에 구현한다. 이것이 가능한 이유는 [http.HandlerFunc()](https://golang.org/pkg/net/http/#HandlerFunc)의 정의를 찾아보면 알 수 있다.
> The HandlerFunc type is an adapter to allow the use of ordinary functions as HTTP handlers. If f is a function with the appropriate signature, HandlerFunc(f) is a Handler that calls f.

http.HandlerFunc type을 receiver로 가지는 ServerHTTP(http.ResponseWriter, *http.Request)가 있기 때문에 결과적으로 http.HandlerFunc는 http.Handler를 구현하게 되고, 이를 이용해서 일반 function을 http.Handler로 이용하는 것이 가능하게 된다. 리턴하는 http.HandlerFunc()의 body에 원하는 동작을 구현하고 마지막으로 인자로 받았던 handler의 ServeHTTP()를 호출해준다.

```go
func SetMiddlewares(handler http.Handler, middlewares Middleware...) http.Handler {
  for _, middleware := range middlewares {
    handler = middleware(handler)
  }
  
  return handler
}
```
### Middleware 실행 
원래의 handler에 middleware를 bind하기 위한 function이다. 인자로 넘겨 받은 middleware들에 대해 순차적으로 bind하므로, 순서상 나중에 bind된 middleware가 먼저 실행 되게 된다. 실제로 middleware를 사용하도록 수정한 코드는 아래와 같다.

```go
func main() {
  mux := http.NewServeMux{}
  
  handler := CustomHandler{}
  mux.Handle("/", SetMiddlewares(handler, HelloMiddleware(), ByeMiddleware())
  
  http.ListenAndServe(":8080", mux)
}
```

현재 구현에서는 bind한 middleware들의 동작이 완료 되면 핸들러가 실행되게 되므로, 만약 middleware의 동작 시점을 핸들러의 동작 이후로 하기 원하는 경우 defer를 이용할 수 있다.
```go
func ByeMiddleware() Middleware {
  return func(handler http.Handler) handler.Handler {
    return http.HandlerFunc(w http.ResponseWriter, r *Http.Request) {
      defer func() {
        /* Do something in middleware while processing request. */
      }()
      
      handler.ServeHTTP(w, r)
    }
  }
}
```
