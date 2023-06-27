---
layout: post
title: Callback에 대해 알아보자
date: 2023-06-27
categories: [Java]
tags: [Java, Callback]
---

## Callback 이란?

![Synchronous, Asynchronous](/assets/img/2023-06-27-Java%20Callback/2023-06-27-15-04-29.png)

Callback은 프로그래밍에서 흔히 볼 수 있는 패턴 중 하나로, 어떤 함수나 메소드가 다른 코드의 끝나는 시점에 호출되도록 프로그래밍 하는 방식을 나타냅니다. 주로 비동기 프로그래밍에서 사용되는데, 어떤 연산이 끝나는 시점을 예측하기 어려울 때 사용됩니다. 결과를 기다리는 동안 다른 작업을 수행하고, 결과가 준비되면 callback 함수를 호출함으로써 결과를 처리합니다.

즉, Callback 패턴은 보통 한 함수가 다른 함수를 인자로 받아서 특정 시점에서 호출하는 것을 말하며, Callback 함수는 "나중에 호출해달라"고 하는 함수를 의미합니다.

Callback 패턴은 주로 이벤트 처리, 비동기 처리, 타이머 작업 등에서 사용됩니다.

## Callback 예시 코드

- 자바는 `Runnable`, `Future`, `CompletableFuture를` 사용하여 비동기 처리를 간단하게 처리할 수 있습니다.
  - Javascript에서는 `Promise`, `async`/`await` 사용

```java
public class Main {
    public static void execute(Runnable callback) {
        System.out.println("Performing a task...");
        callback.run();
    }

    public static void main(String[] args) {
        execute(() -> System.out.println("Task finished, this is the callback!"));
    }
}
```

## Callback 지옥

Callback의 비교적 간단한 코드로 주된 장점은 비동기 처리를 가능하게 합니다. 즉, 시간이 많이 소요되는 작업이 있을 때, 이 작업이 완료될 때까지 프로그램이 멈춰있지 않고 다른 작업을 계속 수행할 수 있도록 구현할 때 자주 사용됩니다.

하지만 Callback에도 단점이 있습니다. 가장 큰 단점은 과도한 사용으로 인한 **Callback 지옥**이라는 상황이 발생할 수 있다는 것입니다.

Callback 지옥은 중첩된 Callback 함수들로 코드의 복잡성이 급격히 증가하는 상황을 가리킵니다. Callback 함수 안에 다른 Callback 함수가 있고, 그 안에 또 다른 Callback 함수가 있는 등의 경우, 코드가 매우 복잡해지고 가독성이 떨어집니다. 즉, 콜백 함수를 너무 많이 중첩해서 사용하면 코드의 가독성이 떨어지고, 에러 처리가 복잡해지는 문제가 발생합니다.

자바에서는 비동기 처리를 위해 보통 `Future`, `CompletableFuture` 등을 사용하기에, 콜백 패턴을 직접적으로 구현하는 경우는 비교적 드물지만, Javascript에서는 콜백을 자주 사용하기에 종종 콜백지옥을 마딱뜨리게 됩니다.

### Callback 지옥 예시 코드

```java
public class Main {
    public static void execute(Runnable callback) {
        System.out.println("작업을 시작합니다");
        callback.run();
        System.out.println("작업을 종료합니다");
    }

    public static void main(String[] args) {
        execute(() -> {
            System.out.println("첫 번째 콜백함수를 실행합니다");
            execute(() -> {
                System.out.println("두 번째 콜백함수를 실행합니다");
                execute(() -> {
                    System.out.println("세 번째 콜백함수를 실행합니다");
                    execute(() -> {
                        System.out.println("네 번째 콜백함수를 실행합니다");
                    });
                });
            });
        });
    }
}

// [결과]
// 작업을 시작합니다
// 첫 번째 콜백함수를 실행합니다
// 작업을 시작합니다
// 두 번째 콜백함수를 실행합니다
// 작업을 시작합니다
// 세 번째 콜백함수를 실행합니다
// 작업을 시작합니다
// 네 번째 콜백함수를 실행합니다
// 작업을 종료합니다
// 작업을 종료합니다
// 작업을 종료합니다
// 작업을 종료합니다
```

## Callback 활용

Callback 함수는 프로그래밍에서 여러가지 상황에서 유용하게 사용될 수 있습니다. 다음은 Callback을 사용할 수 있는 몇 가지 일반적인 시나리오입니다

1. **비동기 작업**: Callback 함수는 비동기 프로그래밍에서 가장 널리 사용됩니다. 파일을 읽거나, 네트워크를 통해 데이터를 가져오는 작업, DB 조회 등과 같이 시간이 오래 걸리는 작업이 있을 경우, 이 작업이 완료되는 동안 다른 코드를 실행할 수 있도록 해주는 것이 Callback의 주요 장점입니다. 이렇게 하면 프로그램이 멈추는 대신에, 비동기적으로 여러 작업을 동시에 처리할 수 있습니다.
2. **사용자 이벤트 처리**: 사용자의 행동에 대응하는 로직을 작성할 때, Callback 함수를 사용할 수 있습니다. 예를 들어, 버튼 클릭, 키보드 입력, 마우스 움직임 등의 이벤트가 발생했을 때 어떤 동작을 수행할지 정의하는 코드를 Callback 함수로 작성할 수 있습니다.
3. **코드의 재사용성과 구조화**: Callback 함수는 특정 기능을 추상화하고 코드를 재사용하는 데 도움이 됩니다. 예를 들어, 어떤 작업을 수행하는 함수가 있을 때, 그 작업이 끝난 후에 어떤 작업을 수행할지를 Callback 함수로 전달하면, 작업 후의 동작을 유연하게 바꿀 수 있게 됩니다. 이는 함수나 메소드의 기능을 확장하거나 재사용하는 데 도움이 됩니다.
4. **커스텀 정렬 또는 검색 알고리즘**: Array나 List와 같은 데이터 구조에 대해 정렬이나 검색을 수행할 때, 특정 기준에 따라 정렬하거나 검색하고 싶을 때 Callback 함수를 사용할 수 있습니다. 이렇게 하면 코드를 보다 유연하게 작성할 수 있습니다.

이렇게 보면, Callback은 프로그래밍에서 다양한 상황에서 유용하게 사용할 수 있음을 알 수 있습니다. 하지만 Callback 지옥 등의 문제로 인해 코드의 복잡성이 높아질 수 있으므로 적절하게 사용하는 것이 중요합니다.

## Callback 사용 시 주의사항

1. **과도한 중첩**: 앞서 설명한 'Callback 지옥'에 대한 문제를 초래합니다. Callback 함수를 계속 중첩해서 사용하면 코드의 가독성이 떨어지고, 로직을 이해하고 디버그하는 것이 어려워집니다. 이런 경우, `Future`, `Promise`, `Async/Await`, `Observable` 같은 더 현대적인 비동기 처리 패턴을 사용하는 것이 좋습니다.
2. **예외 처리**: Callback 패턴에서는 예외 처리가 어렵습니다. Callback 함수 안에서 발생하는 에러는 일반적으로 바깥쪽 코드로 전파되지 않기 때문에, 각 Callback 함수 내부에서 에러를 적절히 처리해야 합니다. 이것은 코드의 복잡성을 증가시키고, 에러 처리가 누락될 수 있습니다.
3. **제어 흐름**: Callback 함수를 사용하면 프로그램의 제어 흐름을 추적하기 어려울 수 있습니다. 비동기 작업이 완료될 때마다 Callback 함수가 호출되므로, 코드의 실행 순서를 예측하거나 이해하는 것이 어려울 수 있습니다.
4. **코드 테스팅과 유지 보수**: Callback 함수를 과도하게 사용하면 코드의 테스팅과 유지 보수가 어려워질 수 있습니다. 특히, 각각의 Callback 함수가 다른 Callback 함수에 의존하는 경우, 코드의 동작을 이해하고 테스트하는 데 시간이 많이 걸릴 수 있습니다.

따라서, Callback 함수의 이러한 단점을 고려하여 적절한 상황에서만 사용하는 것이 중요합니다. 또한, 이러한 문제를 완화하는 데 도움이 되는 다양한 도구와 패턴(예:  `Future`, `Promise`, `Async/Await`, `Observable` 등)에 대해 알아보는 것이 좋습니다.
