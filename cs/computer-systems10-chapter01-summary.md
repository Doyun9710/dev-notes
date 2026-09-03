# Chapter 1. 컴퓨터 시스템으로의 여행

> A Tour of Computer Systems

## 핵심 개념

* 컴퓨터 시스템은 프로그램을 실행하기 위해 하드웨어와 소프트웨어의 여러 계층이 함께 동작하는 구조
* Chapter 1에서는 간단한 `hello.c` 프로그램을 따라가며 컴퓨터 시스템 전체를 조망
* 핵심 흐름은 다음과 같음

```text
Source Code
    ↓
Compilation System
    ↓
Executable
    ↓
Operating System
    ↓
Main Memory
    ↓
Processor
    ↓
I/O Device / Network
```

* 프로그램 실행 과정에서는 다음 핵심 개념이 서로 연결됨

  * Bits and Context
  * Compilation System
  * Processor
  * Memory
  * Cache
  * Memory Hierarchy
  * Operating System
  * Network
  * Concurrency and Parallelism
  * Abstraction

---

# 1.1 정보는 비트와 컨텍스트로 해석됨

> Information Is Bits + Context

## 모든 정보는 비트로 표현됨

* 컴퓨터 시스템에서 다루는 모든 정보는 결국 `0`과 `1`의 연속인 **비트(Bit)** 로 표현
* 일반적인 현대 시스템에서는 8개의 비트를 하나의 **바이트(Byte)** 로 묶어서 사용

대표적인 정보:

* 소스 코드
* 문자열
* 정수
* 실수
* 이미지
* 기계어
* 실행 파일
* 네트워크 데이터

---

## 문자 역시 숫자로 저장됨

* `hello.c`와 같은 텍스트 파일도 내부적으로는 바이트들의 연속
* 문자는 문자 인코딩 규칙에 따라 정수 값으로 변환되어 저장

ASCII 예:

```text
h → 104
e → 101
l → 108
l → 108
o → 111
```

```text
문자
 ↓
정수 값
 ↓
Byte
 ↓
Bits
```

---

## 비트 자체에는 의미가 없음

예를 들어 다음 비트 패턴이 있다고 가정

```text
01000001
```

컨텍스트에 따라 다음과 같이 해석 가능

* 정수 → `65`

* ASCII 문자 → `'A'`

* 기계어 명령어의 일부 → 다른 의미

* 따라서 비트 자체만으로는 데이터의 의미를 결정할 수 없음

* **어떤 프로그램이 어떤 방식으로 해석하는지가 의미를 결정**

> **정보를 이해하려면 비트뿐 아니라 그 비트를 해석하는 컨텍스트까지 함께 봐야 함.**

---

# 1.2 프로그램은 여러 단계를 거쳐 실행 파일로 변환됨

> Programs Are Translated by Other Programs into Different Forms

## 컴파일 시스템

* 사람이 작성한 C 코드는 CPU가 직접 실행할 수 없음
* 여러 프로그램을 거쳐 CPU가 실행할 수 있는 기계어 형태로 변환
* 이러한 변환 과정 전체를 **컴파일 시스템(Compilation System)** 이라고 함

```text
hello.c
   ↓ Preprocessor
hello.i
   ↓ Compiler
hello.s
   ↓ Assembler
hello.o
   ↓ Linker
hello
```

---

## 1. 전처리 — Preprocessing

* 담당 프로그램: `cpp`
* `#`으로 시작하는 전처리 지시문 처리

예:

```c
#include <stdio.h>
#define MAX_SIZE 100
```

* 헤더 포함
* 매크로 치환
* 조건부 컴파일

결과:

```text
hello.c
   ↓
hello.i
```

* `hello.i`

  * 전처리가 완료된 C 코드
  * Text 파일

---

## 2. 컴파일 — Compilation

* 담당 프로그램: `cc1`
* 전처리된 C 코드를 대상 CPU 아키텍처에 맞는 어셈블리 코드로 변환

```text
hello.i
   ↓
hello.s
```

* `hello.s`

  * Assembly Language
  * 사람이 읽을 수 있는 저수준 Text 코드

---

## 3. 어셈블 — Assembly

* 담당 프로그램: `as`
* Assembly Code를 Machine Code로 변환

```text
hello.s
   ↓
hello.o
```

* `hello.o`

  * Binary 파일
  * **Relocatable Object File**
  * 기계어를 포함하지만 아직 최종 실행 파일은 아님

---

## 4. 링크 — Linking

* 담당 프로그램: `ld`
* 여러 Object File과 Library의 참조 관계를 해결하고 최종 실행 파일 생성

주요 역할:

* Symbol Resolution
* Relocation

```text
Object Files
     +
Libraries
     ↓
Symbol Resolution
     ↓
Relocation
     ↓
Executable
```

결과:

```text
hello
```

* 운영체제가 로드하여 실행할 수 있는 Executable File

---

# 1.3 컴파일 시스템을 이해해야 하는 이유

> It Pays to Understand How Compilation Systems Work

## 프로그램 성능

* C 코드의 형태에 따라 생성되는 기계어와 실행 비용이 달라질 수 있음
* 다음 내용을 이해하는 기반

  * 함수 호출
  * 반복문
  * 조건 분기
  * 메모리 접근
  * 컴파일러 최적화

---

## 링크 오류

대표적인 오류:

```text
undefined reference to ...
multiple definition of ...
```

* 컴파일 자체는 성공해도 Linking 단계에서 실행 파일 생성 실패 가능
* Object File, Symbol, Library 사이의 관계 이해 필요

---

## 보안

* 일부 보안 취약점은 프로그램의 Machine-Level 동작과 밀접하게 관련

대표적인 예:

* Buffer Overflow

* Stack 관련 취약점

* 잘못된 메모리 접근

* 이후 Machine-Level Programming 학습의 기반

---

# 1.4 프로세서는 메모리에 저장된 명령어를 읽고 실행함

> Processors Read and Interpret Instructions Stored in Memory

## 주요 하드웨어 구성 요소

컴퓨터 시스템의 주요 구성 요소:

* Bus
* I/O Device
* Main Memory
* Processor

```text
I/O Devices
     ↕
    Bus
     ↕
Main Memory
     ↕
 Processor
```

---

## 버스 — Bus

* 시스템 구성 요소 사이에서 데이터와 제어 정보를 전달하는 통로
* CPU, 메모리, I/O 장치 등을 연결
* 시스템은 일정 크기의 데이터 단위를 버스를 통해 전송

---

## I/O 장치 — I/O Devices

* 외부 환경과 컴퓨터를 연결하는 장치

대표적인 예:

* Keyboard

* Mouse

* Display

* Disk

* Network Adapter

* Controller 또는 Adapter를 통해 시스템과 연결

---

## 메인 메모리 — Main Memory

* 실행 중인 프로그램의 코드와 데이터를 저장하는 공간
* 물리적으로 주로 DRAM 기반
* 논리적으로 연속된 바이트 배열로 이해 가능

---

## 프로세서 — Processor

* 메인 메모리에 저장된 기계어 명령어를 실행하는 장치

주요 구성 요소:

### Program Counter

* 다음에 실행할 명령어의 주소를 가리키는 레지스터

### Register File

* 연산에 필요한 데이터와 중간 결과를 저장하는 고속 저장 공간

### ALU

* 산술 및 논리 연산 수행

---

## 명령어 실행 과정

개념적으로 CPU는 다음 과정을 반복

```text
Fetch
 ↓
Decode
 ↓
Execute
 ↓
PC Update
```

* Fetch

  * PC가 가리키는 명령어 읽기
* Decode

  * 명령어 해석
* Execute

  * 실제 연산 수행
* PC Update

  * 다음 실행 위치 결정

---

## `hello` 실행

```bash
./hello
```

입력 후 단순화한 실행 과정:

```text
Keyboard
   ↓
Shell
   ↓
Disk
   ↓
Main Memory
   ↓
Processor
   ↓
Display
```

* 사용자의 명령어를 Shell이 읽음
* 실행 파일을 Disk에서 Main Memory로 로드
* CPU가 메모리에 있는 명령어 실행
* 프로그램의 출력 결과를 I/O 장치로 전달

> **프로그램 실행은 CPU의 계산뿐 아니라 시스템 내부에서 코드와 데이터가 계속 이동하는 과정.**

---

# 1.5 캐시가 중요함

> Caches Matter

## 데이터 이동 비용

* CPU와 메인 메모리 사이에는 큰 속도 차이가 존재
* CPU가 필요한 데이터를 기다리면 전체 실행 성능 저하

```text
CPU
 ↓
데이터 필요
 ↓
Main Memory 접근
 ↓
대기
```

* 프로그램 성능에는 계산 비용뿐 아니라 **데이터 이동 비용**도 중요

---

## 캐시 메모리 — Cache Memory

* CPU와 메인 메모리 사이의 속도 차이를 줄이기 위한 작고 빠른 저장 공간
* 일반적으로 SRAM 기반

```text
CPU
 ↓
Cache
 ↓
Main Memory
```

* Cache

  * 빠름
  * 작음
  * 비쌈

* Main Memory

  * 상대적으로 느림
  * 큼
  * 저렴함

---

## 지역성 — Locality

* 프로그램이 최근 사용한 데이터나 주변 데이터를 다시 사용하는 경향

### Temporal Locality

* 최근 참조한 데이터가 가까운 미래에 다시 사용될 가능성이 높은 특성

### Spatial Locality

* 특정 주소 주변의 데이터가 가까운 미래에 함께 사용될 가능성이 높은 특성

* 캐시는 이러한 지역성을 활용하여 적은 용량으로 높은 효과 제공

> **캐시는 자주 사용할 가능성이 높은 데이터를 CPU 가까이에 유지하여 느린 메모리 접근을 줄임.**

---

# 1.6 저장장치는 계층 구조를 이룸

> Storage Devices Form a Hierarchy

## 메모리 계층 구조

```text
        빠름 / 작음 / 비쌈
               ↑

L0     Registers
L1     L1 Cache
L2     L2 Cache
L3     L3 Cache
L4     Main Memory
L5     Local Storage
L6     Remote Storage

               ↓
        느림 / 큼 / 저렴
```

상위 계층일수록 일반적으로:

* 빠름
* 작음
* CPU와 가까움
* 비트당 비용이 높음

하위 계층일수록:

* 느림
* 큼
* CPU와 멂
* 비트당 비용이 낮음

---

## 계층 사이의 캐싱

* 상위 계층은 바로 아래 계층에 있는 데이터 일부를 저장하는 **Cache 역할** 수행

```text
Registers
   ↑
L1 Cache
   ↑
L2 Cache
   ↑
L3 Cache
   ↑
Main Memory
   ↑
Local Storage
```

* 자주 사용하는 데이터를 CPU 가까이에 유지하는 것이 핵심

> **Memory Hierarchy는 작고 빠른 저장장치와 크고 느린 저장장치를 조합하여 성능·용량·비용 사이의 균형을 만드는 구조.**

---

# 1.7 운영체제는 하드웨어를 관리함

> The Operating System Manages the Hardware

## 운영체제의 역할

* 응용 프로그램과 하드웨어 사이에 위치하는 소프트웨어 계층

```text
Application
    ↓
Operating System
    ↓
Hardware
```

주요 목적:

* 프로그램으로부터 시스템 자원 보호
* 복잡한 하드웨어에 대한 단순하고 일관된 추상화 제공

---

## 프로세스 — Process

* **실행 중인 프로그램에 대한 추상화**
* 각 프로그램이 시스템 자원을 독립적으로 사용하는 것처럼 보이게 함

### Context Switch

* CPU가 한 프로세스에서 다른 프로세스로 실행 대상을 변경하는 과정
* 현재 실행 상태 저장 후 다른 프로세스 상태 복원

---

## 쓰레드 — Thread

* 프로세스 내부의 실행 흐름
* 하나의 프로세스에 여러 Thread 존재 가능

공유하는 자원:

* Code
* Global Data
* Heap
* 일부 프로세스 자원

각 Thread가 독립적으로 가지는 주요 상태:

* Program Counter
* Register
* Stack

---

## 가상 메모리 — Virtual Memory

* 각 프로세스에 독립적인 주소 공간이 있는 것처럼 제공하는 추상화

일반적인 가상 주소 공간:

```text
높은 주소
┌──────────────────┐
│ Kernel Memory    │
├──────────────────┤
│ Stack       ↓    │
├──────────────────┤
│ Shared Libraries │
├──────────────────┤
│ Heap        ↑    │
├──────────────────┤
│ Data             │
├──────────────────┤
│ Code             │
└──────────────────┘
낮은 주소
```

---

## 파일 — File

* I/O 데이터를 **바이트의 연속(Sequence of Bytes)** 으로 다루는 추상화
* 다양한 장치의 세부 동작을 직접 다루지 않고 일관된 인터페이스 사용 가능

---

# 1.8 시스템은 네트워크를 통해 다른 시스템과 통신함

> Systems Communicate with Other Systems Using Networks

## 네트워크도 I/O 장치

* 하나의 컴퓨터 시스템 관점에서 Network Adapter도 I/O 장치
* 메모리와 네트워크 사이에서 데이터를 전달

```text
Main Memory
     ↕
Network Adapter
     ↕
   Network
     ↕
Remote System
```

---

## Client와 Server

```text
Client
  ↓ Request
Network
  ↓
Server
  ↓ Response
Network
  ↓
Client
```

* 클라이언트가 요청 전송
* 서버가 요청 수신 및 처리
* 서버가 결과 전송
* 클라이언트가 응답 수신

---

## Socket

* 운영체제가 제공하는 네트워크 통신 인터페이스
* Unix 계열에서는 파일 I/O와 유사한 읽기·쓰기 모델 활용 가능

```text
Application
    ↓
Socket
    ↓
Operating System
    ↓
Network Adapter
```

* 네트워크는 프로그램의 데이터 이동 범위를 다른 시스템까지 확장

---

# 1.9 중요한 주제들

> Important Themes

Chapter 1을 마무리하며 컴퓨터 시스템 전반에서 반복적으로 등장하는 핵심 원리 소개

* Amdahl's Law
* Concurrency and Parallelism
* Abstraction

---

## 1.9.1 암달의 법칙 — Amdahl's Law

전체 실행 시간에서

* 개선 대상의 비율 → `a`
* 개선 대상의 성능 향상 → `k`

전체 Speedup:

$$
S = \frac{1}{(1-a)+\frac{a}{k}}
$$

* 특정 부분을 아무리 빠르게 만들어도 개선되지 않은 부분이 남아 있으면 전체 성능 향상 제한

최대 Speedup:

$$
S_{\max} = \frac{1}{1-a}
$$

### 핵심 의미

* 작은 부분을 크게 개선하는 것보다 전체 실행 시간에서 큰 비중을 차지하는 병목을 개선하는 것이 중요

```text
측정
 ↓
병목 발견
 ↓
최적화
 ↓
재측정
```

---

# 1.9.2 동시성과 병렬성

> Concurrency and Parallelism

## 동시성 — Concurrency

* **여러 작업의 실행 구간이 시간적으로 겹쳐 진행되는 성질**
* 반드시 같은 순간에 실제 실행될 필요는 없음
* 단일 CPU에서도 작업 사이를 빠르게 전환하여 구현 가능

```text
Time ─────────────────────→

Task A  ████      ████
Task B      ██████    ███
```

---

## 병렬성 — Parallelism

* **둘 이상의 작업이나 연산이 실제로 같은 시점에 실행되는 성질**
* 여러 Core 또는 실행 장치를 동시에 활용

```text
Core 1 → Task A ─────────→
Core 2 → Task B ─────────→
```

* 동시성이 존재한다고 반드시 병렬성이 존재하는 것은 아님

---

## 스레드 수준 병렬성

> Thread-Level Parallelism

### Multicore

* 하나의 CPU 칩에 여러 물리 Core 구성
* 서로 다른 Thread를 실제로 병렬 실행 가능

### SMT

> Simultaneous Multithreading

* 하나의 물리 Core에서 여러 Hardware Thread 지원
* Intel의 대표적인 구현 명칭이 Hyper-Threading
* 일부 실행 상태는 분리하고 실행 자원의 상당 부분은 공유
* Core 내부 자원 활용률 향상이 목적

---

## 명령어 수준 병렬성

> Instruction-Level Parallelism

### Pipelining

* 여러 Instruction의 서로 다른 실행 단계를 겹쳐 수행

```text
          Time →

Inst A   Fetch Decode Execute
Inst B         Fetch Decode Execute
Inst C               Fetch Decode Execute
```

### Superscalar

* 여러 실행 장치를 사용하여 여러 독립적인 명령어를 동시에 처리

---

## SIMD 병렬성

> Single-Instruction, Multiple-Data

* 하나의 명령어로 여러 데이터에 동일한 연산 수행

```text
[a1 a2 a3 a4]
       +
[b1 b2 b3 b4]
       ↓
SIMD Instruction
       ↓
[c1 c2 c3 c4]
```

대표적인 활용:

* 이미지
* 영상
* 행렬
* 벡터
* 과학 계산

---

# 1.9.3 컴퓨터 시스템에서 추상화의 중요성

> The Importance of Abstractions in Computer Systems

## 추상화 — Abstraction

* 복잡한 내부 구현을 감추고 필요한 기능을 단순하고 일관된 인터페이스로 제공하는 개념
* 컴퓨터 시스템의 복잡성을 관리하는 핵심 방법

```text
Complex Implementation
        ↓
   Abstraction
        ↓
Simple Interface
```

---

## 대표적인 시스템 추상화

| 추상화             | 감추는 주요 대상          |
| --------------- | ------------------ |
| ISA             | 프로세서 하드웨어의 세부 구현   |
| Process         | 프로그램 실행과 시스템 자원 관리 |
| Virtual Memory  | 물리 메모리 구조와 관리      |
| File            | I/O 장치의 세부 동작      |
| Virtual Machine | 컴퓨터 시스템 전체         |

---

## ISA — Instruction Set Architecture

* 소프트웨어와 프로세서 사이의 중요한 인터페이스
* 프로세서가 제공하는 명령어와 동작 규칙 정의
* 동일한 ISA를 구현한 프로세서는 내부 구조가 달라도 같은 기계어 프로그램 실행 가능

```text
Software
   ↓
  ISA
   ↓
Processor Hardware
```

---

## 추상화의 의미

* 상위 계층은 하위 계층의 모든 세부 구현을 알 필요 없음
* 내부 구현이 변경되어도 동일한 추상화 인터페이스를 유지할 수 있음
* 프로그래머가 해결하려는 문제에 필요한 수준에 집중 가능

> **추상화는 복잡성을 없애는 것이 아니라, 복잡성을 필요한 경계 뒤에 감추는 방법.**

---

# Chapter 1 전체 흐름

`hello.c`라는 하나의 프로그램에서 출발하면 Chapter 1의 모든 내용이 하나의 흐름으로 연결됨

```text
hello.c
   │
   │ Bits + Context
   ▼
Source Code
   │
   │ Compilation System
   ▼
Executable
   │
   │ Operating System
   ▼
Main Memory
   │
   │ Cache / Memory Hierarchy
   ▼
Processor
   │
   │ Instruction Execution
   ▼
Program Running
   │
   ├─ I/O
   └─ Network
```

그리고 시스템 전체를 바라볼 때 다음 원리가 반복적으로 등장

```text
Performance
   └─ Amdahl's Law

Execution
   └─ Concurrency / Parallelism

Complexity
   └─ Abstraction
```

---

# 개발할 때 왜 중요한가

## 소스 코드 아래의 동작을 이해할 수 있음

* 개발자는 보통 고수준 언어와 Framework를 사용
* 하지만 실제 프로그램은 결국 다음 과정을 통해 실행

```text
Source Code
 ↓
Machine Code
 ↓
CPU
 ↓
Memory
 ↓
I/O
```

* 문제가 발생했을 때 어느 계층에서 발생했는지 추적하는 기반

---

## 성능 문제를 더 넓게 볼 수 있음

* 프로그램 성능은 CPU 연산 횟수만으로 결정되지 않음
* 다음 요소가 모두 영향을 줄 수 있음

  * Compiler가 생성한 코드
  * Memory Access
  * Cache
  * I/O
  * Network
  * Parallelism

```text
Program Performance
       │
       ├─ Computation
       ├─ Memory
       ├─ Storage
       ├─ I/O
       └─ Network
```

---

## 추상화 아래를 볼 수 있게 됨

* 평소에는 OS, Compiler, Runtime 등이 복잡한 시스템 동작을 감춰줌
* 대부분의 개발에서는 이러한 추상화를 그대로 사용하는 것이 적절
* 하지만 성능 문제, 메모리 오류, 링크 오류, 시스템 장애 등이 발생하면 추상화 아래의 동작 이해가 문제 해결에 도움

---

# 정리

* 컴퓨터의 모든 정보는 비트로 표현되며 컨텍스트가 의미를 결정
* C 프로그램은 전처리, 컴파일, 어셈블, 링크 과정을 거쳐 실행 파일로 변환
* 실행 파일은 운영체제를 통해 메모리에 적재
* CPU는 메모리의 기계어 명령어를 읽고 실행
* 프로그램 실행 중에는 코드와 데이터가 여러 저장장치 사이를 계속 이동
* CPU와 메모리의 속도 차이를 줄이기 위해 Cache 사용
* 저장장치는 속도·용량·비용에 따라 Memory Hierarchy 구성
* 운영체제는 Process, Virtual Memory, File 등의 추상화를 통해 하드웨어 관리
* 네트워크는 시스템 관점에서 또 하나의 I/O 장치이며 다른 시스템까지 데이터 이동 범위를 확장
* 현대 프로세서는 Thread, Instruction, Data 수준에서 병렬성 활용
* Amdahl's Law는 부분 최적화가 전체 성능에 미치는 한계를 설명
* Abstraction은 복잡한 시스템을 계층적으로 이해하고 사용할 수 있게 하는 핵심 원리

```text
Bits
 ↓
Programs
 ↓
Compiler
 ↓
Machine Code
 ↓
Processor
 ↕
Memory Hierarchy
 ↕
Operating System
 ↕
I/O / Network

      +
Performance
Parallelism
Abstraction
```

> **Chapter 1의 핵심은 개별 용어를 암기하는 것이 아니라, 내가 작성한 프로그램이 소스 코드에서 시작하여 컴파일되고, 메모리에 적재되고, CPU에서 실행되며, 운영체제와 I/O를 통해 외부 세계와 상호작용하는 전체 흐름을 하나의 시스템으로 이해하는 것.**
