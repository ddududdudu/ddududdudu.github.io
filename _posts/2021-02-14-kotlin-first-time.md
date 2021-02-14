---
layout: post
title: "[kotlin] 첫 인상"
description: ""
categories: blog
tags: [kotlin]
image:
feature:
date: 2021-02-14T21:31:50-04:00
---

Kotlin을 main 개발 언어로 사용 중인 다른 팀의 다른 프로젝트의 코드를 직접 수정 해야 할 경우가 생겼다. 
짧은 코드지만 처음으로 Kotlin으로 코드를 작성 해보고, 리뷰를 받아 고쳐 나간 과정을 기록 해본다.

### 첫 코드
해야 할 일은 아래와 같았다.
- 어떤 DTO에 `List<T>` 타입의 필드를 추가 해야 함
- 해당 필드는 `Nullable` 임
- 값이 존재 하는 경우 리스트 내의 객체들을 다른 타입의 객체들로 컨버팅 해서 리스트 구성
- 새롭게 구성 된 리스트를 resposne에 설정

장황하게 설명 했지만, `null` 일 수 있는 `List` 필드에 대해 값이 존재하면 다른 타입으로 컨버팅 해서 새로운 리스트를 구성하는 간단한 코드가 필요했다.  
기존 처럼 자바로 작성한 코드에서는 이것을 아래 처럼 구현했다.

```Java
final List<ResponseAddedFiled> addedFileds = 
    Optional.ofNullable(sourceDTO.getAddedField())
                                 .map(Collection::stream)
                                 .orElseGet(Stream::empty)
                                 .map(addedField -> 
                                     ResponseAddedField
                                         .builder()
                                         .filedA(addedField.getPropA())
                                         .filedB(addedField.getPropB())
                                         .filedC(addedField.getPropC())
                                         .build())
                                 .collect(Collectors.toList());
```

`Nullable` 필드이기 때문에 `Optional`과 `Stream`을 이용 해서 변환한 객체의 리스트를 생성했다.  
Kotlin에 대한 지식이 전혀 없기 때문에, 수정 해야 할 클래스의 다른 Kotlin 코드를 스윽 훔쳐 보았다. `null` 핸들링을 `?` 연산자를 이용 하고 있구나 하는 통밥을 굴려서 아래 처럼 작성하고 PR을 올렸다.
```Kotlin
val addedFileds = 
    sourceDTO.addedFiled?.stream()
                        ?.map { addedField ->
                            ResponseAddedField
                                .builder()
                                .filedA(addedField.getFieldA())
                                .filedB(addedField.getFieldB())
                                .filedC(addedField.getFieldC())
                                .build()}
                        ?.collect(Collectors.toList())
```

### 두 번째 코드
이후 아래와 같은 코멘트를 받았다.
> - It seems good to use let to reduce the propagation of safe calls (?.).
> - No need to use stream because Iterable<T> supports map.

우선 두 번째 코멘트를 먼저 보면, `Iterable<T>` 이 `map` 연산을 지원하기 때문에 따로 `stream`으로 변환할 필요가 없다고 한다. Kotlin의 `Collection` 클래스의 메서드 시그니처를 찾아보니 아래와 같았다.
```Kotlin
/**
 * Returns a list containing the results of applying the given [transform] function
 * to each element in the original collection.
 * 
 * @sample samples.collections.Collections.Transformations.map
 */
public inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {
    return mapTo(ArrayList<R>(collectionSizeOrDefault(10)), transform)
}
```
- Kotlin의 컬렉션은 `map()` 메서드를 정의 하고 있다.
- 리턴 타입이 `List<R>` 이기 때문에 `Stream`을 사용할 때 처럼 별도의 최종 연산으로 콜렉팅 하지 않아도 된다.

라는 점을 알 수 있었다.

다음으로 불필요한 `safe call`(`?` 연산자를 말하는 것 같다)의 전파를 줄이기 위해 `let`을 사용 해보라고 한다.  
이 내용들을 적용해서 제안 된 코드는 아래와 같다. 

```Kotlin
val addedFields = 
    sourceDTO.addedField?.let {
        it.map { addedFiled ->
            ResponseAddedField.builder()
                .filedA(addedField.getFieldA())
                .filedB(addedField.getFieldB())
                .filedC(addedField.getFieldC())
                .build()}}
```
- `?` 연산자와 `let` 구문을 조합해서 사용 하는 경우 `non-null` 일 때의 동작을 별도 코드 블록으로 구분 가능하다.
- `let` 으로 정의 된 코드 블록은 람다 표현식으로 정의할 수 있는데, 람다 표현식의 매개변수가 단일 값인 경우 `it` 이라는 예약어를 사용할 수 있다.

라는 점을 알 수 있다.

### 세 번째 코드
고맙게도 다른 분께서 다시 한 번 코드를 아래처럼 다듬어 주셨다.
```Kotlin
val addedFields = 
    sourceDTO.addedField?.map { addedFiled ->
        ResponseAddedField.builder()
                          .filedA(addedField.getFieldA())
                          .filedB(addedField.getFieldB())
                          .filedC(addedField.getFieldC())
                          .build()}
```
필요한 동작 자체가 `map()`을 이용한 컨버팅 이기 때문에, 지금 상태에서는 별도 코드 블록 조차도 필요가 없고 따라서 `let` 사용이 불필요 했다는 것을 알 수 있다.

### 결론
- Java에서 null에 대한 핸들링을 위해 여러 헬퍼/유틸리티 클래스를 만들어서 사용 하고 있었는데 언어 자체에서 매끄럽게 지원 해주니 정말 좋다는 것을 느낌
- 올 해는 꼭 코틀린을 배워서 작은 부분에라도 적용 해보자
- 사실 별 내용은 아니지만 오랜만에 뭐라도 끄적이고 싶어서 정리 해봄