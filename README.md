# 어셈블리어 학습 자료

## 개발환경 구축

* 어셈블리어의 문법은 CPU의 명령어 집합 구조(ISA), 운영체제, 어셈블러의 종류에 따라 달라질 수 있음
  - 이 자료에서는 Intel x64, 리눅스, NASM 기준으로 설명할 것이다.

* WSL(Windows Subsystem for Linux) 또는 리눅스 환경에서 다음과 같이 구축한다.

  1) WSL 또는 리눅스의 경우

    - 파워셸을 열고 다음 명령어 실행

      ```
      $ wsl --install
      ```

    - 필수 도구 설치
 
      ```
      $ sudo apt update
      $ sudo apt install nasm build-essential gdb
      ```

    - 컴파일 및 실행
 
      ```
      $ nasm -f elf64 hello.asm -o hello.o
      $ nasm -f elf64 -g -F dwarf hello.asm -o hello.o  # 만약 GDB 디버깅을 할 경우
      $ ld hello.o -o hello
      $ ./hello
      ```

    - 디버깅: gdb ./hello 실행 후 layout regs라고 입력해 보세요. (... 이후 내용 나중에 추가)

  2) VS-Code + WSL의 경우

    - 1. VS Code 설치 후 'WSL' 확장 프로그램 설치

    - 2. 좌측 하단의 >< 아이콘을 클릭하여 "Connect to WSL" 선택.

    - 3. 'x86 and x86_64 Assembly' 확장을 설치하면 코드 하이라이팅이 지원됩니다.

    - 4. **'GDB Debugger'**를 통해 레지스터 값을 실시간으로 보며 디버깅할 수 있습니다. (... 이후 내용 나중에 추가)

## 레지스터

### 레지스터의 크기

<img width="659" height="250" alt="image" src="https://github.com/user-attachments/assets/91898721-f2ba-41d2-bce4-5b834f2cc2ca" />

* RAX: 64비트 전체를 사용

* EAX: 64비트 중 하위 32비트를 사용

* AX: 64비트 중 하위 16비트를 사용

  - AH: AX에서 하위 절반의 상위 8비트를 사용

  - AL: AX에서 하위 절반의 하위 8비트를 사용
 
### 레지스터 종류

| 64비트 | 32비트 | 16비트 | 상위 8비트 | 하위 8비트 | 용도 |
| ------ | ------ | ------ | ---------- | ---------- | ---- |
| RAX    | EAX    | AX     | AH         | AL         | 범용, Accumulator를 의미하며 결과값을 저장하는 곳, 리턴값을 담는 곳으로 많이 쓰임 |
| RBX    | EBX    | BX     | BH         | BL         | 범용, Base를 의미하며 메모리의 기준점으로 자주 사용 |
| RCX    | ECX    | CX     | CH         | CL         | 범용, Counter를 의미하며 반복문을 돈 횟수를 기록하는데 자주 사용 |
| RDX    | EDX    | DX     | DH         | DL         | 범용, Data를 의미하며 연산 보조 및 I/O 포트 저장 등 다양한 데이터를 담는데 주로 사용 |
| RSI    | ESI    | SI     | -          | SIL        | 범용, Source Index를 의미하며 데이터 원본 주소를 가리킴 |
| RDI    | EDI    | DI     | -          | DIL        | 범용, Destination Index를 의미하며 데이터 도착지 주소를 가리킴 |
| RBP    | EBP    | BP     | -          | BPL        | 특수, Base Pointer를 의미하며 스택의 기준 주소를 가리킴 |
| RSP    | ESP    | SP     | -          | SPL        | 특수, Stack Pointer를 의미하며 스택의 최상단 주소를 가리킴 (Push, Pop 할 때마다 바뀜) |
| RIP    | EIP    | IP     | -          | IPL        | 특수, Instruction Pointer를 의미하며 CPU가 다음에 실행할 명령어가 어디 있는지 알려줌 |
| RFLAGS | EFLAGS | FLAGS  | -          | -          | 특수, Flags Register를 의미하며, CPU의 현재 상태를 알려줌 |
| R8     | R8D    | R8W    | -          | R8B        | 범용, x64에 추가됨 |
| R9     | R9D    | R9W    | -          | R9B        | 범용, x64에 추가됨 |
| R10    | R10D   | R10W   | -          | R10B       | 범용, x64에 추가됨 |
| R11    | R11D   | R11W   | -          | R11B       | 범용, x64에 추가됨 |
| R12    | R12D   | R12W   | -          | R12B       | 범용, x64에 추가됨 |
| R13    | R13D   | R13W   | -          | R13B       | 범용, x64에 추가됨 |
| R14    | R14D   | R14W   | -          | R14B       | 범용, x64에 추가됨 |
| R15    | R15D   | R15W   | -          | R15B       | 범용, x64에 추가됨 |

* 약자의 뜻은 다음과 같음
  - R(Register / 64비트): 64비트 레지스터
  - E(Extended / 32비트): 16비트 시절, 32비트로 확장되었다는 의미
  - H(High / 8비트): 16비트 레지스터의 상위 8비트를 의미
  - L(Low / 8비트): 16비트 레지스터의 하위 8비트를 의미
  - W(Word): 16비트 / D(Doubleword): 32비트 / B(Byte): 8비트

* RFLAGS 레지스터의 각 비트별 의미

| 비트 | 약어 | 이름 | 종류 | 설명 |
| ---- | ---- | ---- | ---- | ---- |
| 0 | CF | Carry Flag | 상태 | 부호 없는 연산에서 자리 올림/빌림 발생* |
| 2 | PF | Parify Flag | 상태 | 결과의 하위 8비트 중 1의 개수가 짝수임 |
| 4 | AF | Auxiliary Flag | 상태 | BCD 연산 시 하위 4비트에서 자리 올림 발생 |
| 6 | ZF | Zero Flag | 상태 | 연산 결과가 0임* |
| 7 | SF | Sign Flag | 상태 | 연산 결과가 음수임 (최상위 비트가 1)* |
| 8 | TF | Trap Flag | 시스템 | 한 단계씩 실행 모드 활성화 (디버깅용) |
| 9 | IF | Interrupt Enable | 시스템 | 외부 인터럽트(키보드 입력 등)를 처리함 |
| 10 | DF | Direction Flag | 제어 | 문자열 처리시 주소를 감소시킴 (0이면 증가) |
| 11 | OF | Overflag Flag | 상태 | 부호 있는 연산에서 범위 초과 발생* |
| 12-13 | IOPL | I/O Privilege | 시스템 | 입출력 포트에 접근 가능한 권한 레벨 (0~3) |
| 14 | NT | Nested Task | 시스템 | 현재 태스크가 다른 태스크 내부에 중첩됨 |
| 16 | RF | Resume Flag | 시스템 | 디버그 예외 상황을 일시적으로 무시함 |
| 17 | VM | Virtual 8086 | 시스템 | 가상 8086 모드 동작 중 |
| 18 | AC | Alignment Check | 시스템 | 메모리 정렬 확인 기능 활성화 |
| 21 | ID | ID Flag | 시스템 | CPUID 명령어를 지원하는지 확인용 |

* RFLAGS 레지스터 조작 명령어
  - PUSHFQ (Push Flags Quadword): 현재 RFLAGS 값을 스택에 저장합니다.
  - POPFQ (Pop Flags Quadword): 스택에 있는 값을 RFLAGS로 복원합니다.
  - STC / CLC: Carry Flag(CF)를 직접 1로 만들거나(Set) 0으로 만듭니다(Clear).
  - STD / CLD: Direction Flag(DF)를 직접 설정하거나 해제합니다.

### 엔디안

* 데이터를 저장하는 순서가 아키텍처마다 다름
  - 빅 엔디안: 낮은 주소에 최상위 비트(Most Significant Bit)부터 저장하는 방식 (주로 IBM, JVM, 네트워크 전송)
    * 사람이 보기에 직관적이라는 장점이 있음
  - 리틀 엔디안: 낮은 주소에 최하위 비트(Least Significant Bit)부터 저장하는 방식 (주로 Intel)
    * 8비트와의 호환성이 좋고, 데이터 캐스팅 속도가 빠름 (예: 32비트에서 16비트로 캐스팅할 때 앞의 2개 바이트만 읽으면 됨)

* 만약 `0A 0B 0C 0D` 데이터를 저장한다고 하면,
  - 빅 엔디안의 경우
    | 메모리 주소 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
    | ---------- | - | - | - | - | - | - | - | - |
    | 바이트 | 0x00 | 0x00 | 0x00 | 0x00 | 0x0A | 0x0B | 0x0C | 0x0D |
  - 리틀 엔디안의 경우 (아래부터 1바이트씩 채움)
    | 메모리 주소 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
    | ---------- | - | - | - | - | - | - | - | - |
    | 바이트 | 0x00 | 0x00 | 0x00 | 0x00 | 0x0D | 0x0C | 0x0B | 0x0A |

### 시스템 콜

* 시스템 콜: 운영체제가 제공하는 API 집합 (리눅스 x64용 시스템 콜 종류는 [1](https://github.com/torvalds/linux/blob/master/arch/x86/entry/syscalls/syscall_64.tbl), [2](https://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/)를 참조)
  - syscall 호출을 위해 다음 레지스터를 사용할 수 있음
  - RAX: 시스템 콜 번호를 기입할 것 (위의 API 문서 링크 참조), 호출 뒤에는 결과 값이 담김 (성공시 리턴값, 실패시 오류 코드)
  - RDI, RSI, RDX, R10, R8, R9: 함수의 1, 2, 3, 4, 5, 6번째 인자를 넣을 것
  - (참고로 일반 라이브러리 호출의 경우 인자를 넣는 레지스터가 다름: RDI, RSI, RDX, RCX, R8, R9)
  - 6개보다 인자가 많으면 스택을 이용해야 함

## 어셈블리 코드

### 섹션

* 어셈블리 코드는 다음 4가지 섹션이 있음
  - `.data`: 초기화된 데이터 또는 상수를 선언함
  - `.bss`: 초기화되지 않은 변수를 선언함
  - `.text`: 프로그램 코드
  - `.shstrtab`: 기존 섹션에 대한 참조를 저장
 
### 데이터 타입

* 예제 코드

```
section .data
    num1:   equ 100
    num2:   equ 50
    msg:    db "Sum is correct", 10
```

* 원래 어셈블리 언어는 정적 타입 언어가 아니며 바이트 집합을 직접 다룸
  - NASM 자체적으로 데이터 타입을 정의하는 데 도움을 주는 함수를 제공함
 
* `.data` 섹션: 기본 데이터 타입을 기반으로 NASM에서 제공하는 의사 명령어는 다음과 같다.

| 키워드 | 이름 | 크기 | 설명 |
| ------ | ---- | ---- | ---- |
| DB | Define Byte | 1바이트 (8비트) | 문자나 작은 정수 저장 |
| DW | Define Word | 2바이티 (16비트) | 보통 16비트 정수 저장 |
| DD | Define Doubleword | 4바이트 (32비트) | 32비트 정수나 실수(float) 저장 |
| DQ | Define Quadword | 8바이트 (64비트) | 64비트 정수나 배정밀도 실수(double) |
| DT | Define Ten Bytes | 10바이트 (80비트) | 확장 정밀도 실수 저장 |
| DO | Define Octoword | 16바이트 (128비트) | 128비트 데이터 (SSE 레지스터 등에서 사용) |
| DY | Define Y-word | 32바이트 (256비트) | 256비트 데이터 (AVX 레지스터 용) |
| DZ | Define Z-word | 64바이트 (512비트) | 512비트 데이터 (AVX-512 레지스터 용) |

* `EQU` 상수 선언: num1은 100으로 치환됨 (C 언어의 #define과 같은 용도)

* 문장 뒤에 ,10 또는 ,13 ,10를 붙임 (10: 라인피드, 13: 캐리지리턴)

* `.bss` 섹션: 초기화되지 않은 변수를 예약하는 명령어는 다음과 같다.

| 키워드 | 이름 | 크기 | 설명 |
| ------ | ---- | ---- | ---- |
| RESB | Reserve Byte | 1바이트 | resb 10 (10바이트 예약) |
| RESW | Reserve Word | 2바이트 | resw 5 (10바이트 예약) |
| RESD | Reserve Doubleword | 4바이트 | 32비트 변수용 (float) |
| RESQ | Reserve Quadword | 8바이트 | 64비트 변수용 (double) |
| REST | Reserve Ten Bytes | 10바이트 | 확장 실수용 |
| RESO | Reserve Octoword | 16바이트 | 128비트용 |
| RESY | Reserve Y-word | 32바이트 | 256비트용 |
| RESZ | Reserve Z-word | 64바이트 | 512비트용 |

### 스택

* 스택은 push 할수록 거꾸로 자라게 되어 있어서 RSP의 주소 값이 감소하게 되어 있음, pop 할수록 RSP의 주소 값이 증가함
  - RSP의 주소 값은 데이터가 들어 있는 스택의 가장 위를 가리킴
  - RBP의 주소 값과 RSP의 주소 값이 같으면 스택이 비어 있다는 뜻임
  - 함수 호출시 항상 다음과 같은 동작을 수행해야 함
    ```
    push rbp        ; 기존 Base Pointer 백업
    mov rbp, rsp    ; 새로운 Frame Base Pointer 세트
    ...
    mov rsp, rbp    ; RSP 위치를 RBP 위치로 끌어올림으로써 쌓였던 데이터 모두 정리
    pop rbp         ; RSP가 가리키는 곳에서 값을 꺼내 RBP 레지스터에 넣음 (저장했던 Base Pointer 복원) --> 위 코드와 이 코드를 leave라는 명령어 하나로 합칠 수 있음
    ret             ; RSP가 가리키는 주소값(call 호출시 자동 저장된 값)을 꺼내 RIP 레지스터에 넣음 (CPU는 call 명령 다음 줄부터 실행하게 됨)
    ```

* 스택 동작의 예시 (단순한 값 저장/불러오기)
  - `push rax`: RSP 값이 8 감소한 뒤에 rax 값을 RSP 주소에 저장함
  - `pop rax`: RSP 주소로부터 값을 가져와 rax에 저장하고 RSP 값이 8 증가함
 
* 스택 동작의 예시 (함수 호출)
  - `call MyFunc`: MyFunc 함수를 호출
    ```
    push returnAddr  ; call 직후의 주소
    jump MyFunc      ; MyFunc 함수로 점프
    ```
  - `ret`
    ```
    pop rip    ; Instruction Pointer에 저장했던 call 직후의 주소를 가져와서 저장
    ```

* 예제

```
section .text
    global _start

_start:
    ; 1. 함수 호출 (리턴 주소를 스택에 push하고 jump)
    call add_numbers

    ; 3. 프로그램 종료 (시스템 콜)
    mov rax, 60         ; sys_exit
    xor rdi, rdi        ; status 0
    syscall

add_numbers:
    ; --- 프롤로그 (새로운 층 쌓기) ---
    push rbp            ; 부모(_start)의 RBP를 백업
    mov rbp, rsp        ; 현재 위치를 이 함수의 기준점(바닥)으로 설정
    sub rsp, 16         ; 지역 변수 2개를 위한 16바이트 공간 확보

    ; --- 본문 (스택 사용) ---
    mov qword [rbp-8], 10   ; 첫 번째 지역 변수 = 10
    mov qword [rbp-16], 20  ; 두 번째 지역 변수 = 20

    mov rax, [rbp-8]    ; rax = 10
    add rax, [rbp-16]   ; rax = 10 + 20 (결과는 30)

    ; --- 에필로그 (정리하고 돌아가기) ---
    leave               ; mov rsp, rbp + pop rbp (스택 원상복구)
    ret                 ; 스택의 리턴 주소를 꺼내 _start로 복귀
```
