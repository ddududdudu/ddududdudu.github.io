`Golang` 에서는 `enum` type이 존재하지 않지만, 아래와 같이 라벨링을 해서 유사한 형태를 가져볼 수 있다.
  
```go
type EnumValue string

const (
    Value1 EnumValue "value1"
    Value2 EnumValue "value2"
    Value3 EnumValue "value2"
)
```

