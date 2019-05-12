---
layout: post
title: "First-class object, anonymous function, clousre, lambda-expression"
description: "First-class object, 익명 함수, 클로저, lambda-expression에 대한 정리"
categories: blog
tags: [first-class object, anonymous function, closure, lambda-expression, 람다, 익명 함수, 클로저, 일급 객체]
image:
feature:
date: 2019-02-20T21:31:50-04:00
---

뭔가 서로 연관이 있어 보이면서도 명확히 정리 해보지 못했던 것들에 대해 정리해본다.

### First class object
프로그래밍 언어에서 `일급 객체(first-class object)`란 무엇일까? [정의](https://ko.wikipedia.org/wiki/%EC%9D%BC%EA%B8%89_%EA%B0%9D%EC%B2%B4)는 아래와 같다.
> 특정 언어의 일급 객체 (first-class citizens, 일급 값, 일급 엔티티, 혹은 일급 시민)이라 함은 컴퓨터 프로그래밍 언어 디자인에서 일반적으로 다른 객체들에 적용 가능한 연산을 모두 지원하는 객체를 가리킨다. 함수에 매개변수로 넘기기, 변수에 대입하기와 같은 연산들이 여기서 말하는 일반적인 연산의 예에 해당한다. *- Wikipedia*
  
`Object` 라는 개념에 대한 언어별 해석이 다를 수 있기 때문에 `First-class citizen` 과 같이 좀 더 추상화 시킨 표현도 사용하지만, 이 포스팅에서는 `object`를 유지하겠다.  
  
`일급 객체`로서의 조건을 좀 더 상세히 기술하면 아래와 같다.
1. 변수나 데이터 구조안에 담을 수 있음.
2. 파라미터로 전달할 수 있음.
3. 반환값으로 사용할 수 있음.
4. 할당에 사용된 이름과 관계없이 고유한 구별이 가능함.
5. 동적으로 프로퍼티 할당이 가능함.

예를 들어, `C`의 `array`는 `일급 객체`일까? 아래와 같이 예제를 작성해서 확인 해보자.
```c     
#include <stdio.h>

int getSizeOfArray(int array[][]) {
    return sizeof(array);
}

int getSizeOfFixedArray(int array[2][5]) {
    return sizeof(array);
}

int getSizeOfPointerArray(int (*array)[5]) {
    return sizeof(array);
}

int main() {
    int array[2][5];

    printf("Size of array is %d\n", sizeof(array));
    printf("Size of calculated array is %d\n", getSizeOfArray(array));
    printf("Size of calculated fixed array is %d\n", getSizeOfFixedArray(array));
    printf("Size of calculated array pointer is %d\n", getSizeOfPointerArray(array));
}

// Size of array is 40
// Size of calculated array is 8
// Size of calculated fixed array is 8
// Size of calculated array pointer is 8
```
`C`에서의 `pointer`와 `array`의 관계에 대해서는 이 [포스팅]()을 참고하자.  
우선, `getSizeOfArray()` 함수의 시그니처에서 확인할 수 있듯 이 함수는 `int[]` 타입의 파라미터를 받고 있다. 그렇다면 결과로 `40`이 나와야 할 것 같지만(4 bytes * 10개) ~~당연하게도~~ 포인터의 크기인 `8`이 출력된다. 이에 더해서, (컴파일러 설정에 따라 다르겠지만) 위 코드를 컴파일 하면 아래와 같은 경고 메시지를 확인할 수 있다.  
> warning: 'sizeof' on array function parameter 'array' will return size of 'int *' [-Wsizeof-array-argument] return sizeof(array);

`array의 이름`은 `array의 첫 번째 요소를 가리키는 포인터`이기 때문이다. `sizeof` operator는 단지 compile-time에 결정 가능한 값을 출력할 뿐이므로 해당 `array`가 선언 된 code block에서는 예상한 값을 얻을 수 있는 반면, 단순히 포인터가 전달 되는 매개 변수로서의 배열은 알 방법이 없다. 이는 아예 고정 크기 배열로서의 파라미터를 표현 하고자 한 `getSizeOfFixedArray()` 함수일지라도 마찬가지이다. `함수`의 `매개 변수`로 전달 되는 배열은 크기를 적더라도 컴파일러에 의해 무시되고 포인터로 해석된다. `배열 포인터`는 어떨까. 결국 `배열을 가리키는 포인터`이므로 역시 포인터의 크기가 출력 될 뿐이다.  
`C`의 `main()` 함수의 시그니처를 생각해 보면 아래 세 가지가 가능한데,
```c
int main();
int main(int argc, char** argv);
int main(int argc, char* argv[]);
```
`argc`가 왜 존재 할 수 밖에 없는지를 생각해보면 자명하다. 따라서 `C`에서 `array`는 `array` 자체로서 파라미터 전달이 불가능하므로 `일급 객체`가 아님을 알 수 있다.  

### Function as a first-class object
`일급 객체`로서의 `함수`란 무엇일까? 아래 `Go` 예제를 통해 확인해보자.
```go
package main

import (
    "fmt"
)

type CalcType string
const (
    add CalcType = "add"
    sub CalcType = "sub"
)

func AddFunc(a, b int) int {
    return a+b
}

func SubFunc(a, b int) int {
    return a-b
}

func CalcFunction(calcType CalcType) func(int, int) int {
    switch calcType {
    case add:
        return AddFunc
    case sub:
        return SubFunc
    default:
        fmt.Println("unsupported calculation type: " + calcType)
        return nil
    }
}

func main() {
    a := 10
    b := 5
    
    calcFunc := CalcFunction(add)
    fmt.Printf("add : %d\n", calcFunc(a, b))
    
    calcFunc = CalcFunction(sub)
    fmt.Printf("sub : %d\n", calcFunc(a, b))
}

// add : 15
// sub : 5
```
`CalcFunction()`은 전달 받은 인수에 따라 `func(int, int) int` 라는 시그니처를 가지는 함수를 리턴하고 있다. `AddFunc()` 이든 `SubFunc()` 이든 시그니처가 일치 하므로 리턴 된 함수는 변수 `calcFunc`에 할당 되고 이후 `calcFunc()` 호출을 통해 사용할 수 있다. 즉 함수를 변수에 대입하고, 인자로서 전달하고, 함수의 리턴 값으로 반환 할 수 있으므로 `Go` 의 `함수`는 `일급 객체`이다. `일급 객체로서의 함수` 개념의 부분 집합으로 `함수를 인자로 받을 수 있고 && 함수를 리턴할 수 있는 함수`를 `high-order function(고차 함수, 고계 함수)` 라고 한다.    

`Java`의 경우는 어떨까? `Java`의 세계에서는 `primitive type`을 제외한 모든 것들은 `Object`로 표현된다. `Function`이라는 개념(인자와 리턴 값을 가질 수 있는 코드 블럭)도 `Java`에서는 특정 `object`를 추상화하는 `class`의 `method`로서만 존재할 수 있을 뿐이다. 즉, 함수라는 형태의 코드 블록 자체만을 다루는 것을 언어적으로 지원하지 않는다.  

하지만 `C`의 경우는 조금 고민이 필요할 것 같다. `C`에서는 `function`을 가리키는 `pointer`인 `function pointer`를 이용하면 동일하게 함수를 대입/전달/반환 할 수 있다. 그럼 `C`에서도 `function`은 `일급 객체`인걸까? 위 예제를 `C`로 동일하게 구현하면 아래와 같다.
```C
#include <stdio.h>

typedef int(*fpCalcFunc)(int, int);

typedef enum e_CalcType {
    ADD = 0,
    SUB
} CalcType;

int AddFunc(int a, int b) {
    return a+b;
}

int SubFunc(int a, int b) {
    return a-b;
}

fpCalcFunc CalcFunction(CalcType calcFuncType) {
    switch (calcFuncType) {
        case ADD:
            return AddFunc;
            break;
        case SUB:
            return SubFunc;
            break;
        default:
            printf("unsupported calculation type: %s\n", calcFuncType);
    }
    
    return NULL;
}

int main() {
    int a = 10;
    int b = 5;
    fpCalcFunc calcFunc = NULL;
    
    calcFunc = CalcFunction(ADD);
    printf("add : %d\n", calcFunc(a, b));
    
    calcFunc = CalcFunction(SUB);
    printf("sub : %d\n", calcFunc(a, b));
}

// add : 15
// sub : 5
```
굳이 이런 예제가 아니더라도 이미 `C`에서 정렬을 위한 `qsort()` 호출 시, 비교하는 로직을 담은 `함수`를 인자로 전달하고 있음을 잘 알고 있다. 여튼 `C`의 `함수`는 `함수 포인터`를 이용하면 (함수 포인터)변수에 할당할수도 있고, 함수의 인자로 넘길수도 있으며, 함수의 리턴 값으로 반환할수도 있다.  
하지만 언어 자체적으로 `함수`를 표현하기 위한 키워드가 존재하지 않음으로 인해 `포인터`의 힘을 빌어 마치 `일급 객체`인 것 처럼 사용할 수 있는 것에 더 가깝다고 생각한다. (`포인터`를 이용해 `C++`의 `call-by-reference` 인 것 처럼 사용할 수 있지만 실상은 전달하는 값이 `address` 일 뿐인 것 처럼)  

### First-class function and anonymous function
`First-class function`이란 말 그대로, `First-class object` 로서의 `function`을 의미한다. 다섯 가지 `first-class object`의 조건에 더해 아래 두 가지 조건을 추가로 충족해야 한다.
1. 런타임 생성이 가능해야 함.
2. 익명으로 생성 가능해야 함.  

위에서 사용한 `Go`예제를 조금 바꾸어 보자.
```go
package main

import (
    "fmt"
)

type CalcType string
const (
    add CalcType = "add"
    sub CalcType = "sub"
)

func main() {
    a := 10
    b := 5
    
    calcFunc := CalcFunction(add)
    fmt.Printf("add : %d\n", calcFunc(a, b))
    
    calcFunc = CalcFunction(sub)
    fmt.Printf("sub : %d\n", calcFunc(a, b))
}

func CalcFunction(calcType CalcType) func(int, int) int {
    switch calcType {
    case add:
        return func(a, b int) int {
            return a+b
        }
    case sub:
        return func(a, b int) int {
            return a-b
        }
    default:
        fmt.Println("unsupported calculation type: " + calcType)
        return nil
    }
}

// add : 15
// sub : 5
```
`First-class function` 설명에서 사용한 예제와 동일하지만, 명시적으로 named function이었던 AddFunc()와 SubFunc()을 정의 하는 대신, 동일한 시그니처와 구현부를 가진 코드 블럭을 리턴하고 있다. 이름이 없는 `함수` 즉, `anonymous function`인 것이다. 이름이 없기 때문에 명시적으로 변수에 할당 해두지 않는 이상 `익명 함수`를 다시 접근할 수 있는 방법이 없고, 주로 이런 식으로 일회성 용도의 코드를 정의할 때 주로 사용 한다. `Java`에서 보통 `Thread` 생성 시 `Runnable` 인터페이스의 익명 구현체를 사용하곤 하는 패턴이 연상 될 것이다.

### Closure
`클로저`를 한 문장으로 정의해보자면 아래와 같다고 생각한다.
> 의존하는 객체에 대한 문맥을 유지하고 있는 코드 블록

아래 코드를 보자.
```go
package main

import (
    "fmt"
)

func Adder(base int) func(int number) int {
    return func(int number) int {
        return base + number
    }
}

func main() {
    fiveAdder := Adder(5)
    tenAdder  := Adder(10)
    
    fmt.Printf("Adder with base 5  : %d\n", fiveAdder(5))
    fmt.Printf("Adder with base 10 : %d\n", tenAdder(5))
}

// Adder with base 5  : 10
// Adder with base 10 : 15
```
`Adder()`는 `익명 함수`를 리턴 하는 `함수`이다. 특이한 점은 `Adder()`의 인자로 받은 값인 `base`를 내부에서 리턴 하는 `익명 함수`에서 사용하고 있다는 점이다. `base`는 `Adder()`의 파라미터로서 정의 된 `지역 변수`이고, 해당 코드의 실행이 끝나면 더 이상 사용할 수 없는 것이 보통이다. 하지만 반환 된 `익명 함수`가 해당 객체를 사용함으로 인해 의존성이 생기게 되고, 마치 외부 scope의 변수를 참조하는 것 처럼 사용하게 된다. 그렇지만 `base`의 값과 이에 대한 접근은 오로지 해당 코드 블록에서만으로 제한 되는 것이다. 즉, 특정 동작을 수행 하는 코드 블록이 생성 되는 시점의 모든 의존성을 내부에 함께 포함하고 있게 되고, 이는 `일급 함수`로서 `동작 흐름` 자체를 변수에 저장할 수 있게 된다. 이러한 형태를 `클로저`라고 하고, `Go`는 언어적으로 `클로저`를 지원한다.  

`C`에서의 `클로저`는 어떤 모습일까? 앞서 알아보았듯 `C`에서의 `코드 블록`에 대한 전달은 `함수` 자체를 `일급 객체`로서 전달하는 것이 아니라 `함수 포인터`를 이용하여 유사한 구현이 가능하도록 할 뿐이고, 당연하게도 외부 scope에 대한 참조 자체가 허용 되지 않으므로 `클로저`라는 개념이 없다. 하지만 `일급 함수`의 경우 처럼, 역시 흉내 낼 수는 있다. 아래 코드를 보자.
```c
#include <stdio.h>

void printResult(int(*add)(int), int number) {
    printf("Adder with base 5 :  %d\n", add(number));
}

int main() {
    int base = 5;
    int add(int number) {
        return base + number;
    }
    
    printResult(add, 5);
    
    return 0;
}

// Adder with base 5 :  10
```
`GCC`의 비표준 확장인 `nested functions`를 이용하여 외부 scope의 변수를 참조하는 `add()`를 사용하고 있다. 하지만 역시 언어적인 한계로 인해 어거지스러움은 피할 수 없다. `함수` 내부에 감추고자 하는 의도가 있는 변수인 `base`를 코드 블록에 포함 시켜 `함수` 형태로 반환할 수 없기 때문이다.  

`Java`의 경우는 어떨까. 
```java
public class Program {
    public interface Addable {
        public int add(int number);
    }
    
    public static void main(String[] args) {
	int base = 5;
	
	Addable fiveAdder = new Addable() {
	    @Override
	    public int add(int number) {
		        return base + number;
	    }
	};
	
	System.out.println("Adder with base 5 : " + fiveAdder.add(5));
    }
}

// Adder with base 5 :  10
```
`Java` 역시 `일급 함수`를 가질 수 없기 때문에 `Go`와 같은 언어 자체적인 `클로저` 지원은 없다. 하지만 위 코드처럼 `anonymous object`를 생성하고 외부 scope의 변수를 참조할 수 있다. 하지만 `Java`의 경우 이렇게 외부 scope에 대한 참조가 생기게 되면 해당 의존성이 있는 외부 변수의 값을 변경할 수 없게 된다. 즉, `read-only`로 취급되며, 이는 코드 흐름 상 의존성이 생기기 전이든 후든 관계 없다. 참조만 가능하게 되는 것이다. 이게 무슨 `클로저`냐 라는 생각이 들지만, `제한적 클로저`라는 표현으로 취급하기도 하는 모양이다.

### Lambda calculus
> 컴퓨터과학 및 수리논리학에서, 람다 대수 또는 람다 계산(λ-calculus, lambda-calculus)이란 변수의 네임 바인딩과 대입의 방법을 이용하여 함수 정의, 함수 적용, 귀납적 함수 추상화를 수행하고 수학 연산을 표현하는 형식 체계이다. *- [Wikipedia](https://ko.wikipedia.org/wiki/%EB%9E%8C%EB%8B%A4_%EB%8C%80%EC%88%98)* 

수학적 영역이므로 개념을 확인 하는 정도로만 정리해본다. (앞으로 `함수형 프로그래밍`을 학습하며 추가적인 보충/수정이 필요하다)  
`람다 대수`는 `함수`를 단순하게 표현할 수 있게 함으로써 `함수의 계산`이라고 하는 부분을 더 잘 이해할 수 있도록 한다. `람다 대수`를 통해 `함수`를 표현 함으로써 아래 사실을 확인할 수 있다.
1. 함수가 반드시 이름을 가질 필요는 없다.
2. 함수의 입력 변수 또한 이름을 가질 필요는 없다.
3. 두 개 이상의 입력을 받는 함수는 하나의 입력을 받아 또 하나의 함수를 출력하는 함수의 연속으로 다시 쓸 수 있다. (== `Currying`)

`함수형 프로그래밍`에 대해 표현하자면, 수학적으로 `람다 대수`의 개념에 맞게 모델링한 `함수`의 조합을 바탕으로 프로그램을 작성하는 것이라고 할 수 있을 것 같다.

### Lambda function
그렇다면 `람다 대수`의 개념에 따라 모델링한 `함수`를 `람다 함수`라고 부를 수 있을 것 같은데, 정확히 `람다 함수`란 무엇일까. `Go`에서는 `함수`의 이름과 그 `함수`의 입력 변수의 이름을 생략한 것, 즉 그 함수의 시그니처를 `func` 리터럴을 이용해 `익명 함수`로서 표현할 수 있다. 또한, 이 `익명 함수`에서 또 다른 `익명 함수`를 반환할 수 있고, 외부 scope의 변수를 바인딩 함으로써 두 개 이상의 입력 변수를 가지는 `함수`를 단일 입력 변수를 가지는 일련의 함수들로 다시 쓸 수 있다. 이것을 `currying`이라고 한다. 아래 코드를 보자.  
```go
package main

import (
	"fmt"
)

// 정수 a, b, c를 입력 받아 b와 c를 곱한 값에 a를 더한 결과를 리턴.
func calculate(a, b, c int) int {
	return a + b * c
}

// 정수 a를 입력 받아 b와 더한 결과를 리턴하는 함수를 리턴.
func add(b int) func(int) int {
	return func(a int) int {
		return a + b;
	}
}

// 정수 b를 입력 받아 a와 곱한 결과를 리턴하는 함수를 리턴.
func multiply(c int) func(int) int {
	return func(b int) int {
		return b * c
	}
}

func main() {
	generalCalc := calculate(1, 2, 3)
	
	multiplier  := multiply(3)        
	adder       := add(multiplier(2)) 
	curriedCalc := adder(1)
	
	fmt.Printf("general = %d\ncurried = %d\n", generalCalc, curriedCalc)
}

// general = 7
// curried = 7
```
정수 a, b, c를 입력 받아 b와 c를 곱한 값에 a를 더한 결과를 리턴하는 함수 `calculate()`가 있다. 이 함수를 `커링`해보자. 즉, 단일 입력 변수를 가지는 함수의 연속으로 표현을 바꿔보자는 얘기이다. 우선, `multiply(3)`을 호출해 `정수 하나를 입력 받아 3을 더한 값을 리턴하는 함수인 multiplier()`를 생성했다. 그리고 `add()`의 인수로 `multiplier(2)`를 넘기고 `adder()`를 생성했는데, 생성 된 `adder()`는 `정수 하나를 입력 받아 multiplier(2)의 결과 값을 더한 값을 리턴하는 함수`이다. 최종적으로 `add(1)`을 호출하면 `calculate()`의 동작과 같은 값을 출력하는 함수가 된다. 즉, 세 개의 매개변수를 가지는 `calculate()`를 하나의 매개변수 만을 가지는 `multiply()`, `add()`의 연속으로 표현할 수 있음을 알 수 있다.  
`Go`의 경우와 같이, `익명 함수`를 지원하는 언어에서는 대부분 `익명 함수`를 통해 `람대 대수`의 개념에 따라 `함수`를 모델링 한 `람다 함수`를 표현할 수 있기 때문에 `익명 함수`와 `람다 함수`를 거의 동일한 개념으로 이해 하는 것 같다.  

### Lambda expression
`람다 표현식`은 어떠한 코드 블록을 하나의 식(expression)으로 표현 하는 것을 말한다. 보통 각 언어에서의 `함수` 또는 `메서드`가 `코드 블록`에 해당 하므로, 이들을 특정 표현 규칙에 맞추어 기술하고 이 표현을 통해 `함수`를 나타낼 수 있다.

`C`의 경우 `일급 함수`를 지원 하지 않으므로 애당초 `람다 함수`라는 개념에서 벗어나 있다.  

`Go`는 `func` 리터럴을 통해 `익명 함수`를 표현할수는 있지만 `람다 표현식`을 지원하지는 않는다. `Go`를 `functional programming language(함수형 프로그래밍 언어)`라고 구분하지 않는 이유인데, 이는 `Go`로 `함수형 프로그래밍` 이라는 패러다임을 구현할 수 있는가 와는 다른 문제이다. (위에서 살펴 보았듯 `Go`로도 `람다 함수`를 표현 할 수 있다) `람다 표현식`은 `람다 함수`를 표현하기 위한 방법 중 하나일 뿐이다.  

`Java`는 `JDK1.8`에서 정식으로 `lambda expression`을 `지원`하고 있다. 기존 `자바` 문법에서는 모든 것이 `객체`로 표현 되어야 하고, `함수` 형태의 코드 블록을 다루기 위해서는 `클래스`를 생성 하고 해당 `클래스`에 포함 되는 `메서드` 형태가 필수라는 제약이 있었다. 즉 `함수`를 나타내는 리터럴이 존재하지도 않고, 언어의 구현 스펙을 변경하지 않는 이상 `Java`에서는 `익명 함수` 자체가 구현 불가능한 내용 이었지만, `람다 표현식`이 언어에 정식으로 추가 됨으로 인해서 `함수` 형태로 다룰 수 있게 되었다. 이를 위해 새로운 문법 혹은 타입을 추가하지 않고 하나의 `추상 메서드`를 가지는 `인터페이스`를 `람다 표현식`을 이용해 `람다 함수`와 매핑하는 구조를 마련하였다. 하나의 `추상 메서드`만을 가지는 `인터페이스`를 `함수형 인터페이스(functional interface)`라고 정의하고, `람다 표현식`을 이용해 이 `인터페이스`의 익명 구현체를 생성하여 전달 함으로써 `익명 함수`를 다룰 수 있게 한 것이다.  
편하게 이해 해보자면, 어떠한 `함수`자체를 전달 할 방법이 없으니 그 `함수`의 구현부를 나타낼 수 있는 표현 방법을 마련해서 그 표현 방법 대로 `함수`의 동작을 나타내는 경우 이것을 하나의 메서드를 가지는 익명 클래스로 구현하여 대신 전달해준다고 볼 수 있을 것 같다.

`클로저` 설명 시 사용 했던 `Java` 예제 코드를 `람다 표현식`을 사용하도록 변경 해보면 아래와 같다.
```java
public class Program {
    @FunctionalInterface
    private interface Addable {
        public abstract int add(int number);
    }
    
    private static Addable getAdderWithBase(int base) {
        Addable adder = (int number) -> base + number;
        
        return adder;
    } 
    
    public static void main(String[] args) {
    	Addable adder = getAdderWithBase(5);
    	
	    System.out.println("Adder with base 5 : " + adder.add(5));
    }
}

// Adder with base 5 :  10
```
`Addable` interface에 `@FuntionalInterface`라는 `annotaion`이 붙어 있을 경우, 해당 인터페이스가 `람다 표현식`으로 매칭 가능한 인터페이스 인지, 즉 단일 추상 메서드만을 가진 인터페이스인지(물론 `디폴트 메서드`나 `정적 메서드`는 상관 없음)를 컴파일러가 체크해준다. `Addable` 인터페이스 변수 `adder`에 `람다 표현식`을 이용해 `int add(int)` 메서드를 오버라이딩한 구현체를 `대입`하였다. 실제 생성 된 것은 `Addable` 인터페이스의 구현체이지만 마치 하나의 표현식으로 정의한 `함수`를 변수에 대입하는 것과 같이 표현 가능하다. 즉, 변수에 `함수`를 담고 있는 것이며, 물론 `함수`의 반환 값으로 `함수`를 반환할수도 있게 되었고, 이를 통해 깔끔하게 `Java`에서의 `람다 함수`를 구현할 수 있게 되었다.

`Oracle`에서 제공하는 `Java` 스펙에서 지원하는 [`람다 표현식`의 예](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.27)는 아래와 같다.
```java
() -> {}                // No parameters; result is void
() -> 42                // No parameters, expression body
() -> null              // No parameters, expression body
() -> { return 42; }    // No parameters, block body with return
() -> { System.gc(); }  // No parameters, void block body

() -> {                 // Complex block body with returns
  if (true) return 12;
  else {
    int result = 15;
    for (int i = 1; i < 10; i++)
      result *= i;
    return result;
  }
}                          

(int x) -> x+1              // Single declared-type parameter
(int x) -> { return x+1; }  // Single declared-type parameter
(x) -> x+1                  // Single inferred-type parameter
x -> x+1                    // Parentheses optional for
                            // single inferred-type parameter

(String s) -> s.length()      // Single declared-type parameter
(Thread t) -> { t.start(); }  // Single declared-type parameter
s -> s.length()               // Single inferred-type parameter
t -> { t.start(); }           // Single inferred-type parameter

(int x, int y) -> x+y  // Multiple declared-type parameters
(x, y) -> x+y          // Multiple inferred-type parameters
(x, int y) -> x+y    // Illegal: can't mix inferred and declared types
(x, final y) -> x+y  // Illegal: no modifiers with inferred types
```
