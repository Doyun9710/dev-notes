# 1.9 중요한 주제들

> Important Themes

## 핵심 개념

* 컴퓨터 시스템은 하드웨어와 소프트웨어가 복잡하게 결합된 구조
* 이러한 시스템을 이해하고 성능을 향상하기 위해 몇 가지 핵심 원리가 반복적으로 사용됨
* CS:APP에서는 Chapter 1을 마무리하며 다음 세 가지 주제를 강조

  * 암달의 법칙(Amdahl's Law)
  * 동시성과 병렬성(Concurrency and Parallelism)
  * 추상화(Abstraction)

```text
Important Themes
      │
      ├─ Amdahl's Law
      │    └─ 어디를 개선해야 하는가?
      │
      ├─ Concurrency & Parallelism
      │    └─ 어떻게 더 많은 일을 동시에 처리하는가?
      │
      └─ Abstraction
           └─ 복잡성을 어떻게 감추고 관리하는가?
```

---

## 1. 암달의 법칙 — Amdahl's Law

* 시스템의 일부를 개선했을 때 **전체 시스템의 성능이 얼마나 향상되는지** 설명하는 원리
* 특정 부분을 크게 개선하더라도 해당 부분이 전체 실행 시간에서 차지하는 비율이 작으면 전체 성능 향상은 제한적

전체 실행 시간에서

* 개선 대상의 비율 → `a`
* 해당 부분의 성능 향상 배수 → `k`

라고 하면 전체 성능 향상 비율(Speedup)은 다음과 같음

$$
S = \frac{1}{(1-a)+\frac{a}{k}}
$$

### 예시

전체 실행 시간의 60%를 차지하는 부분을 3배 빠르게 만든다고 가정

```text
a = 0.6
k = 3
```

$$
S
=
\frac{1}
{(1-0.6)+\frac{0.6}{3}}
=
\frac{1}{0.6}
\approx 1.67
$$

* 개선한 부분은 3배 빨라졌지만 전체 프로그램은 약 1.67배만 빨라짐
* 개선되지 않은 나머지 실행 시간이 그대로 존재하기 때문

---

## 성능 향상의 한계

* 개선 대상의 속도를 무한히 높인다고 가정

```text
k → ∞
```

* 개선 대상의 실행 시간은 0에 가까워짐
* 하지만 개선되지 않은 `(1-a)` 부분은 그대로 남음

따라서 최대 성능 향상은

$$
S_{\max} = \frac{1}{1-a}
$$

예를 들어 전체 실행 시간의 50%만 개선 가능하다면

$$
S_{\max} = 2
$$

* 해당 부분을 아무리 빠르게 만들어도 전체 시스템은 최대 2배까지만 빨라질 수 있음

### 핵심 의미

* 성능 최적화에서는 **전체 실행 시간에서 큰 비중을 차지하는 부분을 개선하는 것이 중요**
* 작은 부분을 크게 개선하는 것보다 실제 병목을 찾아 개선하는 것이 효과적

```text
측정
 ↓
병목 발견
 ↓
개선
 ↓
다시 측정
```

> **전체 성능을 크게 향상하려면 전체 실행 시간에서 충분히 큰 비중을 차지하는 부분을 개선해야 함.**

---

## 2. 동시성과 병렬성

> Concurrency and Parallelism

* 여러 작업을 함께 처리하기 위한 시스템의 핵심 개념
* 서로 밀접하게 관련되어 있지만 의미는 다름

### 동시성 — Concurrency

* **여러 작업의 실행 구간이 시간적으로 겹쳐 진행되는 성질**
* 각 작업이 반드시 같은 순간에 실제로 실행될 필요는 없음
* 하나의 CPU 코어에서도 작업 사이를 빠르게 전환하여 동시성 구현 가능

```text
Time ─────────────────────→

Task A  ████      ████
Task B      ██████    ███
```

* 특정 순간에는 하나의 작업만 실행
* 전체 시간 구간에서는 여러 작업이 함께 진행

> **동시성은 여러 작업이 서로 겹치는 시간 동안 독립적으로 진행될 수 있는 성질**

---

### 병렬성 — Parallelism

* **둘 이상의 작업 또는 연산이 실제로 같은 시점에 실행되는 성질**
* 여러 CPU 코어 또는 여러 실행 장치와 같은 하드웨어 자원을 동시에 활용
* 주로 실행 시간 단축이나 처리량 증가를 목적으로 활용

```text
Core 1 → Task A ─────────→
Core 2 → Task B ─────────→
```

* `Task A`와 `Task B`가 같은 순간에 실제 실행

> **병렬성은 여러 작업이나 연산을 실제로 동시에 실행하여 계산 자원을 함께 활용하는 것**

---

## 동시성과 병렬성의 차이

| 구분       | 동시성              | 병렬성                |
| -------- | ---------------- | ------------------ |
| 핵심       | 여러 작업의 진행 구간이 겹침 | 여러 작업이 같은 순간 실제 실행 |
| 같은 순간 실행 | 필수 아님            | 필요                 |
| 단일 코어    | 가능               | 일반적인 스레드 실행에서는 제한적 |
| 멀티코어     | 가능               | 가능                 |
| 관점       | 작업의 구성과 진행       | 실제 실행과 하드웨어 활용     |

```text
Single Core
→ Concurrency 가능
→ Thread-Level Parallelism은 제한적

Multicore
→ Concurrency 가능
→ Parallelism 가능
```

* 번갈아 실행하는 것은 동시성을 구현하는 방법 중 하나
* 동시성이 존재한다고 해서 반드시 병렬성이 존재하는 것은 아님

---

## 병렬성이 중요해진 이유

* 과거에는 CPU의 클록 속도를 높이는 방식으로 성능 향상

* 지속적인 클록 속도 증가는 다음 문제에 직면

  * 전력 소비 증가
  * 발열 증가
  * 전력 밀도 문제

* 단일 연산 장치를 계속 빠르게 만드는 것만으로는 성능 향상에 한계

* 여러 작업과 연산을 동시에 수행하는 방향으로 발전

```text
Clock Frequency 중심
        ↓
성능 향상의 한계
        ↓
Parallelism 활용
```

---

## 3. 스레드 수준 병렬성

> Thread-Level Parallelism

* 여러 실행 흐름(Thread)을 병렬로 처리하는 방식
* 대표적인 기술

  * Multicore Processor
  * Simultaneous Multithreading(SMT)

---

### 멀티코어 프로세서 — Multicore Processor

* 하나의 프로세서 칩에 여러 CPU 코어를 포함하는 구조
* 각 코어가 독립적으로 명령어 실행 가능

```text
Processor
│
├─ Core 1 → Thread A
├─ Core 2 → Thread B
├─ Core 3 → Thread C
└─ Core 4 → Thread D
```

* 여러 코어가 서로 다른 작업을 실제로 동시에 실행 가능
* 각 코어는 일반적으로 독립적인 실행 상태와 일부 캐시 보유
* 더 높은 수준의 Cache와 Main Memory는 여러 코어가 공유하는 구조가 흔함
* 실제 캐시 구조는 프로세서 아키텍처에 따라 달라질 수 있음

### 핵심 의미

* 여러 작업을 동시에 처리하여 시스템 처리량 향상 가능
* 단일 프로그램의 성능을 높이려면 프로그램 자체도 병렬 실행이 가능하도록 구성될 필요

> **코어 수가 많다고 모든 프로그램이 자동으로 빨라지는 것은 아님.**

---

## 동시 멀티스레딩 — SMT

> Simultaneous Multithreading

* 하나의 물리적 CPU 코어가 여러 하드웨어 스레드를 처리할 수 있도록 하는 기술
* Intel에서는 **Hyper-Threading**이라는 이름으로 알려짐

```text
Physical Core
│
├─ Hardware Thread 1
└─ Hardware Thread 2
```

* 여러 스레드를 위해 일부 실행 상태를 별도로 유지

  * Program Counter
  * Register 상태

* CPU 내부의 여러 실행 자원은 공유

  * Execution Unit
  * Cache
  * Memory Interface 등

```text
Thread 1 State ─┐
                ├─ Shared Execution Resources
Thread 2 State ─┘
```

* 한 스레드가 활용하지 못하는 실행 자원을 다른 스레드가 사용할 기회 제공
* 결과적으로 하나의 코어 내부 자원 활용률 향상 가능

### Multicore와 SMT

| 구분    | Multicore         | SMT                              |
| ----- | ----------------- | -------------------------------- |
| 구조    | 물리적 Core를 여러 개 구성 | 하나의 Core에서 여러 Hardware Thread 지원 |
| 실행 자원 | Core별로 상당 부분 독립   | 상당 부분 공유                         |
| 목적    | 병렬 실행 능력 증가       | Core 내부 자원 활용률 향상                |

---

## 4. 명령어 수준 병렬성

> Instruction-Level Parallelism, ILP

* 하나의 Thread 내부에서도 여러 기계어 명령어의 실행을 겹치거나 동시에 처리하는 방식
* 대표적인 기술

  * Pipelining
  * Superscalar Execution

---

### 파이프라이닝 — Pipelining

* 명령어 실행 과정을 여러 단계로 나누고 서로 다른 명령어의 단계를 겹쳐 수행

```text
          Time →

Inst A   Fetch Decode Execute
Inst B         Fetch Decode Execute
Inst C               Fetch Decode Execute
```

* 하나의 명령어가 완전히 종료될 때까지 기다렸다가 다음 명령어를 시작하지 않음
* 여러 명령어의 서로 다른 처리 단계를 동시에 수행하여 전체 처리량 증가

---

### 초스칼라 — Superscalar

* 여러 실행 장치를 이용하여 한 시점에 여러 명령어를 처리할 수 있는 프로세서 구조
* 서로 의존성이 없는 명령어를 병렬로 실행 가능

```text
Instruction 1 → ALU
Instruction 2 → ALU
Instruction 3 → Load Unit
```

* 현대 프로세서는 Pipelining과 여러 실행 장치를 함께 활용하여 높은 Instruction-Level Parallelism 제공

---

## 5. SIMD 병렬성

> Single-Instruction, Multiple-Data

* **하나의 명령어로 여러 데이터에 동일한 연산을 동시에 수행하는 방식**
* 데이터 수준 병렬성(Data-Level Parallelism)을 활용

일반적인 방식:

```text
a1 + b1
a2 + b2
a3 + b3
a4 + b4
```

SIMD:

```text
[a1 a2 a3 a4]
       +
[b1 b2 b3 b4]
       ↓
 SIMD Instruction
       ↓
[c1 c2 c3 c4]
```

### 활용하기 좋은 작업

* 이미지 처리

* 영상 처리

* 음성 처리

* 행렬 및 벡터 연산

* 과학 계산

* 동일한 연산을 많은 데이터에 반복하는 작업에 효과적

---

## 병렬성의 수준

```text
Parallelism
    │
    ├─ Thread-Level
    │    ├─ Multicore
    │    └─ SMT
    │
    ├─ Instruction-Level
    │    ├─ Pipelining
    │    └─ Superscalar
    │
    └─ Data-Level
         └─ SIMD
```

| 수준                | 병렬화 대상     | 대표 기술                   |
| ----------------- | ---------- | ----------------------- |
| Thread Level      | 여러 실행 흐름   | Multicore, SMT          |
| Instruction Level | 여러 기계어 명령어 | Pipelining, Superscalar |
| Data Level        | 여러 데이터     | SIMD                    |

---

## 6. 컴퓨터 시스템에서 추상화의 중요성

> The Importance of Abstractions in Computer Systems

* **추상화(Abstraction)** 는 복잡한 내부 구현을 감추고 사용자가 필요한 기능을 단순한 인터페이스로 제공하는 개념
* 컴퓨터 과학 전반에서 복잡성을 관리하기 위한 핵심 원리

예를 들어 함수 라이브러리를 사용할 때

```c
printf("Hello\n");
```

* 프로그래머가 `printf` 내부 구현을 모두 이해할 필요 없음
* 정의된 인터페이스와 사용 방법만 알면 기능 사용 가능

```text
복잡한 내부 구현
       ↓
   Abstraction
       ↓
단순한 Interface
```

---

## 추상화가 필요한 이유

### 복잡성 관리

* 현대 컴퓨터 시스템의 모든 하드웨어 동작을 프로그래머가 직접 다루는 것은 현실적으로 불가능
* 하위 계층의 복잡한 세부 구현을 감추고 필요한 기능만 노출

### 계층 간 독립성

* 동일한 추상화 인터페이스를 유지하면 내부 구현을 변경하더라도 상위 계층에 미치는 영향 감소
* 서로 다른 구현이 동일한 인터페이스 제공 가능

### 개발 생산성

* 프로그래머는 현재 해결하려는 문제에 필요한 수준에 집중 가능
* 모든 하위 계층을 동시에 이해할 필요 감소

---

## ISA — Instruction Set Architecture

* **실제 프로세서 하드웨어를 추상화한 인터페이스**
* 소프트웨어와 프로세서 사이에서 사용 가능한 명령어와 동작 규칙 정의

```text
Machine Code
     ↓
    ISA
     ↓
Processor Hardware
```

* 프로그램은 프로세서 내부의 실제 구현 방식을 알 필요 없음
* 동일한 ISA를 구현하는 프로세서라면 내부 구조가 달라도 같은 기계어 프로그램 실행 가능

예:

```text
Machine Code
     ↓
   x86-64 ISA
     ↓
┌───────────────┐
│ Processor A   │
│ Processor B   │
│ Processor C   │
└───────────────┘
```

* 실제 프로세서는 여러 명령어를 병렬로 처리하는 등 매우 복잡하게 동작
* 하지만 소프트웨어에서는 ISA가 제공하는 일관된 실행 모델을 기준으로 프로그램 작성 가능

---

## 운영체제가 제공하는 추상화

앞서 1.7에서 학습한 운영체제의 주요 추상화도 같은 원리

### 프로세스 — Process

* **실행 중인 프로그램에 대한 추상화**
* 프로그램이 CPU와 시스템 자원을 사용하는 복잡한 과정을 감춤

### 가상 메모리 — Virtual Memory

* **프로그램 메모리에 대한 추상화**
* 각 프로세스가 자신만의 일관된 가상 주소 공간을 사용하는 것처럼 보이게 함

### 파일 — File

* **I/O 장치에 대한 추상화**
* 다양한 I/O 데이터를 바이트의 연속이라는 공통된 형태로 다룰 수 있게 함

```text
Operating System
      │
      ├─ Process
      ├─ Virtual Memory
      └─ File
```

---

## 가상 머신 — Virtual Machine

* **컴퓨터 시스템 전체에 대한 추상화**
* 운영체제, 프로세서, 프로그램 등을 포함한 하나의 컴퓨터 환경을 추상화

```text
Virtual Machine
      ↓
Computer System
 ├─ Operating System
 ├─ Processor
 └─ Programs
```

* 하나의 물리 시스템 위에서 서로 다른 독립적인 컴퓨터 환경을 구성하는 기반
* 서로 다른 운영체제나 동일 운영체제의 서로 다른 환경을 하나의 물리 머신에서 실행 가능

---

## 시스템의 추상화 관계

추상화를 단순한 일렬 계층으로 보기보다 **각 시스템 구성 요소의 복잡성을 다른 추상화가 감추는 관계**로 이해하는 것이 적절

```text
                 Virtual Machine
                       │
           ┌───────────┼───────────┐
           │           │           │
        Process       ISA      Virtual Memory
           │           │           │
           │       Processor       │
           │                       │
           └──── System Resources ─┘

Files
  ↓
I/O Devices
```

보다 간단하게 정리하면 다음과 같음

| 추상화             | 감추는 대상                |
| --------------- | --------------------- |
| ISA             | 실제 프로세서 하드웨어          |
| Process         | 실행 중인 프로그램과 시스템 자원 관리 |
| Virtual Memory  | 프로그램의 메모리             |
| File            | I/O 장치                |
| Virtual Machine | 컴퓨터 시스템 전체            |

---

## 개발할 때 왜 중요한가

### 성능 최적화에서는 병목을 먼저 찾아야 함

* 암달의 법칙에 따라 작은 부분을 아무리 최적화해도 전체 성능 향상은 제한적
* 실제 실행 시간에서 큰 비중을 차지하는 영역을 측정하고 개선할 필요

---

### 병렬 하드웨어가 있다고 자동으로 빨라지는 것은 아님

* 여러 코어가 있어도 작업을 독립적으로 나눌 수 없다면 병렬성 활용 제한
* 작업 사이의 의존 관계와 동기화 비용 고려 필요

```text
독립적인 작업
→ 병렬화하기 쉬움

강한 의존 관계
→ 병렬화하기 어려움
```

---

### 추상화의 경계를 이해하면 문제를 더 쉽게 추적 가능

* 평소에는 추상화를 통해 하위 구현을 몰라도 프로그램 작성 가능
* 하지만 문제가 발생하면 추상화 아래 계층을 이해하는 것이 원인 분석에 도움

예:

```text
Application Problem
       ↓
System Call
       ↓
Operating System
       ↓
Hardware
```

* CS:APP를 학습하는 목적도 이러한 추상화 아래에서 실제 시스템이 어떻게 동작하는지 이해하는 것과 연결

---

## 정리

* CS:APP 1.9에서는 컴퓨터 시스템 전반에서 반복적으로 등장하는 세 가지 중요한 주제를 소개

### Amdahl's Law

* 시스템의 일부를 개선했을 때 전체 성능 향상에는 한계 존재
* 큰 성능 향상을 위해서는 전체 실행 시간에서 큰 비중을 차지하는 부분 개선 필요

### Concurrency and Parallelism

* 동시성

  * 여러 작업의 실행 구간이 시간적으로 겹쳐 진행되는 성질

* 병렬성

  * 여러 작업이나 연산이 실제로 같은 순간 실행되는 성질

* 현대 프로세서는 여러 수준에서 병렬성 활용

```text
Thread-Level
Instruction-Level
Data-Level (SIMD)
```

### Abstraction

* 복잡한 내부 구현을 감추고 단순하고 일관된 인터페이스 제공
* 대표적인 시스템 추상화

  * ISA
  * Process
  * Virtual Memory
  * File
  * Virtual Machine

```text
복잡한 Computer System
          ↓
      Abstraction
          ↓
단순하고 일관된 Interface
```

> **컴퓨터 시스템은 병렬성을 이용해 더 많은 일을 빠르게 처리하고, 추상화를 이용해 복잡한 구현을 관리하며, 암달의 법칙을 통해 어디를 개선해야 전체 성능에 의미 있는 영향을 줄 수 있는지 판단함.**
