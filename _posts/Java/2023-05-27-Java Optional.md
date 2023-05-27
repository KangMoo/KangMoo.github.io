---
layout: post
title: Java Optional을 이해하고 활용하자
date: 2023-05-27
categories: [Java]
tags: [Java]
---

# Java Optional을 이해하고 활용하자

개발을 하다보면 가장 많이 발생하는 예외 중 하나가 바로 NPE(Null Pointer Exception)입니다. 오늘은 자바에서 NPE를 회피하는 효과적인 방법을 제공하는 Optional 클래스에 대해 알아보돌 하겠습니다.

## Optional이란?

- Optional은 Java 8에서 도입된 클래스로, Null Pointer Exception을 방지하고, Null을 보다 명확하게 처리하도록 돕는 기능을 제공합니다.
- Optional은 java.util 패키지에 포함되어 있는 클래스입니다. 이 클래스의 주 목적은 Null이 될 수 있는 객체를 감싸는 데에 있습니다.
- 값이 Null일 가능성이 있는 객체를 직접 다루는 대신, Optional 객체를 통해 간접적으로 다루게 합니다. 이렇게 함으로써 Optional은 개발자가 의도치 않은 Null Pointer Exception에 대한 방어적인 코딩을 할 수 있게 돕습니다.

## Optional의 장점

### 1. 명확한 의도의 표현

- Optional은 메서드가 결과를 반환하지 않을 수 있다는 것을 명확하게 표현합니다. 즉, Optional을 사용하면 Null을 반환하는 것이 아니라, 비어있는 Optional을 반환하는 것이 가능해집니다.

### 2. Null Pointer Exception 방지

- Optional은 Null 값을 직접 다루는 것이 아니라 감싸서 다루므로, NullPointerException을 방지할 수 있습니다.

## Optional 사용 방법

### Optional 생성하기

Optional 객체를 생성하는 방법은 간단합니다.

```java
Optional<String> optional = Optional.of("Hello World");
```

위 코드는 "Hello World"라는 문자열을 가진 Optional 객체를 생성합니다.

그런데 만약 Null 값을 가진 Optional 객체를 생성하려면 어떻게 해야 할까요? 그 경우에는V `Optional.empty()` 메서드를 사용합니다.

```java
Optional<String> optional = Optional.empty();
```

이는 빈 Optional 객체를 생성하는 코드입니다.

또한 Null이 될 수 있는 값을 Optional 객체로 감싸려면 `Optional.ofNullable()` 메서드를 사용합니다.

```java
String str = null;
Optional<String> optional = Optional.ofNullable(str);
```

위 코드는 Null 값을 가진 Optional 객체를 생성합니다.

### Optional 값 접근하기

Optional 객체의 값을 얻으려면 `get()` 메서드를 사용합니다.

```java
Optional<String> optional = Optional.of("Hello World");
String value = optional.get(); // "Hello World"
```

하지만 만약 Optional 객체가 비어 있으면 `get()` 메서드는 NoSuchElementException을 던집니다. 따라서 `get()` 메서드를 호출하기 전에 `isPresent()` 메서드를 사용하여 Optional 객체가 값이 있는지 확인해야 합니다.

```java
Optional<String> optional = Optional.of("Hello World");
if (optional.isPresent()) {
    String value = optional.get(); // "Hello World"
}
```

그런데 이 방법은 여전히 Null 확인 로직을 직접 다루게 합니다. 그 대신 Optional에서 제공하는 `orElse()` 메서드를 사용하여 Optional 객체가 비어 있을 때의 기본 값을 설정할 수 있습니다.

```java
Optional<String> optional = Optional.empty();
String value = optional.orElse("Default Value"); // "Default Value"
```

## Optional의 더 많은 기능들

### ifPresent()

`ifPresent()` 메서드는 값이 존재하는 경우에만 주어진 작업을 실행하는 메서드입니다. 이 메서드는 매개변수로 Consumer 함수형 인터페이스를 받습니다.

```java
Optional<String> optional = Optional.of("Hello World");
optional.ifPresent(value -> System.out.println(value)); // "Hello World"
```

### orElseGet()

`orElseGet()` 메서드는 Optional 객체가 비어있을 때 실행할 Supplier 함수형 인터페이스를 인자로 받습니다. 이 메서드는 `orElse()` 메서드와 비슷하지만, 기본값을 동적으로 생성할 수 있다는 점에서 차이가 있습니다.

```java
Optional<String> optional = Optional.empty();
String value = optional.orElseGet(() -> "Default Value"); // "Default Value"
```

> <details>
> <summary>`orElse()`와 `orElseGet()`의 차이</summary>
> <div markdown="1">
> 
> `orElse()`와 `orElseGet()`은 모두 Java의 Optional 클래스에 포함된 메서드로, Optional 객체가 비어있는 경우에 대한 대체 값을 제공합니다. 하지만 두 메서드는 사용 방식에 약간의 차이가 있습니다.
>
> ---
> 
> **orElse()**
> 
> `orElse()` 메서드는 인수로 받은 값을 반환합니다. 이 인수는 Optional 객체가 비어있는지 여부와 상관없이 항상 계산됩니다. 따라서 이 인수는 CPU 또는 메모리 자원을 많이 사용하는 "무거운" 연산이 아닐 때 사용하는 것이 좋습니다.
> 
> ```java
> Optional<String> optional = Optional.empty();
> String value = optional.orElse(expensiveOperation());
> ```
> 
> 위의 코드에서 `expensiveOperation()`은 Optional 객체가 비어있는지 여부에 관계없이 항상 실행됩니다.
>
> ---
> 
> **orElseGet()**
> 
> 반면에, `orElseGet()` 메서드는 인수로 받은 Supplier를 호출하여 그 결과를 반환합니다. 이 Supplier는 Optional 객체가 비어 있을 때만 실행되므로, CPU 또는 메모리 자원을 많이 사용하는 "무거운" 연산을 실행할 때 사용하는 것이 좋습니다.
> 
> ```java
> Optional<String> optional = Optional.empty();
> String value = optional.orElseGet(() -> expensiveOperation());
> ```
> 
> 위의 코드에서 `expensiveOperation()`은 Optional 객체가 비어 있을 때만 실행됩니다.
> 
> 따라서 `orElse()`와 `orElseGet()`의 주된 차이점은 비어있는 Optional 객체에 대한 대체 값을 언제 계산하느냐에 있습니다. `orElse()`는 항상 계산하고, `orElseGet()`은 필요할 때만 계산합니다.
> </div>
> </details>

### orElseThrow()

`orElseThrow()` 메서드는 Optional 객체가 비어있을 때 예외를 발생시키는 메서드입니다. 이 메서드는 `orElseGet()` 메서드와 비슷하지만, 비어있는 경우 Supplier 함수형 인터페이스에 의해 제공되는 예외를 던집니다.

```java
Optional<String> optional = Optional.empty();
String value = optional.orElseThrow(() -> new IllegalArgumentException("Optional is empty"));
```

### filter(), map(), flatMap()

Optional 클래스는 `filter()`, `map()`, `flatMap()` 등의 메서드를 제공하여 Optional 객체를 더 유연하게 다룰 수 있습니다. 이들 메서드는 Stream 인터페이스의 동일한 메서드와 비슷한 동작을 합니다.

`filter()` 메서드는 Predicate 함수형 인터페이스를 인자로 받아 Optional 객체의 값을 테스트하고, 테스트를 통과하는 값만을 포함하는 새로운 Optional 객체를 반환합니다.

```java
Optional<String> optional = Optional.of("Hello World");
Optional<String> filtered = optional.filter(value -> value.length() > 10); // Optional["Hello World"]
```

`map()` 메서드는 Function 함수형 인터페이스를 인자로 받아 Optional 객체의 값을 변환하고, 그 결과를 포함하는 새로운 Optional 객체를 반환합니다.

```java
Optional<String> optional = Optional.of("Hello World");
Optional<Integer> mapped = optional.map(String::length); // Optional[11]
```

`flatMap()` 메서드는 Function 함수형 인터페이스를 인자로 받아 Optional 객체의 값을 변환하고, 그 결과를 포함하는 새로운 Optional 객체를 반환합니다. 이 메서드는 `map()` 메서드와 비슷하지만, 변환 함수의 결과가 Optional인 경우에 유용합니다.

```java
Optional<String> optional = Optional.of("Hello World");
Optional<Optional<String>> mapped = optional.map(value -> Optional.of(value.toLowerCase())); // Optional[Optional["hello world"]]
Optional<String> flatMapped = optional.flatMap(value -> Optional.of(value.toLowerCase())); // Optional["hello world"]
```
