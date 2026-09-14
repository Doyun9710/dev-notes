# 2.1.5 프로그램 코드도 바이트의 연속으로 표현됨

> Representing Code

## 핵심 개념

* C로 작성한 소스 코드는 CPU가 직접 실행하는 형태가 아님
* 컴파일 과정을 거쳐 대상 머신의 **기계어 코드(Machine Code)** 로 변환
* 기계 수준에서 프로그램 코드 역시 결국 **바이트의 연속**

```text
C Source Code
      ↓
Compilation System
      ↓
Machine Code
      ↓
Byte Sequence
```

* 중요한 점은 기계어 코드의 바이트 표현이 C 소스 자체에 의해 고정되는 것이 아니라는 것

```text
Source Code
    +
Target Architecture
    +
Compiler / Environment
    ↓
Machine Code
```

* 동일한 C 코드라도 실행 대상 시스템에 따라 서로 다른 기계어 생성 가능

---

## 기계어는 명령어를 인코딩한 바이트열

CPU는 **명령어 집합 아키텍처(ISA, Instruction Set Architecture)** 에 정의된 규칙에 따라 기계어 바이트를 해석.

기계어에는 다음과 같은 정보가 인코딩될 수 있음.

```text
Operation
+
Operands
+
Registers
+
Addressing Information
```

예를 들어 소스 코드의:

```c
return x + y;
```

라는 하나의 표현도 기계 수준에서는 다음과 같은 여러 동작으로 변환될 수 있음.

```text
operand 읽기
    ↓
덧셈 수행
    ↓
결과 저장
    ↓
함수 복귀
```

### 바이트 하나와 명령어 하나가 항상 대응하지는 않음

특히 x86 계열에서는 명령어 길이가 가변적임.

```text
Machine Code

55
48 89 e5
89 7d fc
...
```

* 어떤 명령어는 1바이트
* 어떤 명령어는 여러 바이트
* opcode뿐 아니라 operand와 addressing 정보 등이 함께 인코딩될 수 있음

따라서:

```text
1 Byte = 1 Instruction
```

으로 이해하면 부정확.

보다 정확하게는:

> **하나 이상의 바이트로 하나의 기계어 명령어가 인코딩될 수 있음.**

---

## 동일한 C 함수의 서로 다른 기계어 표현

CS:APP에서는 다음 함수를 여러 시스템에서 컴파일하여 비교.

```c
int sum(int x, int y) {
    return x + y;
}
```

소스 수준에서는 동일한 연산.

```text
x + y
```

하지만 대상 시스템에 따라 기계어 바이트 표현이 달라짐.

### Linux 32

```text
55 89 e5 8b 45 0c 03 45 08 c9 c3
```

### Windows

```text
55 89 e5 8b 45 0c 03 45 08 5d c3
```

### Sun

```text
81 c3 e0 08 90 02 00 09
```

### Linux 64

```text
55 48 89 e5 89 7d fc 89 75 f8
03 45 fc c9 c3
```

결과:

```text
Same C Source

       ↓

Linux 32   → different byte sequence
Windows    → different byte sequence
Sun        → different byte sequence
Linux 64   → different byte sequence
```

* C 코드의 의미는 동일
* 기계 수준 표현은 서로 다름

---

## CPU 아키텍처에 따라 코드가 달라지는 이유

프로세서마다 지원하는 **명령어 집합 아키텍처(ISA)** 가 다를 수 있음.

대표적인 예:

```text
x86
x86-64
ARM
RISC-V
SPARC
```

ISA는 다음과 같은 내용을 정의.

```text
어떤 명령어가 존재하는가
어떤 레지스터가 존재하는가
operand를 어떻게 지정하는가
명령어를 어떤 비트 패턴으로 인코딩하는가
```

따라서 동일한 연산:

```c
x + y
```

이라도 서로 다른 ISA에서는 완전히 다른 기계어가 필요.

```text
C Source

       ┌→ x86-64 Machine Code
       │
Compiler
       │
       ├→ ARM Machine Code
       │
       └→ RISC-V Machine Code
```

* ARM CPU는 x86-64 기계어를 그대로 실행할 수 없음
* x86-64 CPU 역시 ARM 기계어를 그대로 실행할 수 없음

즉:

```text
Machine Code
=
Architecture-dependent
```

---

## 같은 CPU 계열에서도 코드 표현이 달라질 수 있음

CS:APP의 예제에서는 Linux 32와 Windows가 모두 IA32 계열을 사용하지만 생성된 바이트가 완전히 같지는 않음.

```text
Linux 32

55 89 e5 ... c9 c3
```

```text
Windows

55 89 e5 ... 5d c3
```

* ISA만으로 프로그램 전체의 바이너리 호환성이 결정되는 것은 아님
* 운영체제와 소프트웨어 환경 역시 서로 다른 **코딩 규약(Coding Convention)** 을 사용할 수 있음

더 넓은 실제 실행 파일 수준에서는 다음 요소들도 바이너리 호환성에 영향.

```text
Instruction Set Architecture
+
ABI
+
Calling Convention
+
Executable Format
+
Operating System Interface
```

예를 들어 대표적인 실행 파일 형식:

```text
Linux    → ELF
Windows  → PE
macOS    → Mach-O
```

> 실행 파일 형식이나 시스템 호출 등의 차이는 이번 절의 핵심보다 더 넓은 시스템 수준의 문제이며, 이후 학습에서 구체적으로 연결 가능.

---

## ABI와 바이너리 호환성

동일한 ISA를 사용한다고 해서 모든 바이너리가 자동으로 호환되는 것은 아님.

프로그램이 다른 코드와 정상적으로 상호작용하려면 다음과 같은 규칙도 맞아야 함.

```text
함수 인자를 어디에 전달하는가
       ↓
결과값을 어디에 저장하는가
       ↓
어떤 레지스터를 보존하는가
       ↓
스택을 어떻게 사용하는가
```

이러한 머신 수준의 인터페이스 규칙을 **ABI(Application Binary Interface)** 와 연결해서 이해할 수 있음.

```text
Source-level Interface
        ↓
       API

Binary-level Interface
        ↓
       ABI
```

* API는 소스 코드 수준의 인터페이스
* ABI는 컴파일된 바이너리 수준의 인터페이스

이 차이는 이후 Chapter 3의 함수 호출과 스택 구조를 학습할 때 중요.

---

## 소스 코드의 이식성과 바이너리의 이식성

C 언어는 특정 CPU의 기계어 자체를 작성하는 언어가 아님.

따라서 동일한 C 소스를 서로 다른 대상으로 다시 컴파일 가능.

```text
        source.c
           │
     ┌─────┼─────┐
     ↓     ↓     ↓
   x86   ARM   RISC-V
```

이를 통해 **소스 수준의 이식성(Portability)** 확보 가능.

그러나 이미 생성된 기계어는 특정 실행 환경에 의존.

```text
Source Code
→ relatively portable

Machine Code
→ machine dependent
```

즉:

> **소스 코드가 이식 가능하다는 것과 컴파일된 바이너리가 이식 가능하다는 것은 서로 다른 문제.**

---

## 프로그램도 결국 바이트의 연속

Chapter 1에서 확인한 핵심 관점:

```text
All Information
      ↓
     Bits
```

이는 프로그램 코드에도 그대로 적용.

```text
C Source
   ↓
Machine Code
   ↓
Bytes
   ↓
Bits
```

CPU 관점에서 실행 프로그램의 기계어는 결국 특정 규칙에 따라 해석되는 바이트의 연속.

예:

```text
55 48 89 e5 ...
```

이 바이트들 자체에:

```text
"이것은 sum 함수"
"이 변수는 int x"
"여기는 return 문"
```

이라는 C 언어 수준의 의미가 직접 저장되어 있는 것은 아님.

컴파일 과정에서 원래 소스의 고수준 구조 대부분이 저수준 기계 명령어로 변환됨.

```text
int sum(int x, int y)
        ↓
Machine Instructions
        ↓
Byte Sequence
```

* 디버깅을 위한 별도의 심볼이나 메타데이터가 포함될 수는 있음
* 하지만 기계어 명령 자체가 원래 C 소스 구조를 그대로 보존하는 것은 아님

---

## 코드와 데이터의 관계

메모리 관점에서는 코드와 데이터 모두 바이트로 표현됨.

```text
Memory

┌─────────────────┐
│ Machine Code    │ → bytes
├─────────────────┤
│ Integer Data    │ → bytes
├─────────────────┤
│ Strings         │ → bytes
└─────────────────┘
```

차이는 **해당 바이트를 어떤 규칙으로 해석하는가**에 있음.

```text
Bytes
 │
 ├→ Integer Representation
 │
 ├→ Character Encoding
 │
 └→ Instruction Encoding
```

이는 Chapter 1의 핵심 관점과 직접 연결.

```text
Bits + Context
```

* 정수로 해석하면 데이터
* 문자 인코딩으로 해석하면 문자열
* ISA의 명령어 인코딩 규칙으로 해석하면 기계어 명령

---

## “코드와 데이터가 같다”는 표현의 주의점

코드와 데이터가 모두 바이트로 저장된다고 해서 현대 시스템이 두 영역을 아무 구분 없이 취급한다는 의미는 아님.

실행 과정에서는 CPU가 **프로그램 카운터(Program Counter)** 가 가리키는 주소에서 명령어를 가져와 ISA 규칙에 따라 디코딩.

```text
Program Counter
      ↓
Instruction Address
      ↓
Fetch Bytes
      ↓
Decode
      ↓
Execute
```

또한 현대 운영체제와 프로세서에서는 메모리 영역에 다음과 같은 권한 설정 가능.

```text
Read
Write
Execute
```

예를 들어 데이터 영역을 실행하지 못하게 하는 **NX(No-eXecute)** 와 같은 보호 기능도 존재.

따라서:

```text
CPU는 코드와 데이터를 전혀 구분하지 않는다
```

라고 단순화하기보다는:

> **코드와 데이터 모두 비트와 바이트로 표현되며, 그 의미는 해당 바이트를 어떤 방식으로 해석하고 사용하는지에 따라 달라짐.**

으로 이해하는 것이 적절.

---

## 문자열 표현과 코드 표현 비교

앞 절에서 ASCII 문자열 `"12345"`는 다음과 같이 표현됨.

```text
31 32 33 34 35 00
```

동일한 문자 인코딩을 사용하는 시스템이라면 바이트 순서나 워드 크기와 관계없이 같은 표현 사용 가능.

반면 기계어는:

```text
Architecture
+
Machine Conventions
```

에 강하게 의존.

정리하면:

| 종류           | 해석 규칙                                  | 플랫폼 의존성  |
| ------------ | -------------------------------------- | -------- |
| ASCII 문자열    | Character Encoding                     | 상대적으로 낮음 |
| 정수           | Integer Representation + Byte Ordering | 존재       |
| 포인터          | Address Representation                 | 높음       |
| Machine Code | ISA + Binary Convention                | 매우 높음    |

* 텍스트가 절대적으로 플랫폼 독립적이라는 의미는 아님
* 문자 인코딩이나 줄바꿈 규칙 등이 다르면 차이가 발생 가능
* 다만 동일한 문자 인코딩을 사용하는 경우 기계어보다 훨씬 이식성이 높음

---

## 개발할 때 왜 중요한가

### 크로스 플랫폼 빌드

하나의 C 프로젝트라도 대상 시스템별 바이너리가 필요.

```text
Source Code
   │
   ├→ Linux x86-64 Binary
   ├→ Windows x86-64 Binary
   ├→ macOS ARM64 Binary
   └→ Linux ARM64 Binary
```

* 소스 저장소는 하나일 수 있음
* 최종 실행 파일은 target별로 달라짐

---

### 크로스 컴파일

현재 실행 중인 시스템과 다른 아키텍처를 대상으로 코드 생성 가능.

```text
Build Machine
x86-64 Linux
      ↓
Cross Compiler
      ↓
ARM Machine Code
      ↓
ARM Target Device
```

* 임베디드 시스템
* 모바일 개발
* 운영체제 개발

등에서 중요한 개념

---

### 실행 파일 분석

디버거와 디스어셈블러에서는 기계어 바이트를 사람이 읽을 수 있는 어셈블리 명령어로 변환.

```text
Machine Bytes
     ↓
Disassembler
     ↓
Assembly Instructions
```

예:

```text
55 48 89 e5 ...
```

를 단순 데이터가 아니라 **x86-64 명령어 인코딩**이라는 컨텍스트에서 분석.

이는 Chapter 3의 핵심 학습 내용으로 직접 연결.

---

### 보안

코드 역시 바이트로 존재한다는 사실은 시스템 보안에서도 중요.

```text
Machine Code
+
Memory
+
Control Flow
```

를 이해해야 이후 다음 개념 분석 가능.

```text
Buffer Overflow
Code Injection
Return-Oriented Programming
Memory Protection
```

단순히 C 문법만으로는 프로그램이 실제 시스템에서 어떻게 공격되거나 보호되는지 충분히 설명할 수 없음.

---

## 이후 학습과의 연결

Chapter 2.1에서는 지금까지 여러 종류의 정보를 모두 **바이트 표현**이라는 관점에서 확인.

```text
Bits
 ↓
Hexadecimal Notation
 ↓
Data Sizes
 ↓
Byte Ordering
 ↓
Strings
 ↓
Machine Code
```

서로 전혀 다른 것으로 보이는:

```text
Integer
String
Pointer
Machine Code
```

도 머신 수준에서는 결국:

```text
Byte Sequence
```

라는 공통된 형태를 가짐.

차이는 각 바이트열에 의미를 부여하는 **해석 규칙(Context)**.

```text
Bytes
+
Context
=
Information
```

이후에는 이 비트들을 직접 계산하고 조작하기 위한 **Boolean Algebra와 Bit-level Operations** 학습으로 연결.

```text
Representation
     ↓
Boolean Algebra
     ↓
Bitwise Operations
     ↓
Integer Representation
```

Chapter 3에서는 이번 절에서 잠깐 확인한 기계어 표현을 본격적으로 분석.

```text
C Source
   ↓
Assembly
   ↓
Machine Instructions
   ↓
Registers / Memory
   ↓
Program Execution
```

---

## 정리

* C 소스 코드는 컴파일 과정을 거쳐 기계어 코드로 변환됨
* 기계어는 CPU가 ISA 규칙에 따라 해석하는 바이트 시퀀스
* 하나의 기계어 명령은 하나 이상의 바이트로 인코딩될 수 있음
* 동일한 C 소스라도 CPU 아키텍처에 따라 서로 다른 기계어 생성
* 동일한 CPU 계열에서도 운영체제와 바이너리 규약에 따라 차이 발생 가능
* 따라서 소스 코드의 이식성과 바이너리의 이식성은 서로 다른 개념
* 프로그램 코드 역시 머신 관점에서는 바이트의 연속
* 원래 C 코드의 변수명, 표현식 등의 고수준 의미는 기계 명령 자체에 그대로 남지 않음
* 코드와 데이터 모두 바이트로 표현되지만 서로 다른 규칙과 실행 문맥으로 해석
* 이러한 관점은 이후 기계 수준 프로그래밍과 시스템 보안 학습의 기반

> **프로그램 코드도 결국 바이트의 연속이며, 그 바이트를 기계어 명령으로 만드는 것은 대상 아키텍처의 명령어 인코딩 규칙과 실행 컨텍스트임.**
