---
layout: post
title: 자바의 CompletableFuture 란?
date: 2023-12-21
categories: [Java]
tags: [Java, CompletableFuture, Future, 비동기 프로그래밍]
---

## 들어가면서

Java 5에서 도입된 `Future` 인터페이스는 비동기 계산의 결과를 수월하게 처리할 수 있게 되었지만, 몇가지 한계점이 있었다
- `Future`의 `get()` 메서드를 호출할 때, 작업이 완료될때까지 블록킹된다
- 여러 `Future`를 조합하거나 예외를 처리하는것이 복잡하다
- 비동기 작업의 실패를 알리는 콜백 메커니즘이 부족하다

이러한 `Future`의 한계점을 극복하고자 나온 것이 `CompletableFuture` 이다

## CompletableFuture 란?

- Java 8에 도입된 표준 라이브러리의 클래스
- 비동기 프로그래밍을 위한 강력한 도구
- 기존 `Future` 보다 더 편리하고 효율적인 방식으로 비동기 계산을 처리하는 방법을 제공하는 
- 동시 및 확장 가능한 코드를 작성하기 위한 강력한 도구이며 스트림 및 람다와 같은 다른 Java 8 기능과 함께 사용되는 경우가 많다

## CompletableFuture의 특징

`CompletableFuture`는 `Future`보다 더 유연하고 강력한 비동기 프로그래밍을 지원한다

- 조합 및 체이닝 : 여러 개의 `CompletableFuture`를 조합하거나 체이닝하여 복잡한 비동기 작업을 쉽게 처리할 수 있다
- 예외 처리 : 작업 중 발생한 예외를 처리하거나 기본값을 제공하는 등의 유연한 예외 처리가 가능하다
- 콜백 등록 : 콜백을 등록하여 작업이 완료되었을 때 특정 동작을 수행할 수 있으며 이를 통해 비동기 작업의 완료를 감지하고 그에 따른 동작을 정의할 수 있다
- Custom Executor 지원 : Custom Executor를 사용하여 성능을 향상시킬 수 있으며 특정 상황에 맞는 실행자를 제공하여 작업을 처리할 수 있다
- CompletionStage 구현 : `CompletionStage`를 구현하여 비동기 작업의 완료 여부를 기다리거나, 작업의 결과에 따라 다양한 동작을 수행할 수 있다

## CompletableFuture의 주요 메서드

### 생성 및 실행

- `runAsync(Runnable runnable)` / `runAsync(Runnable runnable, Executor executor)`
  - 비동기적으로 `Runnable` 작업을 실행한다
  - `runAsync`는 반환값이 없는 작업에 사용된다
  - 선택적으로 `Executor`를 지정하여 실행할 스레드를 제어할 수 있다

- `supplyAsync(Supplier<U> supplier)` / `supplyAsync(Supplier<U> supplier, Executor executor)`
  - 결과를 반환하는 비동기 작업을 실행한다
  - `Supplier` 인터페이스를 통해 값을 반환한다
  - `Executor`를 사용하여 작업이 실행될 스레드를 지정할 수 있다

### 결과 처리

- `thenApply(Function<? super T,? extends U> fn)` / `thenApplyAsync(Function<? super T,? extends U> fn)`
  - 작업이 완료된 후, 결과를 다른 값으로 반환한다
  - `Function` 인터페이스를 사용하여 현재 값에서 새로운 값을 도출한다
  - `thenApplyAsync`는 비동기적으로 실행된다

- `thenAccept(Consumer<? super T> action)` / `thenAcceptAsync(Consumer<? super T> action)`
  - 반환값이 없는 동작을 결과에 대해 실행한다
  - `Consumer` 인터페이스를 통해 받은 값을 처리한다
  - `thenAcceptAsync`는 비동기적으로 실행된다

- `thenRun(Runnable action)` / `thenRunAsync(Runnable action)`
  - 작업의 결과에 관계없이, 특정 동작을 실행한다
  - `Runnable`을 사용하여 코드 블록을 실행한다
  - `thenRunAsync`는 비동기적으로 실행된다

### 오류 처리

- `exceptionally(Function<Throwable, ? extends T> fn)`
  - 작업 실행 중 발생한 예외를 처리한다
  - `Function` 인터페이스를 사용하여 예외에 대한 회복 로직을 제공한다

### 조합

- `thenCombine(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)` / `thenCombineAsync(...)`
  - 두 개의 `CompletableFuture`를 조합하고, 두 결과에 대한 작업을 실행한다
  - `BiFunction`을 통해 두 결과를 받아 새로운 결과를 생성한다

- `thenCompose(Function<? super T, ? extends CompletionStage<U>> fn)` / `thenComposeAsync(...)`
  - 현재 작업의 결과를 사용하여 새 `CompletableFuture`를 생성한다
  - 첫 번째 작업의 결과를 두 번째 작업의 입력으로 사용한다

### 블록 및 넌블록 방식의 결과 가져오기

- `get()` / `get(long timeout, TimeUnit unit)`
  - 작업의 결과를 가져온다
  - `get()`은 결과가 준비될 때까지 블록한다
  - 타임아웃을 지정할 수 있다

- `join()`
  - `get()`과 유사하지만, 예외를 `RuntimeException`으로 감싸 반환한다

- `getNow(T valueIfAbsent)`
  - 작업이 완료되지 않았을 때 기본값을 반환한다

### 작업 완료

- `complete(T value)`
  - 수동으로 `CompletableFuture`를 특정 값으로 완료한다
  - 이미 완료된 경우 아무 작업도 수행하지 않는다

- `completeExceptionally(Throwable ex)`
  - `CompletableFuture`를 예외로 완료한다
  - 작업이 예외로 인해 실패한 것으로 처리된다

## CompletableFuture의 주요 메서드 예시 코드

### 생성 및 실행

- `runAsync(Runnable runnable)` 예시

```java
CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
    System.out.println("Asynchronous task running");
});
```

> 비동기적으로 실행될 작업을 정의하고 실행한다

- `supplyAsync(Supplier<U> supplier)` 예시

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "Result of the asynchronous computation";
});
```

> 비동기 작업을 수행하고 결과를 반환한다

### 결과 처리

- `thenApply(Function<? super T,? extends U> fn)` 예시

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> 123)
    .thenApply(result -> "Value: " + result);
```

> 비동기 작업의 결과에 함수를 적용한다

- `thenAccept(Consumer<? super T> action)` 예시

```java
CompletableFuture<Void> future = CompletableFuture
    .supplyAsync(() -> "Hello")
    .thenAccept(result -> System.out.println("Result: " + result));
```

> 비동기 작업의 결과를 소비한다

- `thenRun(Runnable action)` 예시

```java
CompletableFuture<Void> future = CompletableFuture
    .supplyAsync(() -> "Hello")
    .thenRun(() -> System.out.println("All tasks completed"));
```

> 비동기 작업이 완료된 후 추가 작업을 실행한다

### 오류 처리

- `exceptionally(Function<Throwable, ? extends T> fn)` 예시

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> {
        if (Math.random() > 0.5) {
            throw new RuntimeException("Exception occurred");
        }
        return "Success";
    })
    .exceptionally(ex -> "Error: " + ex.getMessage());
```

> 비동기 작업 중 오류가 발생했을 때 대체 결과를 제공한다

### 조합

- `thenCombine(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)` 예시

```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");
CompletableFuture<String> result = future1.thenCombine(future2, (s1, s2) -> s1 + " " + s2);
```

> 두 개의 비동기 작업 결과를 결합한다

- `thenCompose(Function<? super T, ? extends CompletionStage<U>> fn)` 예시

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> "Hello")
    .thenCompose(s -> CompletableFuture.supplyAsync(() -> s + " World"));
```

> 첫 번째 작업의 결과를 사용하여 두 번째 비동기 작업을 시작한다

### 블록 및 넌블록 방식의 결과 가져오기

- `get()` 예시

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Hello");
try {
    String result = future.get();
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
```

> 작업의 완료를 기다린 후 결과를 반환한다

- `join()` 예시

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Hello");
String result = future.join();
```

> `get()`과 유사하지만, 체크되지 않은 예외를 던진다

- `getNow(T valueIfAbsent)` 예시

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Hello");
String result = future.getNow("Default Value");
```

> 결과가 준비되지 않았을 때 기본값을 반환한다

### 작업 완료

- `complete(T value)` 예시

```java
CompletableFuture<String> future = new CompletableFuture<>();
future.complete("Manual result");
```

> CompletableFuture에 수동으로 결과를 설정한다

- `completeExceptionally(Throwable ex)` 예시

```java
CompletableFuture<String> future = new CompletableFuture<>();
future.completeExceptionally(new RuntimeException("Failure"));
```

> CompletableFuture에 예외를 수동으로 설정한다
