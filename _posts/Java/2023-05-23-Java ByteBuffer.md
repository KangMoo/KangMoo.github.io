---
layout: post
title: ByteBuffer를 이해하고 사용하기
date: 2023-05-22 22:32
categories: [Java]
tags: [Java]
---

## ByteBuffer란?

- `ByteBuffer`는 Java NIO 라이브러리의 중요한 컴포넌트로, 바이트 기반 데이터를 처리하는 데 사용됩니다. 이는 파일이나 네트워크로부터 데이터를 읽거나 쓸 때, 그리고 바이트 기반 데이터를 처리할 때(예: 암호화, 압축 등) 유용합니다.

## ByteBuffer의 네 가지 포인터

- `ByteBuffer`는 내부적으로 바이트 배열을 유지하며, 이 배열을 사용해 데이터를 읽고 쓰는 역할을 합니다. 이를 더욱 효율적으로 사용하도록 돕는 것이 바로 `position`, `limit`, `capacity` 등의 개념들입니다.
  
- Position : 버퍼에서 현재 읽고 쓰는 위치를 나타냅니다. 즉, 데이터를 읽거나 쓰는 작업이 수행될 때마다 position 값이 증가합니다. 
- Limit : 버퍼에서 읽거나 쓸 수 있는 데이터의 최대 범위를 표시합니다. 쓰기 모드에서 limit는 일반적으로 capacity와 같습니다. 읽기 모드에서는 새로 읽을 수 있는 바이트의 수를 나타냅니다.
- Capacity : 버퍼가 저장할 수 있는 데이터의 최대 크기를 나타냅니다. `ByteBuffer`가 처음 생성될 때 설정되며, 이후 변경될 수 없습니다.
- Mark : 버퍼의 특정 위치를 기억하기 위한 장치입니다. 나중에 `reset()` 메서드를 호출하면 position이 이 mark 위치로 돌아갑니다. 기본적으로 mark는 정의되지 않은 상태이며, `mark()` 메서드를 호출해야만 설정됩니다.

> 각 포인터의 제약조건은 다음과 같다
> 0 <= mark <= position <= limit <= capacity

## ByteBuffer의 주요 메서드
- `allocate(int capacity)`
   - JVM 힙 메모리에 ByteBuffer를 할당합니다. 이는 주로 JVM 내부에서 데이터를 읽고 쓰는 작업에 사용됩니다.
- `allocateDirect(int capacity)`
   - 주 메모리에 직접적으로 할당된(ByteBuffer에게 독립적인) ByteBuffer를 생성합니다. 이 메서드를 사용하면, I/O 작업을 할 때 시스템이 별도의 버퍼를 사용할 필요 없이 해당 버퍼를 직접 사용할 수 있어, 버퍼링 오버헤드를 줄일 수 있습니다.
- `put(byte b)`
   - 현재 position 위치에 바이트를 쓰고, position을 하나 증가시킵니다.
- `get()`
   - 현재 position의 바이트를 읽고, position을 하나 증가시킵니다.
- `flip()`
   - 버퍼를 읽기 모드로 바꾸기 위해 limit을 현재 position으로 설정하고, position을 0으로 설정합니다.
- `rewind()`
   - position을 0으로 설정합니다. 이를 통해 버퍼의 데이터를 다시 읽거나 쓸 수 있게 됩니다.
- `compact()`
   - 현재 position부터 limit까지의 남아있는 데이터를 버퍼의 시작으로 이동시키고, position을 새로운 limit로 설정합니다. 이는 쓰기 모드로 전환할 때 주로 사용됩니다.
- `hasRemaining()`
   - position과 limit 사이에 읽거나 쓸 수 있는 요소가 남아있는지를 확인합니다. 남아있는 요소가 있으면 true를 반환하고, 그렇지 않으면 false를 반환합니다.
- `clear()`
   - position을 0으로, limit을 capacity로 설정하여 버퍼를 초기화합니다. 이는 버퍼를 새로 쓰기 위해 사용됩니다.
- `mark()`
   - mark를 현재 position으로 설정합니다. 이는 특정 position을 나중에 다시 참조하기 위해 사용됩니다.
- `reset()`
   - position을 이전에 mark로 설정한 위치로 되돌립니다.
- `wrap(byte[] array)`
   - 주어진 바이트 배열을 감싸는 ByteBuffer를 생성합니다. 이 ByteBuffer의 capacity와 limit는 주어진 배열의 길이와 같게 설정됩니다. 이 메서드는 기존 배열을 버퍼로 쉽게 변환할 수 있게 해줍니다.

> `allocate`는 일반적인 용도에 적합하며, `allocateDirect`는 대용량 데이터 처리나 장시간 동안 살아있는 버퍼가 필요한 특정한 경우에 적합합니다.

## ByteBuffer의 사용 예시

- P : Position, L : Limit, C : Capacity

### `allocate`

- 8개의 글자가 들어갈 수 있는 버퍼를 만든다. allocate하면 버퍼는 현재 쓰기모드이다

```java
CharBuffer buf = CharBuffer.allocate(8); // capacity : 8
//	P               L
//  |0|1|2|3|4|5|6|7|
//  | | | | | | | | |
```



### `put`

- put을 사용할 경우 Position이 이동하면서 버퍼에 데이터가 들어간다.

```java
//	P               L
//  |0|1|2|3|4|5|6|7|
//  | | | | | | | | |

buf.put('a'); buf.put('b'); buf.put('c'); buf.put('e');

// P------>P       L 
// |0|1|2|3|4|5|6|7|
// |a|b|c|e| | | | |

buf.put(3,'d');

//         P       L 
// |0|1|2|3|4|5|6|7|
// |a|b|c|d| | | | |
```



### `flip`

- 입력된 데이터를 읽기 위해 read-mode로 바꾼다. (읽기 좋게 바꾼다)
- 버퍼의 시작이자 데이터의 시작인 0으로 Position을 이동하고, 데이터의 끝 부분에 L이 위치하게 된다.
  - Limit이 Position이 되고, Position이 0이 된다.

```java
//         P       L 
// |0|1|2|3|4|5|6|7|
// |a|b|c|d| | | | |

buf.flip();

//          L<------L 
//  P<------P 
//  |0|1|2|3|4|5|6|7|
//  |a|b|c|d| | | | | 
//  L = P
//  P = 0
```



### `get`

- Position을 이동시키며 데이터를 읽는다

```java
//  P               L
//  |0|1|2|3|4|5|6|7|
//  |a|b|c|d| | | | | 

char a, b, c;
a = buf.get(); b = buf.get(); c = buf.get();

// P---->P         L 
// |0|1|2|3|4|5|6|7|
// |a|b|c|d| | | | |
```



### `compact`

- 데이터를 쓰기모드로 바꾼다.
- 읽지 않은 데이터 'd'가 복사되고, 'd'의 바로 뒤에 쓸 수 있도록 Position이 변했다.

```java
//       P         L 
// |0|1|2|3|4|5|6|7|
// |a|b|c|d| | | | |

buf.compact();

//   P<--P
//         L------>L 
// |0|1|2|3|4|5|6|7| 
// |d| | | | | | | | 
// 남아있는 데이터를 처음으로 복사 
// P = 남아있는 데이터 수 
// L = C
```
