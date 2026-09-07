# 2.1.2 데이터 크기와 워드 크기

> Data Sizes

## 핵심 개념

* 컴퓨터의 메모리는 프로그램 관점에서 **바이트(Byte)의 배열**로 볼 수 있음
* 각 바이트에는 해당 위치를 식별하기 위한 **주소(Address)** 존재
* 프로그램이 사용할 수 있는 주소들의 집합을 **가상 주소 공간(Virtual Address Space)** 이라고 함

```text
Virtual Address Space

Address
0x0000  → [ Byte ]
0x0001  → [ Byte ]
0x0002  → [ Byte ]
0x0003  → [ Byte ]
...
```

* 한 주소는 일반적으로 하나의 바이트 위치를 가리킴

* 프로그램은 이러한 주소를 이용하여 메모리의 코드와 데이터를 참조

* 주소를 표현할 수 있는 범위는 시스템의 **워드 크기(Word Size)** 와 밀접한 관계

---

## 워드 크기 — Word Size

* 모든 컴퓨터에는 고유한 **워드 크기 `w`** 존재
* CS:APP에서는 워드 크기를 특히 **포인터 데이터의 명목상 크기**와 연결하여 설명

```text
w-bit system
    ↓
pointer representation
    ↓
virtual address representation
```

* `w`비트로 주소를 표현할 수 있다고 가정하면 주소 범위는 다음과 같음

```text
0 ~ 2ʷ - 1
```

* 따라서 표현 가능한 가상 주소 공간의 이론적인 최대 크기

```text
2ʷ bytes
```

예를 들어 32비트 워드 크기라면:

```text
w = 32

address range
0 ~ 2³² - 1

maximum address space
2³² bytes
```

---

## 32비트 시스템

* 32비트 주소를 사용한다고 가정하면 가능한 주소 개수

```text
2³²
```

* 하나의 주소가 1바이트를 가리키므로 이론적인 최대 가상 주소 공간

```text
2³² bytes
= 4 GiB
```

일반적으로 약 `4 GB`라고 표현하기도 함.

```text
0x00000000
      ~
0xFFFFFFFF
```

* 주소 표현에 32비트 사용
* 전통적인 32비트 시스템에서 포인터 크기는 일반적으로 4바이트

```c
sizeof(void *) == 4
```

* 주소 공간 자체가 제한적이기 때문에 대규모 데이터를 처리하는 프로그램에서는 중요한 제약이 될 수 있음

> `2³² bytes`는 주소 표현으로 계산한 이론적 공간이며, 실제 프로세스가 이 전체 영역을 자유롭게 사용할 수 있다는 의미는 아님.

---

## 64비트 시스템

64비트 주소를 모두 사용할 수 있다고 가정하면:

```text
2⁶⁴ bytes
```

* 약 16 EiB에 해당하는 매우 큰 이론적 주소 공간

```text
2⁶⁴ bytes
= 16 EiB
```

* 64비트 시스템에서는 포인터가 일반적으로 8바이트

```c
sizeof(void *) == 8
```

### 이론적 주소 공간과 실제 구현

* `2⁶⁴`는 64비트 주소로 표현 가능한 **이론적인 최대 범위**
* 실제 프로세서와 운영체제가 반드시 64개의 주소 비트를 전부 사용하는 것은 아님
* 따라서 실제 사용 가능한 가상 주소 공간은 구현에 따라 더 작을 수 있음

```text
64-bit pointer
     ↓
theoretical maximum
2⁶⁴ addresses

     ↓

actual CPU / OS implementation

     ↓
possibly smaller usable range
```

* 중요한 점은 64비트 환경이 32비트 환경보다 훨씬 넓은 주소 범위를 표현할 수 있다는 점

---

## 워드 크기와 포인터의 관계

CS:APP에서 워드 크기가 중요한 가장 직접적인 이유 중 하나는 **포인터 크기와 주소 공간의 크기를 결정하는 기준**이 된다는 점.

대표적인 환경에서는:

| 환경     |  포인터 크기 |   이론적 주소 공간 |
| ------ | ------: | ----------: |
| 32-bit | 4 bytes | `2³²` bytes |
| 64-bit | 8 bytes | `2⁶⁴` bytes |

예:

```c
int *ptr;
```

32비트 환경:

```text
ptr
↓
32-bit address
```

64비트 환경:

```text
ptr
↓
64-bit address
```

* 포인터가 가리키는 `int`의 크기와 포인터 자체의 크기는 별개의 개념

예:

```c
int value;
int *ptr = &value;
```

일반적인 64비트 Linux 환경에서는:

```text
sizeof(value) = 4
sizeof(ptr)   = 8
```

* `int *`가 8바이트라는 것은 `int`가 8바이트라는 의미가 아님
* 포인터에는 **대상의 값이 아니라 대상의 주소**가 저장됨

---

## 32비트 프로그램과 64비트 프로그램

* 동일한 컴퓨터가 항상 하나의 데이터 모델만 사용하는 것은 아님
* 컴파일 대상에 따라 서로 다른 프로그램 생성 가능

GCC에서는 대표적으로 다음 옵션 사용 가능.

```bash
gcc -m32 program.c
gcc -m64 program.c
```

```text
-m32
↓
32-bit target program

-m64
↓
64-bit target program
```

* 단, `-m32`를 사용하려면 해당 시스템에 32비트 컴파일 환경과 라이브러리가 설치되어 있어야 함

### 32비트 바이너리의 하위 호환성

* 많은 x86-64 시스템은 32비트 x86 프로그램 실행을 지원해 왔음
* 하지만 **모든 64비트 아키텍처와 운영체제가 32비트 프로그램 실행을 보장하는 것은 아님**
* 실제 실행 가능 여부는 다음 환경에 따라 결정

```text
CPU architecture
+
Operating System
+
Runtime / Libraries
```

* 따라서 `64-bit = 항상 32-bit 프로그램 실행 가능`으로 이해하면 부정확

---

## C 데이터 타입의 크기

C에서 데이터 타입의 크기는 모든 플랫폼에서 완전히 동일하게 고정되어 있지 않음.

대표적인 CS:APP 환경에서는 다음과 같은 크기 사용.

| C 데이터 타입    | 일반적인 32비트 환경 | 64비트 Linux/x86-64 |
| ----------- | -----------: | ----------------: |
| `char`      |            1 |                 1 |
| `short`     |            2 |                 2 |
| `int`       |            4 |                 4 |
| `long`      |            4 |                 8 |
| `long long` |            8 |                 8 |
| `char *`    |            4 |                 8 |
| `int *`     |            4 |                 8 |
| `float`     |            4 |                 4 |
| `double`    |            8 |                 8 |

단위:

```text
bytes
```

* signed와 unsigned 여부는 일반적으로 해당 정수 타입의 저장 크기를 바꾸지 않음

예:

```c
sizeof(int)
==
sizeof(unsigned int)
```

---

## `char`와 바이트

* C에서 `sizeof(char)`는 정의상 항상 `1`

```c
sizeof(char) == 1
```

* `sizeof`의 결과는 **바이트 단위**

따라서:

```c
sizeof(int) == 4
```

라면 일반적인 시스템에서:

```text
int
=
4 bytes
=
32 bits
```

* CS:APP에서 다루는 시스템은 일반적으로 1바이트를 8비트로 가정

```text
1 Byte = 8 Bits
```

---

## `long`의 크기가 중요한 이유

`long`은 플랫폼에 따라 크기가 달라질 수 있는 대표적인 타입.

### 32비트 환경

```text
long
=
4 bytes
```

### 64비트 Linux / Unix 계열의 LP64 모델

```text
long
=
8 bytes

pointer
=
8 bytes
```

이를 **LP64 데이터 모델**이라고 함.

```text
Long    → 64 bits
Pointer → 64 bits
```

반면 64비트 Windows에서는 일반적으로 **LLP64** 모델 사용.

```text
long
=
4 bytes

long long
=
8 bytes

pointer
=
8 bytes
```

따라서:

```text
64-bit system
≠
all integer types become 64 bits
```

---

## 데이터 모델

대표적인 C 데이터 모델을 정리하면 다음과 같음.

| 모델    | `int` | `long` | Pointer |
| ----- | ----: | -----: | ------: |
| ILP32 |    32 |     32 |      32 |
| LP64  |    32 |     64 |      64 |
| LLP64 |    32 |     32 |      64 |

단위:

```text
bits
```

### ILP32

```text
Int     → 32
Long    → 32
Pointer → 32
```

* 대표적인 32비트 환경

### LP64

```text
Long    → 64
Pointer → 64
```

* Linux, Unix 계열의 대표적인 64비트 모델

### LLP64

```text
Long Long → 64
Pointer   → 64
```

* 64비트 Windows에서 대표적으로 사용

---

## 데이터 타입 크기와 이식성

다음과 같이 타입의 크기를 임의로 가정하면 이식성 문제 발생 가능.

```c
long value;
```

그리고 다음을 항상 참이라고 가정:

```text
long = 8 bytes
```

* LP64 환경에서는 맞을 수 있음
* ILP32 또는 LLP64 환경에서는 틀릴 수 있음

따라서 바이너리 데이터 구조나 프로토콜처럼 **정확한 비트 수가 중요한 코드**에서는 타입 크기를 명확하게 고려해야 함.

---

## 고정폭 정수 타입 — Fixed-width Integer Types

C99의 `<stdint.h>`는 명시적인 비트 폭을 갖는 정수 타입 제공.

```c
#include <stdint.h>

int32_t value;
uint64_t count;
```

대표적인 타입:

```text
int8_t
int16_t
int32_t
int64_t

uint8_t
uint16_t
uint32_t
uint64_t
```

의미:

```text
int32_t
→ 정확히 32비트의 signed integer

uint64_t
→ 정확히 64비트의 unsigned integer
```

CS:APP에서 가정하는 일반적인 8비트 바이트 시스템에서는:

```text
int32_t  → 4 bytes
uint64_t → 8 bytes
```

### 주의점

* `int32_t`, `uint64_t` 등의 **exact-width type**은 해당 크기를 정확히 표현할 수 있는 정수 타입이 구현에 존재할 때 제공됨
* 따라서 모든 imaginable C 구현에서 무조건 존재한다고 보는 것은 엄밀하게는 부정확
* 현대의 일반적인 시스템에서는 거의 표준적으로 사용 가능

---

## 개발할 때 왜 중요한가

### 바이너리 데이터 형식

파일이나 네트워크 프로토콜에서는 필드의 크기가 정확하게 정해지는 경우가 많음.

예:

```text
Header

Version     8 bits
Length     32 bits
Timestamp  64 bits
```

이러한 구조를 다룰 때 단순히:

```c
long length;
```

처럼 작성하면 플랫폼별 크기 차이 발생 가능.

명확한 크기가 필요하다면:

```c
uint32_t length;
uint64_t timestamp;
```

같은 형태가 더 적합.

---

### 구조체의 크기와 메모리 사용량

포인터 크기가 달라지면 동일한 구조체라도 32비트와 64비트 환경에서 크기가 달라질 수 있음.

예:

```c
struct Node {
    int value;
    struct Node *next;
};
```

* `next` 포인터

```text
32-bit → 4 bytes
64-bit → 8 bytes
```

* 실제 구조체 크기는 이후 배우게 될 **정렬(Alignment)** 과 **패딩(Padding)** 의 영향도 받음

---

### 포인터와 주소를 다루는 코드

주소를 일반 정수 타입에 저장하는 코드는 타입 크기를 주의해야 함.

```c
int address;
```

* 64비트 포인터를 32비트 `int`에 저장할 수 없음

* 주소 전체를 표현하지 못해 값 손실 가능

* 따라서 포인터와 정수 사이의 변환이 필요한 경우 데이터 크기를 반드시 고려해야 함

---

## 이후 학습과의 연결

앞 절에서는 비트 패턴을 읽기 쉽게 나타내기 위한 16진수 표기법 학습.

```text
Bits
 ↓
Hexadecimal Notation
```

이번 절에서는 이러한 비트들이 **몇 개씩 묶여 하나의 데이터 타입과 주소를 구성하는지** 확인.

```text
Bits
 ↓
Bytes
 ↓
Data Types
 ↓
Pointers
 ↓
Virtual Addresses
```

이후에는 실제 여러 바이트의 객체가 메모리에 어떻게 배치되는지 학습.

```text
Data Sizes
    ↓
Byte Ordering
    ↓
Endianness
    ↓
Data Representation
```

* 특히 다음 절의 **바이트 순서(Byte Ordering)** 를 이해하려면 하나의 값이 여러 바이트로 구성된다는 사실이 중요

예:

```text
32-bit integer
=
4 bytes
```

* 이 4개의 바이트를 메모리에 어떤 순서로 배치할 것인지가 다음 학습 주제

---

## 정리

* 프로그램은 메모리를 바이트 단위로 주소 지정
* 주소들의 집합을 가상 주소 공간이라고 함
* 워드 크기 `w`는 포인터와 가상 주소 표현 범위에 밀접하게 관련
* 이론적으로 `w`비트 주소는 최대 `2ʷ`개의 바이트 주소 표현 가능
* 32비트 주소 공간은 이론적으로 `2³²`바이트
* 64비트 주소 공간은 이론적으로 `2⁶⁴`바이트
* 실제 CPU와 운영체제가 이 전체 주소 범위를 구현하는 것은 아님
* C 데이터 타입의 크기는 플랫폼과 데이터 모델에 따라 달라질 수 있음
* 특히 `long`과 포인터 크기의 차이에 주의 필요
* 정확한 비트 폭이 필요하다면 `<stdint.h>`의 고정폭 정수 타입 사용 가능
* 데이터 크기에 대한 이해는 이후 바이트 순서, 정수 표현, 포인터, 메모리 구조 학습의 기반

> **데이터 타입의 이름만으로 실제 크기를 가정하기보다, 해당 시스템의 데이터 모델과 각 객체가 차지하는 바이트 수를 함께 이해하는 것이 중요함.**
