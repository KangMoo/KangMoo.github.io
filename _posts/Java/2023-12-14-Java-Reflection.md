---
layout: post
title: 자바의 Reflection 이란?
date: 2023-12-18
categories: [Java]
tags: [Java, Reflection]
---

![java_reflection_humor](/assets/img/2023-12-14-Java-Reflection/2023-12-19-09-39-29.png)

## 자바의 리플렉션(Reflection)이란?

- 실행 중인 자바 애플리케이션 내부를 검사하고, 수정할 수 있는 강력한 기능
  - 강력하다 = 큰 책임감이 따른다
- 클래스, 인터페이스, 메서드, 필드, 생성자 및 모듈과 같은 프로그램의 다양한 측면을 **런타임 중**에 검사하고 수정할 수 있는 기능을 제공한다
- 리플렉션을 사용할 경우 다음과 같은 이점을 얻을 수 있다
  - 유연성 : 리플렉션을 사용하면 컴파일 시점에는 알 수 없는 클래스나 메소드에 대해 작업할 수 있게 함으로써 API 또는 프레임워크를 설계할 때 특히 유용하게 사용될 수 있다
  - 결합도 감소 : 리플렉션은 클래스 간의 결합도를 낮출 수 있게 해주며, 이는 유지보수성과 확장성을 향상시킬 수 있다.
  - 동적인 기능 : 리플렉션을 통해 런타임에 동적으로 특정 기능을 수행하거나 변경하는 것이 가능해지며 동적 어플리케이션 개발에 필수적인 요소로 사용될 수 있다

---

## 리플렉션은 언제 사용할까?

리플렉션을 주로 언제 사용하는지 알아보며 필요성을 느껴보자

- 프라이빗 메소드나 필드에 접근하여 조회하여 디버깅 및 테스트를 해야할 때 (ex. Junit)
- 클래스의 속성, 메소드, 생성자 등에 대한 동적 타입 조사 및 조작이 필요한 경우
- 클래스 이름이나 구조를 동적으로 파악해야하는 경우
- DI(종속성 주입) 컨테이너 및 관점 지향 프로그래밍 시스템과 같은 프레임워크를 구현할 때 (ex. Spring Framework)
- 어노테이션 정보를 런타임에 읽어서 특정 로직을 수행해야할 때
- 애플리케이션에 플러그인이나 확장 기능을 동적으로 로드하고 실행할 때
- 등등..

---

## 리플렉션의 작동 원리

자바 리플렉션 사용해보기 앞서 자바 리플렉션에 대한 원리를 이해해보자

1. 클래스 로딩
   - 자바에서 모든 클래스는 사용되기 전에 클래스 로더에 의해 메모리에 로드된다
   - 클래스 로더는 `.class` 파일(바이트코드)을 JVM 내부의 정의된 메모리 영역(메소드 영역)에 로드한다
   - 이 과정에서 **클래스의 메타데이터(클래스 이름, 메소드, 필드 정보 등)가 메모리에 저장**된다
2. Class 객체
   - JVM 내에 로드된 클래스마다 하나의 `java.lang.Class` 객체가 생성된다
   - 이 `java.lang.Class` 객체는 로드된 클래스의 메타데이터를 가지고 있으며, 리플렉션은 이 `java.lang.Class` 객체를 사용하여 클래스의 정보를 조회하거나 조작한다
3. 리플렉션 API 사용
   - `java.lang.Class` 는 해당 클래스의 메타데이터(예: 클래스 이름, 메소드, 필드 등)에 접근할 수 있도록 도와주는 API가 있으며, 리플렉션은 이 API를 이용한다
   - 예를 들어, `getMethod()`, `getField()`, `getConstructor()` 등의 메소드를 통해 해당 클래스의 메소드, 필드, 생성자 정보를 조회할 수 있다
   - 이러한 메소드들은 `java.lang.reflect` 패키지의 `Method`, `Field`, `Constructor` 등의 클래스를 반환한다
   - 반환된 객체를 통해 메소드를 호출하거나, 필드의 값을 조회/수정할 수 있다
4. 동적 조작
   - 리플렉션을 사용하면, 컴파일 시점에는 알 수 없었던 클래스의 이름이나 메소드를 실행 시점에 결정하고 조작할 수 있다
   - 예를 들어, 문자열로 된 클래스 이름으로 `Class.forName()` 메소드를 호출하여 해당 클래스의 `Class` 객체를 얻을 수 있다
   - 이를 통해 동적으로 객체를 생성하거나, 필드를 조회하거나 메소드를 호출하는 등의 작업을 수행할 수 있다

---

## 리플렉션 사용해보기

---

### 클래스 정보 얻기

리플렉션을 통해 다양한 방식으로 클래스의 정보를 가져올 수 있다

1. 객체를 통한 클래스 정보 얻기

``` java
MyClass obj = new MyClass();
Class cls = obj.getClass();
```

2. 클래스 리터럴을 통한 클래스 정보 얻기

``` java
Class cls = MyClass.class;
```

3. 클래스 이름을 통해 클래스 정보 얻기

``` java
Class cls = Class.forName("com.example.MyClass");
```

---

### 필드 정보 조회 및 조작

리플렉션을 통해 런타임에 객체의 필드(인스턴스 변수)에 접근하여 조회/수정 등이 가능하다

```java
import java.lang.reflect.Field;

public class ReflectionExample {
    public static void main(String[] args) {
        try {
            // Person 객체 생성
            Person person = new Person();

            // Person 클래스의 Class 객체 가져오기
            Class<?> clazz = person.getClass();

            // 'name' 필드에 접근
            Field nameField = clazz.getDeclaredField("name");

            // private 필드 접근을 위해 접근성 변경
            nameField.setAccessible(true);

            // 필드 값 읽기
            System.out.println("기존 이름: " + person.getName());

            // 필드 값 변경
            nameField.set(person, "Alice");

            // 변경된 필드 값 읽기
            System.out.println("변경된 이름: " + person.getName());

        } catch (NoSuchFieldException | IllegalAccessException e) {
            e.printStackTrace();
        }
    }

    static class Person {
        private String name = "Unknown";

        public String getName() {
            return name;
        }
    }
}
```

> **실행 결과**
> 
> ``` shell
> 기존 이름: Unknown
> 변경된 이름: Alice
> ```

---

### 생성자, 메서드 조회 및 사용

리플렉션을 통해 클래스의 생성자, 메서드를 조회/수정할 수 있다

``` java
import java.lang.reflect.Constructor;
import java.lang.reflect.Method;

public class ReflectionDemo {

    public static void main(String[] args) {
        try {
            // Person 클래스의 Class 객체를 가져오기
            Class<?> clazz = Class.forName("ReflectionDemo$Person");

            // Person 클래스의 기본 생성자를 가져오기
            Constructor<?> constructor = clazz.getConstructor();

            // 생성자를 사용하여 Person 인스턴스를 생성
            Object personInstance = constructor.newInstance();

            // setName 메서드를 가져오기
            Method setNameMethod = clazz.getMethod("setName", String.class);
            // setName 메서드를 실행
            setNameMethod.invoke(personInstance, "Alice");

            // getName 메서드를 가져오기
            Method getNameMethod = clazz.getMethod("getName");
            // getName 메서드를 실행하고 결과를 출력
            System.out.println("Name: " + getNameMethod.invoke(personInstance));

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    static class Person {
        private String name;

        public Person() {
            this.name = "Unknown";
        }

        public void setName(String name) {
            this.name = name;
        }

        public String getName() {
            return this.name;
        }
    }
}
```

> **실행 결과**
>
> ``` shell
> Name: Alice
> ```

---

### 리플렉션과 프록시를 사용한 메서드 조작

프록시(proxy)를 사용하여 메서드의 처리 로직이나 결과를 조작할 수 있다. 프록시는 Java 의 메서드 호출을 중간에 가로채고, 사용자 정의 로직으로 처리하거나 조작할 수 있게 해주는 기술이다

자바에서 프록시는 주로 인터페이스 기반의 동적 프록시(Dynamic Proxy)와, 클래스 기반의 프록시(CGLIB 등의 라이브러리를 사용) 두 가지 방법으로 구현되는데, 여기서는 Java의 내장 기능을 사용하는 인터페이스 기반의 동적 프록시에 대해 간단히만 알아본다

**동적 프록시의 사용법**

1. 인터페이스 정의
2. `InvocationHandler` 구현
3. 프록시 객체 생성 및 사용

``` java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyExample {

    public static void main(String[] args) {
        // 원래의 Person 객체 생성
        Person realPerson = new PersonImpl("Alice");

        // 프록시 객체 생성
        Person proxyPerson = (Person) Proxy.newProxyInstance(
                Person.class.getClassLoader(),
                new Class<?>[] { Person.class },
                new CustomInvocationHandler(realPerson)
        );

        // 프록시 객체를 통한 getName 호출
        System.out.println(proxyPerson.getName());
    }

    interface Person {
        String getName();
    }

    static class PersonImpl implements Person {
        private String name;

        public PersonImpl(String name) {
            this.name = name;
        }

        @Override
        public String getName() {
            return name;
        }
    }

    // InvocationHandler 구현
    static class CustomInvocationHandler implements InvocationHandler {
        private final Object target;

        public CustomInvocationHandler(Object target) {
            this.target = target;
        }

        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            // 메서드 호출 전에 할 작업
            System.out.println("Before method: " + method.getName());

            Object result = method.invoke(target, args);

            // 메서드 결과 조작
            if (method.getName().equals("getName")) {
                result = "Hello, " + result + "!";
            }

            // 메서드 호출 후에 할 작업
            System.out.println("After method: " + method.getName());

            return result;
        }
    }
}
```

> **실행 결과**
>
> ``` shell
> Before method: getName
> After method: getName
> Hello, Alice!
> ```

---

### 리플렉션을 통한 어노테이션 조회

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

---

## 리플렉션 사용 시 주의사항

리플렉션은 코드를 동적으로 코드를 조작할 수 있는 아주 강력한 기능이다. 당연히 강력한 기능인 만큼 사용 시 고려해야할 사항들이 많다

1. 성능
   - 리플렉션을 통한 메서드 호출이나 필드 접근 방법은 일반적인 방법보다 느리므로 성능에 민감한 어플리케이션에서는 주의가 필요하다
   - 리플렉션을 사용하면 컴파일러나 JVM이 코드에 대한 최적화를 하기가 어렵다
2. 보안
   - 리플렉션을 사용하면 `private`, `protected`멤버에 접근할 수 있는데, 이는 캡슐화를 위반할 뿐더러 여러 보안 취약점을 초래할 수 있다
   - 보안이 중요한 어플리케이션에서는 보안 관리자(Security Manager)를 사용하여 리플렉션 사용에 제한을 두는 것이 좋다
3. 유지보수
   - 리플렉션을 사용한 코드는 직접적이지 않고 복잡할 수 있어, 코드의 가독성과 이해도를 저하시킨다 (위의 예시만 해도 한눈에 읽히지 않고 결과를 예측하기 어렵다)
   - 리플렉션은 중간에 코드를 조작해버릴 수 있으니 디버깅도 어렵게 만들 수 있다

---

## 결론

리플렉션은 자바의 강력하면서도 유연한 기능 중 하나로, 클래스의 메타데이터에 접근하고, 런타임 중에 클래스의 행위를 조작할 수 있게 해준다. 이를 통해 개발자는 클래스의 내부 정보를 검사하고, 프라이빗 멤버에 접근하며, 유연하게 코드를 작성할 수 있게 된다. 리플렉션은 특히 API 및 프레임워크 개발, 플러그인 시스템, 테스트 코드 작성 등 다양한 상황에서 유용하게 활용될 수 있다.

하지만, 리플렉션은 그 사용에 있어서 신중함이 요구된다. 성능 저하, 보안 이슈, 유지보수의 어려움 등 리플렉션의 부작용도 고려해야 한다. 특히, 리플렉션은 캡슐화를 위반하고 코드의 명확성을 흐릴 수 있어, 이를 사용할 때는 해당 영역의 필요성과 효과를 면밀히 고려해야 한다.

최종적으로, 리플렉션은 자바 프로그래밍의 강력한 도구지만, 그 사용은 적절한 상황에서 신중하게 선택되어야 한다. 불필요하거나 과도한 리플렉션 사용은 애플리케이션의 성능과 안정성을 저해할 수 있으므로, 이점을 항상 염두에 두고 적절한 균형을 찾아야 한다.
