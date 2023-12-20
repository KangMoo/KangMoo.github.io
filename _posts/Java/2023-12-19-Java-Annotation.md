---
layout: post
title: 자바의 Annotation 이란?
date: 2023-12-19
categories: [Java]
tags: [Java, Annotation]
---

## 들어가면서

자바에는 두 가지 종류의 주석이 있다. 주석(Comments)과 어노테이션(Annotations)

주석은 코드의 실행에 전혀 영향을 미치지 않으면서 개발자들이 의도와 로직을 명확하게 전달할 수 있게 도와준다. 그러나 자바의 어노테이션은 단순한 주석을 넘어서는 일을 한다. 어노테이션은 코드에 메타데이터를 추가하여, 컴파일러, 개발 도구, 실행 시간 프레임워크가 이 정보를 사용하여 다양한 작업을 수행할 수 있도록 한다

어노테이션은 코드에 대한 지침이나 설정을 '마킹'하는 데 사용되며, 이는 오버라이딩 검사, 경고 억제, 더 이상 사용되지 않는 코드 표시 등 다양한 형태로 나타날 수 있다. 더 나아가, 어노테이션은 자바 프로그래밍 언어의 기능을 확장하여, 프레임워크 및 라이브러리들이 런타임에 특정 동작을 수행하거나 설정을 적용하는 데 필수적인 역할을 한다

어떻게 이들이 단순한 주석을 넘어서 프로그램의 동작과 구조에 깊이 관여할 수 있는지, 단순히 코드에 대한 메모가 아닌, 강력한 프로그래밍 기법으로서 어떻게 활용될 수 있는지 알아보자

## 자바 어노테이션이란?

- 어노테이션은 코드 사이에 메타데이터를 제공하는 방법을 제공한다
- 이 메타데이터는 컴파일 타임, 런타임, 또는 배포 시에 다양한 방식으로 활용될 수 있다
- 예를 들어, `@Override` 어노테이션은 메서드가 오버라이드 되었음을 나타낸다
- 어노테이션의 사용은 코드의 가독성, 유지보수성을 높이고, 에러를 줄이는 데 도움을 준다

## 어노테이션의 용도

- 컴파일러에 대한 지시 : 예를 들어, `@Override` 어노테이션은 메소드가 오버라이드 되었음을 컴파일러에게 알린다
- 컴파일 타임 및 배포 타임 처리 : 소스 코드를 컴파일하거나 배포할 때 특정 처리를 자동화한다
- 런타임 시 처리 : 어노테이션 정보를 런타임에 사용하여 특정 기능을 실행한다

## 내장 어노테이션

자바에는 여러 내장 어노테이션이 있는데, 대표적인 것들은 다음과 같다

- `@Override`
- `@Deprecated`
- `@SuppressWarnings`
- `@SafeVarargs`
- `@FunctionalInterface`

이 내장 어노테이션들은 자바의 표준 라이브러리에 포함되어 있으며, 각각 특정한 목적을 가지고 있다

### `@Override`

- `@Override` 어노테이션은 메소드가 상위 클래스의 메소드를 오버라이딩한다는 것을 나타낸다
- 만약 상위 클래스에 해당 메소드가 없다면, 컴파일러는 오류를 발생시킨다. 이를 통해 의도하지 않은 메소드 오버라이딩을 방지할 수 있다

### `@Deprecated`

- `@Deprecated` 어노테이션은 특정 요소(클래스, 메소드, 필드 등)가 더 이상 사용되지 않고 향후 버전에서 제거될 가능성이 있음을 나타낸다
- 이 어노테이션을 사용하면 개발자가 해당 요소를 사용할 때 경고를 받게 된다

### `@SuppressWarnings`

- `@SuppressWarnings` 어노테이션은 특정 경고 메시지가 컴파일러에 의해 출력되지 않도록 한다
- 예를 들어, `@SuppressWarnings("deprecation")`은 사용 중인 요소가 더 이상 사용되지 않는다는 경고를 무시하도록 한다

### `@SafeVarargs`

- `@SafeVarargs` 어노테이션은 제네릭 타입의 가변 인자를 사용하는 메소드가 안전하다는 것을 나타낸다
- 이는 제네릭과 가변 인자가 함께 사용될 때 발생할 수 있는 잠재적인 문제를 방지하는 데 도움이 된다

### `@FunctionalInterface`

- `@FunctionalInterface` 어노테이션은 인터페이스가 함수형 인터페이스임을 나타낸다
- 함수형 인터페이스는 정확히 하나의 추상 메소드만 가져야 하며, 이 어노테이션은 그 규칙을 강제하는 데 사용된다

### 내장 어노테이션 예시

```java
public class JavaBuiltInAnnotations {

    @Override
    public String toString() {
        return "JavaBuiltInAnnotations 클래스";
    }

    @Deprecated
    public void deprecatedMethod() {
        // 이 메소드는 더 이상 사용되지 않음
    }

    @SuppressWarnings("unchecked")
    public void suppressWarningMethod() {
        // 경고를 억제하는 메소드
    }

    @SafeVarargs
    public final void safeVarargsMethod(T... args) {
        // 안전한 가변 인자 메소드
    }

    @FunctionalInterface
    interface SimpleFunctionalInterface {
        void execute();
    }
}
```

## 메타 어노테이션

- 메타 어노테이션은 다른 어노테이션을 위한 어노테이션이다
  - 어노테이션의 동작 방식을 지정하거나 어노테이션과 관련된 정보를 제공한다

### `@Target`

- `@Target` 어노테이션은 특정 어노테이션이 적용될 수 있는 Java 요소를 지정한다
- 종류
  - `ElementType.PACKAGE` : 패키지 선언부를 대상으로 한다
  - `ElementType.TYPE` : 타입 선언부를 대상으로 한다
  - `ElementType.CONSTRUCTOR` : 생성자 선언부를 대상으로 한다
  - `ElementType.FIELD` : 필드 선언부를 대상으로 한다
  - `ElementType.METHOD` : 메서드 선언부를 대상으로 한다
  - `ElementType.ANNOTATION_TYPE` : 어노테이션 타입 선언부를 대상으로 한다
  - `ElementType.LOCAL_VARIABLE` : 지역변수 선언부를 대상으로 한다
  - `ElementType.PARAMETER` : 매개변수 선언부를 대상으로 한다
  - `ElementType.TYPE_PARAMETER` : 매개변수 타입 선언부를 대상으로 한다
  - `ElementType.TYPE_USE` : 타입 사용부를 대상으로 한다

### `@Retention`

- `@Retention` 어노테이션은 어노테이션의 유지 정책을 지정한다
- 종류
  - `RetentionPolicy.SOURCE` : 컴파일 전까지만 유효 (컴파일 이후에는 사라짐)
  - `RetentionPolicy.CLASS` : 컴파일러가 클래스를 참조할 때까지 유효
  - `RetentionPolicy.RUNTIME` : 컴파일 이후에도 JVM에 의해 계속 참조가 가능

### `@Documented`

- `@Documented` 어노테이션은 해당 어노테이션이 Javadoc에 포함되어야 함을 나타낸다

### `@Inherited`

- `@Inherited` 어노테이션은 어노테이션이 서브클래스에 상속될 수 있음을 나타낸다

### `@Repeatable`

- `@Repeatable` 어노테이션은 Java 8부터 도입되어, 하나의 요소에 동일한 어노테이션을 여러 번 적용할 수 있게 한다

## 커스텀 어노테이션

- 커스텀 어노테이션은 메타 어노테이션을 이용하여 사용자가 직접 정의하는 어노테이션이다
- 자바는 기본적으로 여러 어노테이션을 제공하지만, 특정한 경우에는 사용자 정의 어노테이션을 만들어 사용하는 것이 유용하다

### 커스텀 어노테이션 예시

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

// 메타 어노테이션을 사용하여 커스텀 어노테이션 정의
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface CustomAnnotation {
    String value();
}
```

```java
public class AnnotationExample {

    @CustomAnnotation(value = "Example Method")
    public void exampleMethod() {
        // 메소드 구현
    }
}
```

## 커스텀 어노테이션 활용

자바에서 커스텀 어노테이션을 활용하는 방법은 다양하다. 커스텀 어노테이션을 활용하는 방법 몇가지를 살펴보자

### 어노테이션과 Reflection

- 자바 Reflection을 사용하면 런타임에 클래스의 메타데이터를 읽고, 수정할 수 있다
- 커스텀 어노테이션의 경우, Reflection을 통해 어노테이션이 적용된 메소드를 찾고, 추가적인 로직을 실행할 수 있다.

#### 예시 1 : 리플렉션을 통한 어노테이션 조회

``` java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.reflect.Method;

public class AnnotationReflectionExample {

    public static void main(String[] args) {
        try {
            // ExampleClass의 Class 객체를 가져온다
            Class<?> clazz = ExampleClass.class;

            // 'exampleMethod' 메서드의 Method 객체를 가져온다
            Method method = clazz.getMethod("exampleMethod");

            // 해당 메서드에 적용된 MyAnnotation 어노테이션을 가져온다
            if (method.isAnnotationPresent(MyAnnotation.class)) {
                MyAnnotation myAnnotation = method.getAnnotation(MyAnnotation.class);
                System.out.println("Value: " + myAnnotation.value());
            }
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
    }

    // 커스텀 어노테이션 정의
    @Retention(RetentionPolicy.RUNTIME)
    @interface MyAnnotation {
        String value() default "Default Value";
    }

    // ExampleClass 정의
    static class ExampleClass {
        // 어노테이션 적용
        @MyAnnotation(value = "Hello, Annotation!")
        public void exampleMethod() {
        }
    }
}
```

> **실행 결과**
>
> ``` shell
> Value: Hello, Annotation!
> ```

#### 예시 2 : 리플렉션을 통한 커스텀 어노테이션이 적용된 메소드의 실행 시간 로깅

- 앞선 예시를 활용하면 메서드의 수행 시간을 계산하는 작업도 가능하다

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.reflect.Method;

public class AnnotationReflectionExample2 {
    public static void main(String[] args) throws Exception {
        process(new ExampleClass());
    }

    static public void process(Object obj) throws Exception {
        for (Method method : obj.getClass().getDeclaredMethods()) {
            if (method.isAnnotationPresent(LogExecutionTime.class)) {
                long startTime = System.currentTimeMillis();
                method.invoke(obj);
                long endTime = System.currentTimeMillis();
                System.out.println(method.getName() + " 실행 시간: " + (endTime - startTime) + "ms");
            }
        }
    }

    // 커스텀 어노테이션 정의
    @Retention(RetentionPolicy.RUNTIME)
    @interface LogExecutionTime {
    }

    static class ExampleClass {
        @LogExecutionTime
        public void exampleMethod() throws InterruptedException {
            Thread.sleep((long) (Math.random() * 100));
        }
    }
}
```

> **실행 결과**
>
> ``` shell
> exampleMethod 실행 시간: 79ms
> ```

## 어노테이션의 한계와 주의사항

어노테이션은 유용하게 사용되지만 몇가지 주의해야할 사항들이 있다

- 런타임 오버헤드 : 어노테이션은 런타임에 분석되어야 하므로 성능에 영향을 미칠 수 있다
- 남용의 위험 : 너무 많은 어노테이션은 코드를 복잡하게 만들 수 있다
- 호환성 문제 : 일부 어노테이션의 경우 특정 라이브러라나 프레임워크에 의존적이므로 호환성에 문제가 발생할 수 있다

## 더 나아가서

앞서 제시한 예시의 사용법 외에도 어노테이션은 더 강력하고 다양하게 활용될 수 있다. 어노테이션에 대해 더 공부하고 활용해보고 싶다면 다음 내용도 공부해보자

- AOP(Aspect Oriented Programming)
  - 어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 각각 모듈화하는 패러다임
  - AOP를 위한 라이브러리로는 Spring AOP, AspectJ 등의 라이브러리들이 있으며, 어노테이션을 이용한 AOP를 지원한다
- 어노테이션 프로세싱
  - 컴파일 타임에 어노테이션을 분석하고 처리하는 과정을 통해 소스 코드를 생성하거나 수정하는 가능
  - 예를 들어, Lombok 라이브러리는 이 기능을 사용하여 바이트 코드를 조작하고, 게터, 세터, 생성자 등을 자동으로 생성할 수 있다
