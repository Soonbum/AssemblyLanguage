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

## 레지스터의 종류 및 접두사/접미사에 따른 사이즈

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
| RFLAGS | EFLAGS | FLAGS  | -          | -          | ??? |
| R8     | R8D    | R8W    | -          | R8B        | 범용, x64에 추가됨 |
| R9     | R9D    | R9W    | -          | R9B        | 범용, x64에 추가됨 |
| R10    | R10D   | R10W   | -          | R10B       | 범용, x64에 추가됨 |
| R11    | R11D   | R11W   | -          | R11B       | 범용, x64에 추가됨 |
| R12    | R12D   | R12W   | -          | R12B       | 범용, x64에 추가됨 |
| R13    | R13D   | R13W   | -          | R13B       | 범용, x64에 추가됨 |
| R14    | R14D   | R14W   | -          | R14B       | 범용, x64에 추가됨 |
| R15    | R15D   | R15W   | -          | R15B       | 범용, x64에 추가됨 |

* 여기서 
