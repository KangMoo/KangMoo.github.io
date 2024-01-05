---
layout: post
title: 자바의 Record 란?
date: 2024-01-03
categories: [Java]
tags: [Java, Record]
---

## Java Record 소개

- **데이터 중심의 불변성과 간결성을 추구하는 클래스**
  - 데이터를 저장하고 전달하는 것에 중점을 둔 클래스
  - 객체의 행동(메서드)보다 객체가 담고 있는 데이터(필드)에 더 많은 중요성을 부여하는 클래스
- JDK14부터 도입되었으며, JDK16에서 정식 스펙으로 포함되었다
- 데이터 전송 객체(Data Transfer Object, DTO)나 값 객체(Value Object)와 같이 데이터를 운반하는 데에 최적화된 구조를 제공한다
- 이전 버전의 Java에서는 클래스를 사용하여 이러한 객체를 만들었지만, Record는 이를 더욱 효율적이고 간결하게 만들어준다

## Record의 특징

- **불변 객체**로 설계되었다. 따라서 한 번 생성된 후에는 내부 상태가 변경될 수 없다
  - Record의 모든 필드는 기본적으로 `private final`이다. 따라서 이후에 변경할 수 없다
  - 이는 불변성을 유지하고 데이터 중심의 설계를 강조하기 위함이다
- 필드명을 명시하지 않고, 필드 순서대로 값을 전달하는 '컴팩트 생성자'를 정의할 수 있다
  - 커스텀 생성자를 정의할 수 있지만, 모든 필드를 초기화해야 한다
- Record는 자동으로 모든 필드에 대한 생성자, `getter` 메서드, `equals()`, `hashCode()`, `toString()` 메서드를 제공하며, 자동으로 `Serializable`을 구현한다
  - 간결성을 제공한다
  - Record의 `getter`는 `get` 접두사 없이 필드명으로 사용한다
- Record는 상속할 수 없으며, `final`로 선언된다

> Record는 위의 특징들로 인해 다음과 같은 이점을 얻을 수 있다
> 
> - 간결한 코드 작성이 가능하다
> - 코드가 간결해지므로 전체적인 가독성이 좋아진다
> - 자동으로 생성되는 메서드로 인해 인간의 실수를 줄일 수 있다

## Record의 사용 예제

### Record 사용 전

```java
public class Product {
    private final String name;
    private final double price;

    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }

    public String getName() {
        return name;
    }

    public double getPrice() {
        return price;
    }

    @Override
    public boolean equals(Object o) {
        // equals 메서드 구현
    }

    @Override
    public int hashCode() {
        // hashCode 메서드 구현
    }

    @Override
    public String toString() {
        // toString 메서드 구현
    }
}
```

### Record 사용 후

```java
public record Product(String name, double price) {
    // 모든 필드, 생성자, 게터, equals(), hashCode(), toString()이 자동으로 생성된다
    // 여기에는 추가적인 메서드나 검증 로직을 포함시킬 수 있다
}
```

---

```java
public class Main {
    public static void main(String[] args) {
        // Record 객체 생성
        Product product = new Product("Apple", 1.99);

        // 게터 메서드 호출 (Record의 경우 필드 이름으로 직접 접근 가능)
        System.out.println("Product Name: " + product.name());
        System.out.println("Product Price: " + product.price());

        // toString 메서드 자동 생성 확인
        System.out.println(product);

        // equals와 hashCode 메서드 자동 생성 확인
        Product sameProduct = new Product("Apple", 1.99);
        System.out.println("Equals: " + product.equals(sameProduct));
        System.out.println("HashCode: " + product.hashCode());
    }
}
```

## Lombok의 `@Value`와 비교

Lombok의 `@Value`는 Record 이전에 만들어졌고, Record는 `@Value`의 장점을 이어받았다

따라서 Lombok의 `@Value`를 접해봤다면 Record와 아주 비슷하다는 느낌을 받을 수 있다. 둘 다 불변 객체를 생성하는데 사용되지만, 세부적인 사용법과 특징에 차이가 있으므로 다음 사항을 고려해서 선택하면 좋다

(물론, 기존 코드에서 Lombok을 사용하고 있었다면, 그대로 사용하면 된다)

### Lombok `@Value` 사용

- JDK 14 이전 버전을 사용하는 경우
- 클래스 내에 추가적인 메서드나 로직이 필요한 경우
- 기존에 작성된 코드와의 호환성이 중요한 경우

### Java Record 사용

- JDK 14 이전 버전을 사용하지 않고 사용할 예정도 없는 경우
- 별도의 의존성 추가를 원하지 않는 경우
