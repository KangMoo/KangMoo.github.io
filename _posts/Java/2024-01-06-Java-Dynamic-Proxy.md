---
layout: post
title: 자바 동적 프록시(Dynamic Proxy) 란?
date: 2024-01-06
categories: [Java]
tags: [Java, Dynamic Proxy, Proxy]
---

![proxy](/assets/img/2024-01-06-Java-Dynamic-Proxy/2024-01-05-15-24-38.png)

## 동적 프록시(Dynamic Proxy)란?

- 자바 동적 프록시는 자바에서 객체의 인터페이스를 구현하여 런타임 시에 객체의 메서드 호출을 제어하는 기술.
- 대부분의 사용자가 자신도 모르게 사용하고 있으며, AOP(Aspect-Oriented Programming)와 같은 기술의 핵심 요소로 활용된다

## 동적 프록시의 개념

- 동적 프록시는 '프록시(Proxy)'라는 용어에서 시작한다
- 프록시는 대리자 또는 중계자라는 의미로, 어떤 객체를 대신하여 그 객체의 기능을 제공한다
- 자바에서 프록시 객체는 대상 객체와 같은 인터페이스를 구현하며, 대상 객체의 메서드 호출을 중계하는 역할을 한다
- "전화 응답 서비스"에 비유해 볼 수 있다. 예를 들어, 어떤 회사에 전화를 걸었을 때, 직접 관련된 사람과 바로 통화하는 것이 아니라 대리자(Proxy)가 대신 수신받는다. 대리자는 누가 전화를 걸었는지, 어떤 요청을 하는지를 먼저 확인하고, 그 다음에 적절한 부서나 사람에게 전화를 연결해 준다.
  - 동적 프록시는 비슷한 역할을 한다. 프로그램에서 어떤 메소드(작업)를 호출하려고 할 때, 동적 프록시가 먼저 이 호출을 받고, 프록시는 이 호출에 대한 정보를 분석하고, 필요한 경우 추가 작업을 수행한 후, 실제 메소드로 요청을 전달한다. 이 과정을 통해, 개발자는 보안 검사, 로깅, 트랜잭션 처리 등의 공통적인 기능들을 메소드 호출에 쉽게 추가할 수 있다

### 프록시의 기본 원리

- 메서드 호출 중계 : 대상 객체의 메서드를 호출할 때, 프록시 객체를 거쳐 메서드가 호출된다.
- 추가 기능 구현 : 프록시 객체는 원래 객체의 기능에 추가적인 기능을 수행할 수 있다.

### 동적 프록시의 특징

- 동적 프록시 객체는 프로그램 실행 중에 생성된다
- 하나의 프록시 클래스로 여러 인터페이스의 구현체를 대신할 수 있다
- 공통 기능을 프록시 클래스에 구현하여 코드 중복을 줄일 수 있다

## 동적 프록시의 사용 사례

- AOP(Aspect-Oriented Programming)
  - 트랜잭션 관리 : 메서드 실행 전후로 트랜잭션을 시작하고 커밋 또는 롤백한다
  - 로깅 및 모니터링 : 메서드 실행 정보를 로깅하거나 성능을 모니터링한다
- 리모트 메서드 호출(Remote Method Invocation, RMI)
  - 네트워크 통신 : 원격 객체에 대한 메서드 호출을 네트워크를 통해 전달한다
- 리소스 관리
  - 자원 해제 자동화 : 객체의 메서드 실행 후 자동으로 리소스를 해제한다

## 동적 프록시 구현 방법

- 자바에서 동적프록시를 구현하는 방법은 크게 두 가지가 있다.
  - 인터페이스 기반 동적 프록시 : 주로 `java.lang.reflect.Proxy` 클래스와 `java.lang.reflect.InvocationHandler` 인터페이스를 사용하여 동적 프록시를 구현한다
  - 클래스 기반 동적 프록시 : 주로 CGLib 라이브러리를 사용하여 동적 프록시를 구현한다

## 인터페이스 기반 동적 프록시

### `InvocationHandler` 인터페이스

- 대상 객체의 메서드 호출을 처리하는 인터페이스로, `invoke` 메서드를 오버라이딩하여 메서드 호출 시 수행할 작업을 정의한다.

```java
// Java Reflect API를 이용한 InvocationHandler 구현 예제
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class DynamicProxyHandler implements InvocationHandler {
    private Object target;

    public DynamicProxyHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 메서드 호출 전 처리할 작업
        System.out.println("[Proxy] Before method: " + method.getName());

        // 실제 객체의 메서드 호출
        Object result = method.invoke(target, args);

        // 메서드 호출 후 처리할 작업
        System.out.println("[Proxy] After method: " + method.getName());

        return result;
    }
}
```

### `Proxy` 클래스

- 동적 프록시 객체를 생성하는 클래스로, `newProxyInstance` 메서드를 사용하여 프록시 객체를 생성한다

```java
// Proxy 클래스를 이용한 동적 프록시 객체 생성 예제
import java.lang.reflect.Proxy;

public class DynamicProxyDemo {
    public static void main(String[] args) {
        // 원본 객체 생성
        MyInterface original = new MyInterfaceImpl();

        // 프록시 객체 생성
        MyInterface proxy = (MyInterface) Proxy.newProxyInstance(
                MyInterface.class.getClassLoader(),
                new Class<?>[] { MyInterface.class },
                new DynamicProxyHandler(original)
        );

        // 프록시 객체를 통한 메서드 호출
        proxy.someMethod();
    }
}

interface MyInterface {
    void someMethod();
}

class MyInterfaceImpl implements MyInterface {
    @Override
    public void someMethod() {
        System.out.println("Executing someMethod");
    }
}
```

> **실행 결과**
>
> ```
> [Proxy] Before method: someMethod
> Executing someMethod
> [Proxy] After method: someMethod
> ```

## 클래스 기반 동적 프록시 (GCLib)

- CGLib는 코드 생성 라이브러리로, 자바 클래스의 바이트 코드를 조작하여 서브 클래스를 동적으로 생성하는 기능을 제공한다
  - 동적 프록시 외에도 많은 강력한 기능들이 있다
- CGLib을 사용하면 인터페이스가 아닌 클래스를 상속받아 프록시를 생성할 수 있다
- 외부 라이브러리를 사용하므로, 별도의 의존성 추가가 필요하다
  - `implementation group: 'cglib', name: 'cglib'`

### `MethodInterceptor` 인터페이스

- 대상 객체의 메서드 호출을 가로채는 인터페이스로, `intercept` 메서드를 구현하여 메서드 호출 시 수행할 작업을 정의한다

```java
// CGLib를 이용한 MethodInterceptor 구현 예제
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class CGLibDynamicProxy implements MethodInterceptor {
    private Object target;

    public Object createProxy(Object target) {
        this.target = target;
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(this.target.getClass());
        enhancer.setCallback(this);
        return enhancer.create();
    }

    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        // 메서드 호출 전 처리할 작업
        System.out.println("[Proxy] Before method: " + method.getName());

        // 실제 객체의 메서드 호출
        Object result = proxy.invokeSuper(obj, args);

        // 메서드 호출 후 처리할 작업
        System.out.println("[Proxy] After method: " + method.getName());

        return result;
    }
}
```

### `Enhancer` 클래스

- CGLib 프록시 객체를 생성하는 클래스로, `create` 메서드를 사용하여 프록시 객체를 생성한다

```java
// CGLib를 이용한 동적 프록시 객체 생성 예제
public class CGLibDemo {
    public static void main(String[] args) {
        // 원본 객체 생성
        MyClass original = new MyClass();

        // CGLib 프록시 객체 생성
        MyClass proxy = (MyClass) new CGLibDynamicProxy().createProxy(original);

        // 프록시 객체를 통한 메서드 호출
        proxy.someMethod();
    }
}

class MyClass {
    public void someMethod() {
        System.out.println("Executing someMethod");
    }
}
```

> **실행 결과**
>
> ```
> [Proxy] Before method: someMethod
> Executing someMethod
> [Proxy] After method: someMethod
> ```
---

> 만약 자바 모듈을 사용할 경우 다음과 같은 에러가 발생할 수 있다
> 
> `java.lang.reflect.InaccessibleObjectException-->Unable to make protected final java.lang.Class java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain) throws java.lang.ClassFormatError accessible: module java.base does not "opens java.lang"`
> 
> 이 에러는 CGLib와 같은 라이브러리가 Java의 내부 API에 접근하려고 할 때 발생하며, 이 문제를 해결하려면 다음 옵션을 JVM에 옵션을 추가해야한다
> 
> `--add-opens java.base/java.lang=ALL-UNNAMED`

## 동적 프록시의 장단점

### 장점

- 다양한 타입의 객체에 대해 동일한 프록시 클래스를 사용할 수 있어 유연한 코드 작성이 가능해진다
- 공통적인 기능을 프록시에서 처리하여 중복을 줄일 수 있다
- 공통 기능 변경 시 프록시 클래스만 수정하면 되므로 유지보수가 용이해질 수 있다

### 단점

- 프록시의 사용으로 인해 시스템의 복잡성이 증가할 수 있다
- 추가적인 메서드 호출과 리플렉션 사용으로 인한 성능 저하가 발생할 수 있다
  - 현대의 컴퓨터 성능과 JVM 최적화로 인해 어느정도 무시해도 되는 수준이긴 하다
- 인터페이스 기반 동적 프록시를 사용할 경우, 인터페이스를 사용하는 클래스 구조로 설계해야한다
- 클래스 기반 동적 프록시를 사용할 경우, 추가적인 라이브러리 의존성이 발생한다 (CGLib)

## 마치며

자바 동적 프록시의 개념과 그 활용법에 대해 이해하고 적절히 활용한다면, 보다 효율적이고 강력한 프로그래밍이 가능해진다

동적프록시를 처음 접해봤다면 이해하는 데 어려움을 겪을 수도 있겠지만, 이해하고 나면 매우 강력하고 유용한 도구라는 것을 알게 될 것이다

항상 새로운 기술을 배우고 적용하는 것은 쉽지 않지만 깊은 이해를 위해 동적 프록시를 적극 활용하게 된다면, 그 과정에서 얻는 지식과 경험은 분명히 개발 역량을 한층 더 끌어올릴 것이다
