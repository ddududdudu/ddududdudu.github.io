### Concurrent programming in Java
실행 중인 프로그램에 동시성을 부여하기 위해 자바 프로세스에서는 하나 이상의 `thread`를 실행하는 `multi-threaded process` 모델을 사용한다. 
동시에 실행 가능한 `thread`의 수에는 제한이 없지만, `JVM 메모리 구조`상 하나의 `thread` 별로 1MiB의 개별 스택 및 각 스택을 구분하기 위한 `guard space`를 필요로 한다.
보통 아래와 같이 `Runnable interface`를 구현한 익명 객체를 생성하여 새로운 `thread`를 실행 하게 되는데,
```java
class ThreadTest {
    public static void main(String[ ] args) {
        Thread testThread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("In sub thread!");   
            }
        }, "sub");
        
        testThread.start();
        
        try {
            Thread.sleep(1000);
        } catch(Exception e) {}
        System.out.println("In main thread!");
    }
}

// In sub thread!
// In main thread!
```
override한 `run()`을 바로 호출 하는 것이 아니라(호출 해도 되지만 멀티 쓰레드로 동작하지 않는다) `start()`를 호출 해야 하는 이유도 이 메서드가 새로운 `thread`를 
생성하고 콜 스택을 설정 하는 등의 사전 작업을 진행 해주기 때문이다.  

### 
