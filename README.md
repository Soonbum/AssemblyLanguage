# 어셈블리어 학습 자료

## 개발환경 구축

* 어셈블리어의 문법은 CPU의 명령어 집합 구조(ISA), 운영체제, 어셈블러의 종류에 따라 달라질 수 있음
  - 이 자료에서는 Intel x64, 리눅스, NASM 기준으로 설명할 것이다.
  - 주로 참조한 자료는 [여기](https://github.com/0xAX/asm)에 있음
  - NASM 관련 자료는 [여기](https://www.nasm.us/doc/nasm01.html)에 있음
  - 인텔의 어셈블리 자료는 [여기](https://www.intel.com/content/www/us/en/developer/articles/technical/intel-sdm.html)에서 구할 수 있음

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
| RIP    | EIP    | IP     | -          | IPL        | 특수, Instruction Pointer를 의미하며 CPU가 다음에 실행할 명령어가 어디 있는지 알려줌 (`mov` 명령어 사용 불가하며 `jmp`, `call`, `ret` 등을 통해 자동으로 바뀜)  |
| RFLAGS | EFLAGS | FLAGS  | -          | -          | 특수, Flags Register를 의미하며, CPU의 현재 상태를 알려줌 |
| R8     | R8D    | R8W    | -          | R8B        | 범용, x64에 추가됨 |
| R9     | R9D    | R9W    | -          | R9B        | 범용, x64에 추가됨 |
| R10    | R10D   | R10W   | -          | R10B       | 범용, x64에 추가됨 |
| R11    | R11D   | R11W   | -          | R11B       | 범용, x64에 추가됨 |
| R12    | R12D   | R12W   | -          | R12B       | 범용, x64에 추가됨 |
| R13    | R13D   | R13W   | -          | R13B       | 범용, x64에 추가됨 |
| R14    | R14D   | R14W   | -          | R14B       | 범용, x64에 추가됨 |
| R15    | R15D   | R15W   | -          | R15B       | 범용, x64에 추가됨 |

* 실수 및 벡터 레지스터는 다음과 같다.

| 이름 | 크기 | 용도 |
| ---- | ---- | ---- |
| XMM0 ~ XMM15 | 16바이트 (128비트) | SSE 명령어, 실수 연산, 짧은 벡터 연산 |
| YMM0 ~ YMM15 | 32바이트 (256비트) | AVX 명령어 전용 (XMM을 포함하는 확장 형태) |
| ZMM0 ~ ZMM31 | 64바이트 (512비트) | AVX-512 전용 (최신 고성능 CPU) |

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
| 10 | DF | Direction Flag | 제어 | 문자열 처리시 주소를 증가 또는 감소시킬지 결정 (0이면 증가, 1이면 감소) |
| 11 | OF | Overflag Flag | 상태 | 부호 있는 연산에서 범위 초과 발생* |
| 12-13 | IOPL | I/O Privilege | 시스템 | 입출력 포트에 접근 가능한 권한 레벨 (0~3) |
| 14 | NT | Nested Task | 시스템 | 현재 태스크가 다른 태스크 내부에 중첩됨 |
| 16 | RF | Resume Flag | 시스템 | 디버그 예외 상황을 일시적으로 무시함 |
| 17 | VM | Virtual 8086 | 시스템 | 가상 8086 모드 동작 중 |
| 18 | AC | Alignment Check | 시스템 | 메모리 정렬 확인 기능 활성화 |
| 21 | ID | ID Flag | 시스템 | CPUID 명령어를 지원하는지 확인용 |

* RFLAGS 레지스터 조작 명령어
  - `PUSHFQ` (Push Flags Quadword): 현재 RFLAGS 값을 스택에 저장합니다. (EFLAGS(32비트)에 대해서는 `PUSHFD`, FLAGS(16비트)에 대해서는 `PUSHF`)
  - `POPFQ` (Pop Flags Quadword): 스택에 있는 값을 RFLAGS로 복원합니다. (EFLAGS(32비트)에 대해서는 `POPFD`, FLAGS(16비트)에 대해서는 `POPF`)
  - `STC` / `CLC`: Carry Flag(CF)를 직접 1로 만들거나(Set) 0으로 만듭니다(Clear).
  - `STD` / `CLD`: Direction Flag(DF)를 직접 설정하거나 해제합니다.
  - (나머지는 생략)

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

* 사용자가 직접 작성하는 어셈블리 코드는 다음 섹션들이 있음
  - `.data`: 초기화된 데이터 또는 상수를 선언함
  - `.rodata`: 읽기 전용 데이터 정의를 선언함
  - `.bss`: 초기화되지 않은 변수를 선언함 (실행시 0으로 채워짐)
  - `.text`: 프로그램 코드
 
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

## 연산자

### 비트 연산

* 시프트: 비트를 왼쪽 혹은 오른쪽으로 이동시키는 명령어들은 다음과 같다.
  - `shl`: 1번째 피연산자의 비트를 왼쪽으로 shift (반대쪽은 0으로 채움)
  - `shr`: 1번째 피연산자의 비트를 오른쪽으로 shift (반대쪽은 0으로 채움)
  - `rol`: 1번째 피연산자의 비트를 왼쪽으로 회전시킴
  - `ror`: 1번째 피연산자의 비트를 오른쪽으로 회전시킴

* 테스트 및 스캔
  - `test`: 1번째 피연산자와 2번째 피연산자의 AND 연산 후에 결과를 저장하지 않고 flag 업데이트
    * ZF(Zero Flag): 연산 결과가 0이면 1이 됨 (즉, rax가 0이면 ZF=1)
    * SF(Sign Flag): 최상위 비트(부호 비트)가 1이면 1이 됨 (즉, rax가 음수이면 SF=1)
    ```
    test rax, rax
    jz .is_zero       ; rax가 0이면 점프
    js .is_negative   ; rax가 음수이면 점프
    ```
  - `bt` (Bit Test): 1번째 피연산자에서 n번째 자릿수(2번째 피연산자)가 확인하고 flag 업데이트
    * BTS(비트 확인 후 1로 세팅)
    * BTR(비트 확인 후 0으로 리셋)
    ```
    bt eax, 3         ; eax의 3번째 비트(2^3 = 8의 자리)를 확인
    jc .bit_is_one    ; 3번째 비트가 1이면(CF=1) 점프
    ```
  - `BSF` / `BSR` (Bit Scan): 레지스터 안에서 '1'이 처음으로 나타나는 위치(인덱스)를 찾고 flag 업데이트
    * BSF (Bit Scan Forward): 오른쪽(LSB, 0번 비트)에서 왼쪽으로 스캔
    * BSR (Bit Scan Reverse): 왼쪽(MSB)에서 오른쪽으로 스캔
    * 만약 대상 레지스터가 모두 0이면, ZF(Zero Flag)가 1이 되고 결과값은 정의되지 않습니다. (반드시 ZF를 먼저 체크할 것)
    ```
    bsf rax, rbx      ; rbx에서 처음 1이 나오는 위치를 rax에 저장
    jz .all_zeros     ; rbx가 0이었다면 예외 처리
    ```

### 데이터 전송 명령어

* `mov`: 범용 레지스터/메모리/값을 범용 레지스터에 저장하는 명령어 (이름은 mov지만 실제 의미는 복사, 저장에 가까움)
  - `mov rax, rcx`: rcx의 값을 rax에 저장
  - `mov rax, 5`: 5를 rax에 저장
  - `mov [rcx], rax`: rax의 값을 rcx에 지정된 메모리 주소에 저장

* 특수한 경우에서 사용하는 `mov` 명령어
  - `movzx`: 작은 크기의 레지스터에서 큰 크기의 레지스터로 데이터를 복사할 때, 상위 비트를 모두 0으로 채움
  - `movsx`: 작은 크기의 레지스터에서 큰 크기의 레지스터로 데이터를 복사할 때, sign 비트를 상위 비트에 복사함 (양수는 0, 음수는 1)
  - `cmove` / `cmovne`: 이전 비교 연산에서 두 피연산자가 "같았다면/달랐다면" 2번째 피연산자의 값을 1번째 피연산자로 복사함
  - `cmovg` / `cmovlg`: 이전 비교 연산에서 1번째 피연산자가 2번째 피연산자보다 "크다면/크거나 같다면" 2번째 피연산자의 값을 1번째 피연산자로 복사함
  - `cmovl` / `cmovle`: 이전 비교 연산에서 1번째 피연산자가 2번째 피연산자보다 "작다면/작거나 같다면" 2번째 피연산자의 값을 1번째 피연산자로 복사함
  - `cmovz` / `cmovnz`: 이전 비교 연산에서 결과가 "0이었다면/0이 아니었다면" 2번째 피연산자의 값을 1번째 피연산자로 복사함
  - `cmovc` / `cmovnc`: 이전 비교 연산이 Carry flag를 "set했다면/set하지 않았다면" 2번째 피연산자의 값을 1번째 피연산자로 복사함

### 연산 명령어

* 정수 산술 연산 (가능하면, `mul`, `div` 연산은 지양할 것!)
  - `add`: 1번째 피연산자의 값에 2번째 피연산자의 값을 더하고 나서 결과를 1번째 피연산자에 저장 (덧셈)
  - `sub`: 1번째 피연산자의 값에 2번째 피연산자의 값을 빼고 나서 결과를 1번째 피연산자에 저장 (뺄셈)
  - `mul`: 부호 없는 연산. 피연산자는 1개만 기재함, 나머지 하나는 무조건 `rax`. 피연산자와 `rax`의 값을 곱한 후 결과의 상위 64비트는 `rdx`, 하위 64비트는 `rax`에 저장
  - `imul`: 부호 있는 연산.
    * 피연산자 1개인 경우: `mul`과 동일
    * 피연산자 2개인 경우: 1번째 피연산자와 2번째 피연산자의 값을 곱하고 나서 결과를 1번째 피연산자에 저장
    * 피연산자 3개인 경우: 2번째 피연산자와 3번째 피연산자의 값을 곱하고 나서 결과를 1번째 피연산자에 저장
  - `div`: 부호 없는 연산. 나누어질 수는 상위 64비트 `rdx` (0으로 초기화할 것: `xor rdx, rdx`), 하위 64비트 `rax`에 담고, 피연산자는 1개만 기재함. 몫은 `rax`, 나머지는 `rdx`에 저장
  - `idiv`: 부호 있는 연산. 나누어질 수는 `cqo` (Convert Quadword to Octword) 명령으로 `rax`의 부호 비트를 `rdx`까지 먼저 확장해야 함. 피연산자는 1개만 기재함. 몫은 `rax`, 나머지는 `rdx`에 저장
  - `inc`: 1번째 피연산자의 값을 1 증가시킴
  - `dec`: 1번째 피연산자의 값을 1 감소시킴
  - `neg`: 1번째 피연산자의 값의 부호를 반전시킴, 예를 들면 5는 -5로 바뀜. (2의 보수)

* 논리 연산
  - `and`: 1, 2번째 피연산자의 논리 AND 연산 결과를 1번째 피연산자에 저장
  - `or`: 1, 2번째 피연산자의 논리 OR 연산 결과를 1번째 피연산자에 저장
  - `xor`: 1, 2번째 피연산자의 논리 XOR 연산 결과를 1번째 피연산자에 저장 (변수를 0으로 초기화시 자주 사용함)
  - `not`: 1번째 피연산자의 논리 NOT 연산 결과를 1번째 피연산자에 저장 (비트 반전)

### 분기 명령어

* 어셈블리어에서는 `cmp`와 `jmp` 및 파생 명령만으로 모든 분기 처리를 수행함
  - `jmp`: 1번째 피연산자가 가리키는 "라벨" 혹은 "프로그램의 주소"로 점프
  - `cmp`: 1번째 피연산자에서 2번째 피연산자를 뺀 후에 결과를 RFLAGS 레지스터에 상태를 표시함
  - `je` (Jump if Equal): 이전 `cmp` 연산 결과가 같았다면 1번째 피연산자가 가리키는 곳으로 점프
  - `jz` (Jump if Zero): RFLAGS 레지스터의 Zero flag가 1이었으면 1번째 피연산자가 가리키는 곳으로 점프 (`je`와 비슷함)
  - `jne` (Jump if Not Equal): 이전 `cmp` 연산 결과가 같지 않았다면 1번째 피연산자가 가리키는 곳으로 점프
  - `jnz` (Jump if Not Zero): RFLAGS 레지스터의 Zero flag가 0이었으면 1번째 피연산자가 가리키는 곳으로 점프 (`jne`와 비슷함)
  - `jg` (Jump if Greater): 이전 `cmp` 연산 결과가 0보다 크면 1번째 피연산자가 가리키는 곳으로 점프
  - `jge` (Jump if Greater or Equal): 이전 `cmp` 연산 결과가 0보다 크거나 같으면 1번째 피연산자가 가리키는 곳으로 점프
  - `jl` (Jump if Less): 이전 `cmp` 연산 결과가 0보다 작으면 1번째 피연산자가 가리키는 곳으로 점프
  - `jle` (Jump if Less or Equal): 이전 `cmp` 연산 결과가 0보다 작거나 같으면 1번째 피연산자가 가리키는 곳으로 점프
  - `ja` (Jump if Above): `jg`와 비슷하지만 부호 없는 비교 수행
  - `jae` (Jump if Above or Equal): `jge`와 비슷하지만 부호 없는 비교 수행
  - `jb` (Jump if Below): `jl`과 비슷하지만 부호 없는 비교 수행
  - `jbe` (Jump if Below or Equal): `jle`와 비슷하지만 부호 없는 비교 수행
  - `js` (Jump if Sign): 결과가 음수(Sign flag가 1)이면 1번째 비연산자가 가리키는 곳으로 점프
  - `jns` (Jump if Not Sign): 결과가 양수(Sign flag가 0)이면 1번째 비연산자가 가리키는 곳으로 점프
  - `jo` (Jump if Overflow): 오버플로우(Overflow flag가 1) 발생시 1번째 비연산자가 가리키는 곳으로 점프
  - `jc` (Jump if Carry): 캐리(Carry flag가 1) 발생시 1번째 비연산자가 가리키는 곳으로 점프

### 문자열/배열 관련 명령어

* 다음 명령어들을 통해 문자열뿐만 아니라 연속된 메모리 데이터(배열, 구조체)까지도 처리할 수 있음 (접미어 의미: byte-1바이트, word-2바이트, doubleword-4바이트, quadword-8바이트)
  - `movs(b|w|d|q)` (Moving String): [RSI]가 가리키는 데이터를 [RDI]가 가리키는 곳으로 복사
  - `cmps(b|w|d|q)` (Compare String): [RSI]와 [RDI]가 가리키는 데이터를 비교, 결과는 RFLAGS에 반영
  - `scas` (Scan String): RAX(또는 AL, EAX)의 값과 [RDI]가 가리키는 메모리 값을 비교합니다.
  - `lods` (Load String): [RSI]가 가리키는 메모리 값을 RAX 레지스터로 읽어옵니다.
  - `stos` (Store String): RAX 레지스터의 값을 [RDI]가 가리키는 메모리에 저장합니다.

* RFLAGS의 Direction flag
  - 위 명령들이 실행되면 RSI, RDI는 데이터 크기만큼 자동으로 변함
    * CLD (Clear DF): 주소가 증가합니다. (정방향 처리)
    * STD (Set DF): 주소가 감소합니다. (역방향 처리)

* 반복하기
  - `rep` (Repeat): RCX가 0이 될 때까지 반복 (주로 `movs`, `stos`와 함께 사용)
  - `repe` / `repz`: RCX가 0이 아니면서, 비교 결과가 같을 동안 반복 (주로 `cmps`, `scas`와 함께 사용)
  - `repne` / `repnz`: RCX가 0이 아니면서, 비교 결과가 다를 동안 반복

* 예제: memset과 같이 0으로 채우기

```
mov rdi, buffer    ; 목적지 주소
mov rax, 0         ; 채울 값 (0)
mov rcx, 100       ; 100번 반복
cld                ; 방향은 정방향(+)
rep stosq          ; RAX(0)를 [RDI]에 저장하고 RDI를 8바이트씩 증가시키기를 100번 반복
```

* 예제: 문자열 길이 찾기

```
mov rdi, string    ; 스캔할 주소
mov al, 0          ; 찾을 값 (\0)
mov rcx, -1        ; 무한대(최댓값) 설정
cld
repne scasb        ; AL(0)과 [RDI]가 다를 동안 계속 스캔
; 결과적으로 RDI는 널 문자 다음 위치를 가리키게 됨
```

* 예제: 문자열 뒤집기 (reverse)

```
section .data
        SYS_WRITE equ 1          ; `sys_write` 시스템 콜 번호.
        SYS_EXIT equ 60          ; `sys_exit` 시스템 콜 번호
        STD_OUT equ 1            ; 표준 출력 file descriptor 번호
        EXIT_CODE equ 0          ; 프로그램 종료 코드. 정상인 경우 0.
        NEW_LINE_LEN equ 1       ; new line 심볼만 있는 경우의 문자열 길이
        INPUT_LEN equ 12         ; INPUT 문자열 길이

        NEW_LINE db 0xa          ; new line 심볼('\n')의 ASCII 코드
        INPUT db "Hello world!"  ; 예제용 입력 문자열

section .bss
        OUTPUT resb INPUT_LEN    ; 뒤집은 문자열을 저장할 출력 버퍼

section .text
        global  _start

; 프로그램 엔트리 포인트
_start:
        xor rcx, rcx         ; rcx 값을 0으로 초기화. 입력 문자열 길이 값을 저장하기 위해 사용될 것임.
        mov rsi, INPUT       ; 입력 문자열 주소를 rsi 레지스터에 저장
        mov rdi, OUTPUT      ; 출력 버퍼 주소를 rdi 레지스터에 저장
        call reverseStringAndPrint    ; reverseStringAndPrint 프로시저 호출.

; 입력 문자열 길이를 계산하고 뒤집을 준비를 함
reverseStringAndPrint:
        cmp byte [rsi], 0    ; 주어진 문자열의 1번째 요소와 `NUL` terminator를 비교
        mov rdx, rcx         ; 뒤집힌 문자열의 길이를 rdx 레지스터에 저장
        je reverseString     ; 입력 문자열 끝에 도달하면 뒤집을 것
        lodsb                ; rsi로부터 바이트 하나를 al 레지스터에 로드하고, 문자열의 다음 문자로 포인터를 이동시킴
        push rax             ; 스택에 입력 문자열의 문자를 저장
        inc rcx              ; 입력 문자열 길이를 저장하는 카운터를 1 증가시킴
        jmp reverseStringAndPrint    ; 입력 문자열의 끝에 이를 때까지 반복

; 문자열을 뒤집은 후에 출력 버퍼에 저장
reverseString:
        cmp rcx, 0           ; 문자열 길이를 저장하는 카운터를 확인
        je printResult       ; 만약 `0`과 같다면 뒤집힌 문자열을 출력
        pop rax              ; 스택으로부터 문자 하나를 Pop
        mov [rdi], rax       ; 출력 버퍼에 가져온 문자 하나를 넣음
        inc rdi              ; 출력 버퍼의 다음 문자로 포인터를 이동시킴
        dec rcx              ; 문자열 길이에 대한 카운터를 1 감소시킴
        jmp reverseString    ; 문자열 끝에 이를 때까지 다음 문자로 이동

; 뒤집힌 문자열을 표준 출력으로 출력
printResult:
        mov rax, SYS_WRITE   ; 시스템 콜 번호 지정 (1: `sys_write`)
        mov rdi, STD_OUT     ; `sys_write`의 1번째 인자 지정 (`stdout`)
        mov rsi, OUTPUT      ; `sys_write`의 2번째 인자 지정 (출력할 결과 문자열에 대한 참조)
        syscall    ; `sys_write` 시스템 콜 호출

        mov rdx, NEW_LINE_LEN    ; 출력할 결과 문자열의 길이를 설정
        mov rax, SYS_WRITE       ; 시스템 콜 번호 지점 (1: `sys_write`)
        mov rdi, STD_OUT         ; `sys_write`의 1번째 인자 지정 (`stdout`)
        mov rsi, NEW_LINE        ; `sys_write`의 2번째 인자 지정 (출력할 결과 문자열에 대한 참조)
        syscall    ; `sys_write` 시스템 콜 호출

        mov rax, SYS_EXIT    ; 시스템 콜 번호 지정 (60: `sys_exit`)
        mov rdi, EXIT_CODE   ; `sys_exit`의 1번째 인자를 0으로 지정. (성공한 경우 0)
        syscall    ; `sys_exit` 시스템 콜 호출
```

## 매크로

* 매크로는 기계어 명령이 아님, 컴파일 할 때 미리 정의된 코드로 대체됨
  - 함수와 비교해서 점프 오버헤드가 없지만, 매크로를 많이 사용할수록 바이너리 크기가 커진다는 단점이 있음
  - 간단한 것은 괜찮지만 남용하지 않는 편이 좋음

### 싱글 라인 매크로

* `%define 매크로이름 값`: 매크로이름을 오른쪽의 값으로 치환함

### 멀티 라인 매크로

* 선언한 "인자개수"만큼 호출할 때 인자를 순서대로 넣으면 마치 어셈블리어 명령어처럼 작동함
  - 주의사항: 매크로 안에서 라벨을 사용할 경우 라벨 앞에 `%%` 접두사가 있어야 함

```
%macro 매크로이름 인자개수
    ; 실행할 코드들
    ; %1, %2 순서로 인자에 접근
%endmacro
```

* 예제 코드는 다음과 같음

```
%macro SAFE_ADD 2
    push rax            ; 원래 rax 값 보존
    mov rax, %1         ; 첫 번째 인자를 rax에
    add rax, %2         ; 두 번째 인자를 더함
    mov %1, rax         ; 결과를 다시 첫 번째 인자에 저장
    pop rax             ; rax 값 복구
%endmacro

section .text
_start:
    SAFE_ADD rbx, 10    ; rbx = rbx + 10 으로 확장됨
```

### 조건부 어셈블리

* 조건부 어셈블리를 사용하면 다음과 같은 이점이 있다.
  - 플랫폼 대응: 윈도우용 코드와 리눅스용 코드를 한 파일에 짜두고, 빌드할 때 환경에 맞는 부분만 골라서 컴파일할 수 있음
  - 디버깅: 개발 중에는 로그 출력 코드를 넣고, 배포할 때는 %ifdef 하나만 꺼서 로그 코드를 싹 제거해 성능을 높일 수 있음
  - 최적화: CPU 사양에 따라 특정 명령어를 지원하는지 체크하여 최적의 코드를 선택하게 할 수 있음

* `%ifdef` / `%ifndef` / `%endif` (Define 체크)
  - 특정 단어가 정의되어 있는지 확인함
  - 주로 디버그 모드나 운영체제별 코드를 작성할 때 사용
  - `%ifdef`: 정의되어 있다면 포함
  - `%ifndef`: 정의되어 있지 않다면 포함
  - 예제 코드
    ```
    %define DEBUG_MODE

    %ifdef DEBUG_MODE
        ; 이 코드는 DEBUG_MODE가 정의되었을 때만 빌드에 포함됨
        PRINT_MSG "Debug: Checkpoint 1", 20
    %endif
    ```

* `%ifmacro` / `%ifnmacro` / `%endif` (매크로 존재 체크)
  - 특정 이름을 가진 매크로가 이미 만들어져 있는지 확인함
  - 다른 파일에서 가져온 매크로와 이름이 겹치지 않게 하거나, 특정 매크로가 있을 때만 코드를 짜고 싶을 때 유용함

* `%if` / `%elif` / `%else` / `%endif` (수식 체크)
  - 숫자 크기를 비교하거나 계산 결과가 참(0이 아님)인지 확인함
  - 예제 코드
    ```
    %define VERSION 2

    %if VERSION > 1
        ; 버전이 1보다 클 때만 실행되는 최신 기능 코드
    %else
        ; 구버전용 코드
    %endif
    ```

* `%ifenv` / `%ifnenv` (환경 변수 체크)
  - 컴퓨터의 **환경 변수(Environment Variable)**가 존재하는지 확인함
  - 예를 들어 빌드하는 시스템의 사용자 이름이 무엇인지, 특정 경로가 설정되어 있는지에 따라 코드를 바꿀 수 있음

* 기타 특수 지시어
  - `%ifempty`: 매크로에 전달된 인자가 비어 있는지 확인함
  - `%ifid`: 인자가 식별자(Identifier)(예: 레지스터 이름, 라벨 이름 등)인지 확인함
  - `%ifnum`: 인자가 숫자인지 확인함
  - `%ifstr`: 인자가 따옴표로 둘러싸인 문자열인지 확인함
  - `%iftoken`: 특정 토큰(코드 조각)이 존재하는지 확인함

### 유용한 표준 매크로

* `%include` 지시어
  - `%include "helpers.asm"`: 외부 .asm 파일을 include 시킴

* `%assign` 지시어
  - 파라미터 없이 숫자 값으로 확장되는 매크로를 정의함
  - 예제
    ```
    ; 예: 데이터 테이블을 자동으로 생성하는 매크로
    %assign i 1
    %rep 5
        db i          ; 1, 2, 3, 4, 5 순서대로 바이트 생성
        %assign i i+1 ; i 값을 1 증가 (재할당)
    %endrep

    ; 결과적으로 아래와 같이 코드가 생성됩니다:
    ; db 1
    ; db 2
    ; db 3
    ; db 4
    ; db 5
    ```

* `%defstr` 지시어
  - 파라미터 없이 문자열 값으로 확장되는 매크로를 정의함
  - `%defstr hello_world_msg "Hello world!"`

* `%!` 지시어
  - 환경 변수를 가져올 때 사용함
  - `%defstr HOME %!HOME`

* `%strlen` 지시어
  - 문자열 길이를 알려줌
  - 예제
    ```
    ; PRINT 매크로 정의
    %macro PRINT 1
    	  mov rax, 1              ; 시스템 콜 번호 지정 (1: `sys_write`)
        mov rdi, 1              ; `sys_write`의 1번째 인자를 1로 설정 (`stdout`)
        mov rsi, %1             ; `sys_write`의 2번째 인자를 프린트 할 문자열에 대한 참조로 설정 (매크로의 1번째 인자로 대체될 것이다)
        mov rdx, %strlen(%1)    ; `sys_write`의 3번째 인자를 프린트 할 문자열의 길이로 설정
        syscall                 ; `sys_write` 시스템 콜 호출
    %endmacro
    ```

* `%rotate`, `%rep` 지시어
  - 매크로 내부에서 반복 작업을 할 때 유용함
  - `%rep`: 특정 코드 블록을 지정한 횟수만큼 반복 생성
    ```
    %rep 5
        db 0    ; 'db 0'이 5번 연속으로 써짐
    %endrep
    ```
  - `%rotate`: 매크로에 전달된 인자들(%1, %2, %3...)의 순서를 한 칸씩 민다. (1이면 오른쪽으로 한 칸 회전, -1이면 왼쪽으로 한 칸 회전)
    ```
    %macro MULTI_PUSH 1-*
        %rep %0
            push %1
            %rotate 1     ; 앞에서부터 차례대로
        %endrep
    %endmacro

    %macro MULTI_POP 1-*
        %rep %0
            %rotate -1    ; 뒤에서부터 거꾸로
            pop %1
        %endrep
    %endmacro

    section .text
    _start:
        MULTI_PUSH rax, rbx, rcx   ; push rax, push rbx, push rcx
        ; ... 어떤 작업 수행 ...
        MULTI_POP  rax, rbx, rcx   ; pop rcx, pop rbx, pop rax (복구 완료!)
    ```

* 오류 메시지 출력을 위한 지시어
  - 관련 지시어 종류는 다음과 같음
    | 지시어 | 의미 | 빌드 중단 여부 | 특징 |
    | ------ | ---- | -------------- | ---- |
    | `%warning` | 경고 | 중단 안함 | 주의만 주고 계속 진행 |
    | `%error` | 오류 | 중단함 (지연) | 발견된 모든 오류를 다 보여주고 종료 |
    | `%fatal` | 치명적인 오류 | 즉시 중단 | 발견 즉시 바로 종료 |
  - 경고 예제
    ```
    %if VERSION < 2
        %warning "이 버전은 곧 지원이 중단될 예정입니다."
    %endif
    ```
  - 오류 예제
    ```
    %macro MY_MACRO 1
        %if %1 > 255
            %error "인자값은 255를 넘을 수 없습니다!"
        %endif
    %endmacro
    ```
  - 치명적인 오류 예제
    ```
    %ifndef REQUIRED_HEADER
        %fatal "필수 헤더 파일이 없습니다. 빌드를 중단합니다."
    %endif
    ```
  - 예제: 매크로 방어 코드
    ```
    %macro PUSH_RAX_ONLY 1
        %ifidni %1, rax    ; 인자가 rax와 같다면 (대소문자 무시)
            push rax
        %else
            %error "이 매크로는 오직 rax만 허용합니다. 입력하신 것: %1"
        %endif
    %endmacro

    _start:
        PUSH_RAX_ONLY rbx  ; 빌드 시 에러 발생!
    ```

* `struc` 매크로: C 언어의 구조체와 유사함
  - 기본 문법은 다음과 같다.
    ```
    struc Person
        .name:   resb 32    ; 32바이트 (이름)
        .age:    resd 1     ; 4바이트 (나이)
        .id:     resq 1     ; 8바이트 (ID)
    endstruc
    ```
  - 예제
    ```
    section .data
        my_friend:
            istruc Person
                at Person.name, db "Alice", 0
                at Person.age,  dd 25
                at Person.id,   dq 12345678
            iend

    ...
    mov eax, [rbx + Person.age]  ; RBX가 구조체의 시작 주소라면, 32바이트(name) 뒤의 age 값을 읽어옴 (이름으로 참조하므로 오프셋 숫자를 매번 안 바꿔도 되서 편리함)
    ```

## 실수 연산

* 앞에서 정수, 문자열을 다루었는데 문자도 실상 정수 데이터임을 감안한다면 지금까지 본 것은 전부 정수 연산/처리만 배운 것이다.
  - 실수 연산은 앞에서 배운 `mov`, `add` 같은 명령어로 수행하기에는 너무 복잡하고 효율적이지 않고 느림 (지수 맞추기, 정규화)
  - 현대 CPU에서는 FPU (Floating Point Unit)라는 전용 연산 장치, 더 나아가 SIMD (Single Instruction Multiple Data) 기술을 이용해 병렬 처리를 수행하여 부동 소수 연산을 고속으로 처리함
    * FPU (Floating Point Unit)의 역할
      - 지수 맞추기 (Alignment): 소수점 위치를 동일하게 맞춤
      - 가수 더하기 (Add): 소수점 앞뒤의 실제 숫자들을 더함
      - 정규화 (Normalization): 결과를 다시 표준 형태(예: 1.xxxx)로 만듦
      - 반올림 (Rounding): 정해진 비트 수에 맞게 끝처리
    * SIMD (Single Instruction Multiple Data) 기술 (FPU보다 더 빠름)
      - SSE (Streaming SIMD Extensions): 128비트 레지스터를 사용하여 여러 개의 소수점 연산을 동시에 처리
      - AVX (Advanced Vector Extensions): 256비트나 512비트 레지스터를 사용하여 한 번에 8~16개의 소수점 연산을 병렬로 처리
  - .data 섹션 예시를 통한 데이터 타입 정의 보기
    ```
    section .data
        ; 1. Single Precision (FP32) - 32비트 (4바이트)
        ; "dd" 지시어를 사용하여 4바이트 상수를 정의
        f32_pi:        dd 3.141592
        f32_one:       dd 1.0

        ; 2. Double Precision (FP64) - 64비트 (8바이트)
        ; "dq" 지시어를 사용하여 8바이트 상수를 정의
        f64_pi:        dq 3.141592653589793
        f64_exp:       dq 2.718281828459

        ; 3. Extended Precision (80-bit) - 80비트 (10바이트)
        ; dt(Ten-byte) 지시어 사용
        f80_exact:     dt 1.234567890123456789

        ; 4. Quadruple Precision (FP128) 또는 SIMD용 데이터
        ; 보통 두 개의 dq나 do(octoword)를 사용하여 비트 단위로 정의
        f128_val1:     dq 0.0, 0.0    ; 16바이트 공간을 0으로 초기화 (2개의 공간)
        f128_val2:     do 0.0         ; 16바이트 공간을 0으로 초기화 (1개의 공간)
    ```
  - .bss 섹션 예시를 통한 데이터 타입 정의 보기
    ```
    section .bss
        ; 1. Half Precision (FP16 / Bfloat16) - 16비트 (2바이트)
        half_val:      resw 1    ; 1개의 word(2바이트) 예약

        ; 2. Single Precision (FP32) - 32비트 (4바이트)
        single_val:    resd 1    ; 1개의 doubleword(4바이트) 예약

        ; 3. Double Precision (FP64) - 64비트 (8바이트)
        double_val:    resq 1    ; 1개의 quadword(8바이트) 예약

        ; 4. Extended Precision (80-bit) - 80비트 (10바이트)
        extended_val:  rest 1    ; 1개의 ten-byte(10바이트) 예약

        ; 5. Quadruple Precision (FP128) - 128비트 (16바이트)
        quad_val:      reso 1    ; 1개의 octoword(16바이트) 예약
    ```

### 실수 연산자 종류

* 실수 및 벡터 레지스터는 다음과 같다.

| 이름 | 크기 | 용도 |
| ---- | ---- | ---- |
| XMM0 ~ XMM15 | 16바이트 (128비트) | SSE 명령어, 실수 연산, 짧은 벡터 연산 |
| YMM0 ~ YMM15 | 32바이트 (256비트) | AVX 명령어 전용 (XMM을 포함하는 확장 형태) |
| ZMM0 ~ ZMM31 | 64바이트 (512비트) | AVX-512 전용 (최신 고성능 CPU) |

* 정수/실수 연산자를 대조해서 표로 다음과 같이 요약됨
  - 접미어 ss: Scalar Single (32비트 float 하나)
  - 접미어 sd: Scalar Double (64비트 double 하나)
  - 접미어 ps: Packed Single (128비트 안에 float 4개를 동시에 연산)
  - 접미어 pd: Packed Double (128비트 안에 double 2개를 동시에 연산)
  - 접두사 f: x87 FPU (80비트 하나)

| 정수 명령어 | Single (32비트/4바이트) | Double (64비트/8바이트) | Extended (80비트/10바이트) | 설명 |
| ----------- | ----------------------- | ----------------------- | -------------------------- | ---- |
| `mov`       | `movss`                 | `movsd`                 | `fld` (Load) / `fst` (Store) | 값을 레지스터/메모리로 복사 |
| `add`       | `addss`                 | `addsd`                 | `fadd`                     | 덧셈 |
| `sub`       | `subss`                 | `subsd`                 | `fsub`                     | 뺄셈 |
| `mul`       | `mulss`                 | `mulsd`                 | `fmul`                     | 곱셈 |
| `div`       | `divss`                 | `divsd`                 | `fdiv`                     | 나눗셈 |
| `cmp`       | `ucomiss` / `comiss`    | `ucomisd` / `comisd`    | `fcomi`                    | 비교 (flag 업데이트) |

* 예제
  ```
  section .data
      f_val1  dd  1.5  ; Single
      f_val2  dd  2.5
      d_val1  dq  1.5  ; Double
      d_val2  dq  2.5

  section .text
  _start:
      ; --- Single Precision 연산 (float) ---
      movss xmm0, [f_val1]     ; xmm0 = 1.5
      addss xmm0, [f_val2]     ; xmm0 = 1.5 + 2.5
      ucomiss xmm0, [f_val1]   ; xmm0와 f_val1 비교
      ja .greater              ; xmm0 > f_val1 이면 점프

      ; --- Double Precision 연산 (double) ---
      movsd xmm1, [d_val1]     ; xmm1 = 1.5
      mulsd xmm1, [d_val2]     ; xmm1 = 1.5 * 2.5
  ```

### 데이터 전송 명령어

* XMM 레지스터에 저장하는 명령어
  - `movss`: Moves a single-precision floating-point value between the XMM registers or between an XMM register and memory. (... 피연산자에 대한 설명 추가)
  - `movsd`: Moves double-precision floating-point value between the XMM registers or between an XMM register and memory. (... 피연산자에 대한 설명 추가)
  - ...
  - `movdqd`
  - `movaps`
  - `movups`
  - ...
  - `movhlps`: Moves two-packed single-precision floating-point values from the high quadword of an XMM register to the low quadword of another XMM register. (... 피연산자에 대한 설명 추가)
  - `movlhps`: Moves two-packed single-precision floating-point values from the low quadword of an XMM register to the high quadword of another XMM register. (... 피연산자에 대한 설명 추가)
  - `fld`
  - `fst`

### 연산 명령어

* 산술 연산 (... 그 외 연산자들은?)
  - `addss`: Adds single-precision floating-point values. (... 피연산자에 대한 설명 추가)
  - `addsd`: Adds double-precision floating point values. (... 피연산자에 대한 설명 추가)
  - `addps`
  - `addpd`
  - `paddd`
  - `paddb`
  - `vaddps`
  - `vaddpd`
  - `subss`: Subtracts single-precision floating-point values. (... 피연산자에 대한 설명 추가)
  - `subsd`: Subtracts double-precision floating-point values. (... 피연산자에 대한 설명 추가)
  - ...
  - `mulss`: Multiplies single-precision floating-point values. (... 피연산자에 대한 설명 추가)
  - `mulsd`: Multiplies double-precision floating-point values. (... 피연산자에 대한 설명 추가)
  - ...
  - `divss`: Divides single-precision floating-point values. (... 피연산자에 대한 설명 추가)
  - `divsd`: Divides double-precision floating-point values. (... 피연산자에 대한 설명 추가)
  - ...
  - `sqrtss`
  - `sqrtsd`
  - ...
  - `maxss`
  - `maxsd`
  - ...
  - `minss`
  - `minsd`
  - ...
  - `pmaxs(q|d|w|b)`
  - `pmaxu(q|d|w|b)`
  - `pmins(q|d|w|b)`
  - `pminu(q|d|w|b)`
  - `round` ???
  - `fadd`
  - `fiadd`
  - `fsub`
  - `fisub`
  - `fimul`
  - `fidiv`
  - `fabs`

### 분기 명령어

* 비교 연산
  - `cmpss`: 1번째 피연산자와 2번째 피연산자와의 관계를 3번째 피연산자에 저장함
    | 3번째 피연산자 값 | 의미 |
    | ----------------- | ---- |
    | 0 | == |
    | 1 | < |
    | 2 | <= |
    | 3 | 둘 중 하나의 피연산자가 NaN (Not a Number) |
    | 4 | != |
    | 5 | >= |
    | 6 | > |
    | 7 | 두 피연산자 모두 NaN (Not a Number) |





... 고급기술 -- https://github.com/0xAX/asm/blob/master/content/asm_7.md

... NASM -- https://www.nasm.us/doc/nasm01.html
