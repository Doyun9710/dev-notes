# 1.2 프로그램은 여러 단계를 거쳐 실행 파일로 변환됨

> Programs Are Translated by Other Programs into Different Forms

## 핵심 개념

- 사람이 작성한 C 소스 코드는 CPU가 직접 실행할 수 없는 형태
- 프로그램 실행을 위해 CPU가 이해할 수 있는 **기계어(Machine Code)** 로 변환 필요
- 하나의 프로그램이 한 번에 기계어가 되는 것이 아니라 여러 프로그램을 거쳐 단계적으로 변환됨
- 이러한 변환 과정 전체를 **컴파일 시스템(Compilation System)** 이라고 함

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

## 컴파일 시스템의 4단계

### 1. 전처리 — Preprocessing

- 담당 프로그램: **전처리기(Preprocessor, `cpp`)**
- `#`으로 시작하는 전처리 지시문 처리
  - `#include` → 헤더 파일 내용 포함
  - `#define` → 매크로 정의 및 치환
  - `#if`, `#ifdef` → 조건부 컴파일 처리
- 결과물: `hello.i`
  - 전처리가 완료된 C 소스 코드
  - 사람이 읽을 수 있는 텍스트 파일

```text
hello.c
   ↓ cpp
hello.i
```

### 2. 컴파일 — Compilation

- 담당 프로그램: **컴파일러(Compiler, `cc1`)**
- 전처리된 C 코드를 분석하여 **어셈블리 코드**로 변환
- 결과물: `hello.s`
  - 어셈블리어로 작성된 텍스트 파일
  - CPU가 수행할 연산을 저수준 코드로 표현

```text
hello.i
   ↓ cc1
hello.s
```

- 어셈블리 코드는 대상 CPU 아키텍처에 맞게 생성됨
- C, C++, Rust 등의 언어가 하나의 공통 어셈블리어로 표준화되는 것은 아님

```text
C / C++ / Rust
       ↓
    Compiler
       ↓
x86-64 또는 ARM 코드
```

### 3. 어셈블 — Assembly

- 담당 프로그램: **어셈블러(Assembler, `as`)**
- 어셈블리 코드를 기계어로 변환
- 결과물: `hello.o`
  - 바이너리 형태의 목적 파일(Object File)
  - 기계어 코드 포함
  - 아직 최종 실행 파일은 아님

```text
hello.s
   ↓ as
hello.o
```

- `hello.o`는 **재배치 가능한 목적 파일(Relocatable Object File)**
- 다른 목적 파일이나 라이브러리와 아직 완전히 연결되지 않은 상태
- 외부 함수나 전역 변수의 최종 주소가 결정되지 않았을 수 있음

### 4. 링크 — Linking

- 담당 프로그램: **링커(Linker, `ld`)**
- 여러 목적 파일과 라이브러리를 연결하여 최종 실행 파일 생성
- 결과물: `hello`
  - 운영체제가 실행할 수 있는 실행 파일(Executable Object File)

```text
hello.o
   +
libraries
   +
other object files
   ↓
  Linker
   ↓
 hello
```

- `printf`처럼 소스 파일에 직접 정의되지 않은 함수는 라이브러리의 구현과 연결 필요
- 링커의 주요 역할
  - **Symbol Resolution** → 함수나 전역 변수의 참조를 실제 정의와 연결
  - **Relocation** → 코드와 데이터의 배치에 맞게 주소 조정

---

## 전체 컴파일 과정

```text
hello.c
│
│ Preprocessing
▼
hello.i
│
│ Compilation
▼
hello.s
│
│ Assembly
▼
hello.o
│
│ Linking
▼
hello
```

| 파일 | 의미 | 형태 |
| --- | --- | --- |
| `hello.c` | 원본 C 소스 코드 | Text |
| `hello.i` | 전처리가 완료된 C 코드 | Text |
| `hello.s` | 어셈블리 코드 | Text |
| `hello.o` | 재배치 가능한 목적 파일 | Binary |
| `hello` | 최종 실행 파일 | Binary |

---

## 개발할 때 왜 중요한가

- 빌드 오류가 발생한 단계를 구분하는 데 도움
  - 헤더 파일 / 매크로 문제 → Preprocessing
  - 문법 / 타입 문제 → Compilation
  - 목적 파일 생성 문제 → Assembly
  - `undefined reference`, `multiple definition` → Linking
- 작성한 C 코드가 실제 기계어로 어떻게 변환되는지 이해하는 기반
  - 함수 호출
  - 조건문과 분기
  - 반복문
  - 레지스터
  - 메모리 접근
  - 컴파일러 최적화
- 프로그램이 기계어와 메모리 수준에서 어떻게 실행되는지 이해하는 기반
  - Stack
  - Memory Layout
  - Buffer Overflow

---

## 정리

- C 소스 코드는 CPU가 직접 실행할 수 없는 형태
- 실행을 위해 **전처리 → 컴파일 → 어셈블 → 링크** 과정을 거침
- 각 단계마다 역할과 결과물이 다름
- `hello.o`와 `hello`는 모두 바이너리 파일이지만 목적이 다름
  - `hello.o` → 추가 연결이 필요한 재배치 가능한 목적 파일
  - `hello` → 운영체제가 실행할 수 있는 최종 실행 파일
- 컴파일 시스템에 대한 이해는 빌드 오류, 성능, 메모리, 시스템 보안을 이해하는 기반
