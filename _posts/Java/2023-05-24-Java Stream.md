---
layout: post
title: Java Stream API를 이해하고 활용하자
date: 2023-05-24
categories: [Java]
tags: [Java]
---
# Java의 Stream이란?

- Stream API는 Java 8에 소개된 새로운 추상화
- 자바 8에는 "스트림"이라는 새로운 데이터 유형을 도입했는데, 이는 데이터 요소의 시퀀스를 나타낸다.
  - 이를 활용하면 배열이나 컬렉션 등의 데이터를 효과적으로 처리할 수 있다.
- 스트림은 개발자가 "어떻게" 처리할 것인지보다 "무엇"을 처리할 것인지에 집중할 수 있도록 한다.
  - 이러한 접근 방식은 코드를 더욱 간결하고 이해하기 쉽게 만들어준다.
- 간단하게 요약하자면, 스트림은 컬렉션, 배열 등의 저장요소를 하나씩 참조하며 함수형 인터페이스(람다식)을 적용하여 반복적으로 처리할 수 있도록 해주는 기능이라고 할 수 있다.

## 백문이불여일견

아직까지는 무슨 말인지 이해하기 어려울 것이다. 한번 코드를 보면서 이해해보자

**컬렉션에서 짝수인 정수만 뽑아서 제곱**을 한 결과를 구하려고 한다고 했을 때, Stream을 사용하기 전/후의 코드를 비교하자면 다음과 같다.

### Stream 사용 전

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> evenSquared = new ArrayList<>();
for (Integer number : numbers) {
    if (number % 2 == 0) {
        int squared = number * number;
        evenSquared.add(squared);
    }
}

for (Integer squared : evenSquared) {
    System.out.println(squared);
}
```

### Stream 사용 후

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> evenSquared = numbers.stream()
        .filter(number -> number % 2 == 0)
        .map(number -> number * number)
        .collect(Collectors.toList());

evenSquared.forEach(System.out::println);
```

## Stream 사용법

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-28-56.png)

Stream은 일반적으로 "Stream 생성 -> 중간연산 -> 최종연산" 단계를 거치며 연산이 진행된다.

### Stream 생성

#### Array -> Stream

```java
String[] arr = new String[] {"Hello", "World", "Hell"};
Stream<String> stream = Arrays.stream(arr); // 배열
Stream<String> streamOfArrayPart = Arrays.stream(arr, 1, 3); // 부분 배열
```

#### Collection -> Stream

```java
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream = list.stream();
Stream<String> parallelStream = list.parallelStream(); // 병렬 처리 스트림
```

#### File -> Stream

- 파일의 경우 자바 NIO 의 Files클래스를 이용해 문자열 스트림 생성이 가능

```java
Path path = Paths.get("C:/Tmp/testfile.txt");
Stream<String> streamOfStrings = Files.lines(path);
Stream<String> streamWithCharset = Files.lines(path, Charset.forName("UTF-8"));
```

#### 빈 스트림 생성 (null 대신 사용 가능)

```java
Stream<Object> stream = Stream.empty();
Stream<String> streamEmpty = Stream.empty();
```

#### Build

```java
Stream<String> generatedStream = Stream.<String>builder()
        .add("Hello")
        .add("World")
        .build();
```

#### generate

- `generate()` 메소드는 크기를 지정하지 않으면 무한 스트림을 생성하므로, 특정 사이즈만큼 생성하려면 반드시 `limit()`을 통해 최대 크기를 제한해야 한다.

```java
Stream<String> generatedStream = Stream.generate(() -> "gen").limit(5);
```

#### iterate

- 초기 값을 시작으로 2씩 증가된 값을 생성한다. `iterate()` 메소드도 마찬가지로 크기를 지정하지 않으면 무한 스트림을 생성하므로 `limit()`을 통해 크기를 제한해야 한다.

```java
Stream<Integer> iteratedStream = Stream.iterate(30, n -> n + 2).limit(5);
```

#### 기본 타입형 스트림

- IntStream, LongStream, DoubleStream
- 기본 타입형 스트림은 제네릭을 사용하지 않고 기본 값을 생성하는 방법.
- range는 [startPosition, endPosition) 범위를 가지며, ragneClosed는 [startPosition, endPosition] 범위를 가진다.

```java
IntStream intStream = IntStream.range(1, 5); // [1, 2, 3, 4]
LongStream longStream = LongStream.rangeClosed(1, 5); // [1, 2, 3, 4, 5]
```

- 필요한 경우 Boxed메서드를 통해 Integer 형태로 박싱할 수 있다.

```java
Stream<Integer> boxedIntStream = IntStream.range(1, 5).boxed();
```

- 난수 스트림을 생성할 수도 있다.

```java
DoubleStream doubles = new Random().doubles(3); // 난수 3개 생성
```

#### 병렬스트림

- 병렬 스트림은 내부적으로 'fork & join'프레임워크를 이용해 자동적으로 연산을 병렬로 수행한다.
- 병렬 스트림을 중단하려면 `sequentail()`을 호출하면 된다.

```java
Stream<String> strStream = Arrays.asList("Hello", "World", "Java").stream();
int sum = strStream.parallel()
                   .mapToInt(s -> s.length())
                   .sum();
```

### Stream 중간 연산

- Java의 Stream API는 데이터 처리 연산을 지원하며, 이 중에서도 "중간 연산"은 Stream을 통해 데이터를 원하는 형태로 처리하는 데 사용되는 연산자이다.
- 각각의 중간 연산자들은 lazy하게 실행되며, 그 결과로 Stream을 반환하므로 메서드 체이닝 형태로 연결하여 처리할 수 있다. 또한 이러한 연산자들은 주로 함수형 인터페이스를 사용한다.

#### filter

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-29-11.png)

```java
Stream<T> filter(Predicate<? super T> predicate);
```

- `filter` 메서드는 원하는 요소만 추출하기 위한 연산자로, 요소를 필터링하거나 제거하는 데 사용된다
- `Predicate` 함수형 인터페이스를 사용한다

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
names.stream()
     .filter(name -> name.startsWith("S"))
     .forEach(System.out::println); // "Sarah", "Sandy" 만 출력된다.
```

#### map

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-29-55.png)

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

- `map` 메서드는 스트림 내 요소를 가공하는 연산자로, 스트림의 각 요소를 다른 형태의 요소로 변환하는 데 사용된다
- `Function` 함수형 인터페이스를 사용한다

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
names.stream()
     .map(String::toUpperCase)
     .forEach(System.out::println); // 모든 이름이 대문자로 출력된다.
```

#### flatMap

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-30-28.png)

```java
<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);
```

- `flatMap` 메서드는 중첩 구조를 한 단계 제거하고 단일 컬렉션으로 만들어주는 연산자이다
- `Function` 함수형 인터페이스를 사용한다
- `map`과의 가장 큰 차이는 함수의 반환 값이 `Stream`형태라는 점이다

```java
List<List<String>> namesNested = Arrays.asList(
  Arrays.asList("Jeff", "Bezos"),
  Arrays.asList("Bill", "Gates"),
  Arrays.asList("Mark", "Zuckerberg"));

namesNested.stream()
            .flatMap(Collection::stream)
            .forEach(System.out::println); // 모든 이름이 하나의 스트림으로 출력된다.
```

#### sorted

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-30-45.png)

```java
Stream<T> sorted();
Stream<T> sorted(Comparator<? super T> comparator);
```

- `sorted` 메서드는 스트림 요소를 정렬하는 연산자로, 스트림의 요소를 정렬하는 데 사용된다
- `Comparator` 함수형 인터페이스를 사용한다

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
names.stream()
     .sorted()
     .forEach(System.out::println); // 이름이 알파벳순으로 출력된다.
```

#### distinct

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-31-07.png)

```java
Stream<T> distinct();
```

- `distinct` 메서드는 중복된 값을 제거하는 연산자이다

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona", "John");
names.stream()
     .distinct()
     .forEach(System.out::println); // 중복된 "John"이 제거되고 출력된다.
```

#### peek

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-31-28.png)

```java
Stream<T> peek(Consumer<? super T> action);
```

- `peek` 메서드는 각 요소에 특정 연산을 수행하는 연산자로, 스트림의 각 요소를 소비하고 다른 연산에 영향을 주지 않는 데 사용된다
- 결과에 영향을 주지 않으며 이 특성 때문에 주로 디버깅 용도로 사용된다.

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
names.stream()
     .peek(System.out::println)
     .map(String::toUpperCase)
     .forEach(System.out::println); // 각 이름이 한 번 출력되고, 그 다음에 대문자로 변환된 이름이 출력된다.
```

#### limit

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-31-41.png)

```java
Stream<T> limit(long maxSize);
```

- `limit` 메서드는 스트림의 크기를 제한하는 데 사용된다
- 앞에서부터 n개의 요소만 취하는 연산자이다.

```java
Stream.iterate(1, n -> n + 1)
      .limit(5)
      .forEach(System.out::println); // 1부터 5까지의 숫자가 출력된다.
```

#### skip

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-31-50.png)

```java
Stream<T> skip(long n);
```

- `skip` 메서드는 스트림의 처음 n개 요소를 무시하는 데 사용된다
- 스트림에서 앞선 n개의 요소를 건너뛰고 그 이후의 요소들만 선택하는 연산자이다.

```java
Stream.iterate(1, n -> n + 1)
      .skip(5)
      .limit(5)
      .forEach(System.out::println); // 6부터 10까지의 숫자가 출력된다.
```

#### concat

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-31-58.png)

```java
static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b);
```

- `concat` 메서드는 두 Stream을 연결하는 연산자로, 두 스트림을 결합하는 데 사용된다

```java
Stream<String> stream1 = Stream.of("One", "Two", "Three");
Stream<String> stream2 = Stream.of("Four", "Five", "Six");
Stream<String> resultingStream = Stream.concat(stream1, stream2);
resultingStream.forEach(System.out::println); // "One", "Two", "Three", "Four", "Five", "Six" 순으로 출력된다.
```

### Stream 최종 연산

- Java의 Stream API에서는 중간 연산 이외에도 최종 연산이라는 개념이 있다.
- 최종 연산은 스트림 파이프라인을 처리하고, 처리 결과를 반환하거나 또는 특정 동작을 수행한다.
- 이 연산을 수행하면 스트림 파이프라인이 실행되며, 스트림이 소비되어 더 이상 사용할 수 없게 된다.

#### forEach

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-32-10.png)

```java
void forEach(Consumer<? super T> action);
```

- `forEach` 메서드는 `Consumer`를 인자로 받아 스트림의 각 요소에 대해 동작을 수행한다.

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
names.stream()
     .forEach(System.out::println); // "John", "Sarah", "Mark", "Tina", "Sandy", "Mona"를 각 줄에 하나씩 출력된다.
```

#### count, sum, min, max, average

```java
long count();

int sum();
long sum();
double sum();

Optional<T> min(Comparator<? super T> comparator);
Optional<T> max(Comparator<? super T> comparator);

OptionalDouble average();
```

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-32-32.png)
![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-32-48.png)
![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-32-54.png)
![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-32-59.png)
![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-33-03.png)

- 이 메서드들은 각각 스트림의 요소 수, 합, 최소값, 최대값, 평균을 계산한다.

```java
long count = IntStream.of(1, 2, 3, 4, 5).count();
long sum = IntStream.of(1, 2, 3, 4, 5).sum();
OptionalInt min = IntStream.of(1, 2, 3, 4, 5).min();
OptionalInt max = IntStream.of(1, 2, 3, 4, 5).max();
OptionalDouble average = IntStream.of(1, 2, 3, 4, 5).average();
```

#### reduce

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-33-29.png)

```java
Optional<T> reduce(BinaryOperator<T> accumulator);
T reduce(T identity, BinaryOperator<T> accumulator);
<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner);
```

`reduce`는 스트림의 요소를 축소하여 단일 결과를 생성한다. 여기에는 세 가지 형태의 `reduce` 메서드가 있다

- `Optional<T> reduce(BinaryOperator<T> accumulator)`: 스트림의 요소를 누적하는 함수를 사용하여 reduce를 수행한다. 초기값이 없어서 반환 값이 Optional이다.
- `T reduce(T identity, BinaryOperator<T> accumulator)`: 초기값을 받아 reduce를 수행한다. 초기값이 제공되므로, 결과는 Optional이 아니다.
- `<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner)`: 병렬 스트림 처리를 지원하는 reduce 메서드다. 병렬 처리의 경우, 누적 함수와 조합 함수를 제공해야 한다.

##### `Optional<T> reduce(BinaryOperator<T> accumulator)`

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> sum = numbers.stream()
    .reduce(Integer::sum);
sum.ifPresent(System.out::println); // 15가 출력된다.
```

##### `T reduce(T identity, BinaryOperator<T> accumulator)`

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream()
    .reduce(0, Integer::sum);
System.out.println(sum); // 15가 출력된다.
```

##### `<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner)`

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
String sum = numbers.parallelStream()
    .reduce("_", (partialString, number) -> partialString + number, String::concat);
System.out.println(sum); // "_1_2_3_4_5"가 출력된다.
```

#### collect

![image](/assets/img/2023-05-24-Java%20Stream/2024-09-10-16-33-40.png)

```java
<R> R collect(Supplier<R> supplier, BiConsumer<R, ? super T> accumulator, BiConsumer<R, R> combiner);
<R, A> R collect(Collector<? super T, A, R> collector);
```

- `collect`는 스트림의 결과를 컬렉션으로 모은다. 이는 다양한 형태로 사용될 수 있다
- `Collectors`클래스는 `collect`를 손쉽게 사용하도록 도와준다.
  - `Collectors.toList()`: 결과를 리스트로 반환한다.
  - `Collectors.toSet()`: 결과를 set으로 반환한다.
  - `Collectors.joining()`: 스트림의 결과를 문자열로 연결한다.
  - `Collectors.groupingBy()`: 특정 기준에 따라 스트림의 요소를 분할한다.
  - `Collectors.partitioningBy()`: 조건에 따라 스트림의 요소를 분할한다.
  - `Collectors.averagingInt()`, `Collectors.summingInt()`, `Collectors.summarizingInt()`: 평균, 합, 요약 통계 등의 연산을 수행한다.

##### `<R> R collect(Supplier<R> supplier, BiConsumer<R, ? super T> accumulator, BiConsumer<R, R> combiner)`

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
List<String> upperCaseNames = names.stream()
    .collect(ArrayList::new, (list, name) -> list.add(name.toUpperCase()), ArrayList::addAll);

upperCaseNames.forEach(System.out::println); // 모든 이름이 대문자로 출력된다.
```

##### `<R, A> R collect(Collector<? super T, A, R> collector)`

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
List<String> upperCaseNames = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
upperCaseNames.forEach(System.out::println); // 모든 이름이 대문자로 출력된다.
```

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona", "John");
Set<String> uniqueNames = names.stream()
    .collect(Collectors.toSet());
System.out.println(uniqueNames); // [John, Sarah, Mark, Tina, Sandy, Mona]가 출력된다.
```

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
String namesConcatenated = names.stream()
    .collect(Collectors.joining(", "));
System.out.println(namesConcatenated); // "John, Sarah, Mark, Tina, Sandy, Mona"가 출력된다.
```

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona", "John");
Map<Integer, List<String>> groupedNames = names.stream()
    .collect(Collectors.groupingBy(String::length));
System.out.println(groupedNames); // {4=[John, Mark, Mona, John], 5=[Sarah, Sandy], 4=[Tina]}이 출력된다.
```

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
Map<Boolean, List<Integer>> partitionedNumbers = numbers.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
System.out.println(partitionedNumbers); // {false=[1, 3, 5], true=[2, 4, 6]}이 출력된다.
```

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
double averageLength = names.stream()
    .collect(Collectors.averagingInt(String::length));
System.out.println(averageLength); // 이름들의 평균 길이가 출력된다.
```

#### anyMatch, allMatch, noneMatch

```java
boolean anyMatch(Predicate<? super T> predicate);
boolean allMatch(Predicate<? super T> predicate);
boolean noneMatch(Predicate<? super T> predicate);
```

- 이 메서드들은 스트림의 요소가 특정 조건을 만족하는지를 확인한다. `Predicate`를 인자로 받아 스트림의 요소에 대한 특정 조건을 검사한다.
- `anyMatch`는 하나라도 조건을 만족하면 true를 반환한다.
- `allMatch`는 모든 요소가 조건을 만족해야 true를 반환한다.
- `noneMatch`는 모든 요소가 조건을 만족하지 않을 때 true를 반환한다.

```java
boolean anyMatch = IntStream.of(1, 2, 3, 4, 5).anyMatch(n -> n > 3);
boolean allMatch = IntStream.of(1, 2, 3, 4, 5).allMatch(n -> n > 0);
boolean noneMatch = IntStream.of(1, 2, 3, 4, 5).noneMatch(n -> n > 5);
```

```java
List<String> names = Arrays.asList("John", "Sarah", "Mark", "Tina", "Sandy", "Mona");
boolean result = names.stream()
                      .anyMatch(name -> name.startsWith("S"));
System.out.println(result); // true가 출력된다. (Sarah, Sandy가 S로 시작하기 때문에)
```

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
boolean result = numbers.stream()
    .allMatch(n -> n > 0);
System.out.println(result); // true가 출력된다. (모든 숫자가 0보다 크기 때문에)
```

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
boolean result = numbers.stream()
    .noneMatch(n -> n > 10);
System.out.println(result); // true가 출력된다. (모든 숫자가 10보다 작기 때문에)
```

## 스트림 디버깅과 성능 최적화

스트림은 함수형 프로그래밍의 일부로 사용되며, 특히 복잡한 데이터 구조를 다루는데 매우 유용하다. 그러나 디버깅이 어려울 수 있고 성능 문제도 초래할 수 있다.

### 디버깅을 위한 스트림의 중간 결과 확인

- 스트림 연산은 내부적으로 복잡하기 때문에 문제를 디버깅하는 것은 어려울 수 있다. 따라서, 개발자들은 스트림의 중간 결과를 확인하는 것이 중요하다고 느낀다. Java 8의 스트림 API는 이를 위한 `peek` 메서드를 제공한다.
- `peek` 메서드는 중간 연산이며, 스트림의 요소를 소비하지 않는다. 주로 디버깅 목적으로 사용되며, 각 처리 단계에서 스트림의 내용을 출력할 수 있다.

```java
List<Integer> numbers = Arrays.asList(2, 3, 4, 5);
numbers.stream()
    .peek(x -> System.out.println("from original stream: " + x))
    .map(x -> x + 17)
    .peek(x -> System.out.println("after map: " + x))
    .filter(x -> x % 2 == 0)
    .peek(x -> System.out.println("after filter: " + x))
    .limit(3)
    .peek(x -> System.out.println("after limit: " + x))
    .collect(Collectors.toList());
```

### 스트림 성능 최적화 팁과 지침

스트림을 사용하면 코드가 간결해지고 가독성이 향상되지만, 잘못 사용하면 성능 문제를 초래할 수 있다. 다음은 스트림 성능을 최적화하는 몇 가지 팁이 있다.

- 불필요한 박싱을 피해라: 자동 박싱과 언박싱은 성능에 영향을 준다. 가능하면 기본형 스트림(`IntStream`, `LongStream`, `DoubleStream`)을 사용하십시오.
- 순차 스트림 vs 병렬 스트림: 병렬 스트림은 여러 코어에서 연산을 병렬로 수행하기 때문에 일부 상황에서는 성능 향상을 가져올 수 있다. 그러나 항상 빠른 것은 아니며, 데이터가 충분히 크지 않거나 스트림 연산이 CPU 집약적이지 않은 경우에는 오히려 성능이 저하될 수 있다.
- 중간 연산의 효율성: 가능한 한 필터링 연산을 먼저 수행하고, 이후에 맵핑 같은 연산을 수행하면 더 효율적이다. 이렇게 하면 처리해야 할 데이터의 크기를 최소화할 수 있다.
- `limit`나 `findFirst`와 같은 짧은 회로 연산은 효율성을 높일 수 있다.

## 마무리

### Java Stream의 장점과 한계

스트림은 데이터 처리를 위한 강력하고 유연한 도구다. 그것들은 복잡한 연산을 단순하게 만들어주며, 가독성을 높이고 코드를 간결하게 만들어 준다. 또한 스트림은 함수형 프로그래밍 패러다임을 지원하여, 병렬 처리와 같은 복잡한 작업을 단순화한다.

그러나 스트림에는 한계가 있다. 예를 들어, 스트림은 한 번만 소비할 수 있으며, 재사용할 수 없다. 또한 스트림 연산은 내부적으로 복잡하여 디버깅이 어려울 수 있다.

### Stream을 활용한 코드 작성 시 고려해야 할 사항

스트림을 사용할 때 다음 사항을 고려해야한다.

- 스트림 연산은 '게으른(lazy)' 특성을 가진다. 이는 최종 연산이 호출되기 전까지 중간 연산이 실행되지 않는다는 것을 의미한다. 이러한 이유로, 중간 연산을 수행하는 데 필요한 데이터가 모두 준비되어 있지 않은 경우, 예기치 않은 결과를 초래할 수 있다.

   ```java
   List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
           
   // map() 연산은 각 요소에 대해 제곱을 계산하고, limit() 연산은 3개의 요소만 가져온다.
   Stream<Integer> stream = numbers.stream()
           .map(n -> {
               int square = n * n;
               System.out.println("Square of " + n + " is " + square);
               return square;
           })
           .limit(3);
   
   // 중간 연산은 최종 연산이 호출되기 전까지 실행되지 않는다. 따라서 map() 연산 및 출력은 실행되지 않는다.
   System.out.println("Lazy evaluation");
   
   // 최종 연산이 호출되면, 중간 연산이 실행된다. 따라서 map() 연산 및 출력이 실행된다.
   stream.forEach(System.out::println);
   ```

   위 코드에서 `map()` 연산은 각 요소에 대해 제곱을 계산하고, `limit()` 연산은 3개의 요소만 가져오는데, `map()` 연산은 최종 연산이 호출되기 전까지 실행되지 않는다. 따라서, `map()` 연산 및 출력은 실행되지 않는다.

- 스트림은 한 번만 사용할 수 있다. 한 번 소비한 스트림은 재사용할 수 없으므로, 다시 사용해야 할 경우 새 스트림을 생성해야 한다.

- 병렬 스트림 사용 시, 데이터의 순서가 중요하지 않은 경우에만 사용하는 것이 좋다. 병렬 스트림은 여러 쓰레드에서 동시에 실행되므로, 연산의 순서를 보장할 수 없다.
