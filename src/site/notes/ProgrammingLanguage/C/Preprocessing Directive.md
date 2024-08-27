---
{"dg-publish":true,"tags":["C","프로그래밍언어","기초","전처리"],"permalink":"/ProgrammingLanguage/C/Preprocessing Directive/","dgPassFrontmatter":true,"created":"2024-08-01T01:32:10.000+09:00","updated":"2024-08-02T18:57:44.093+09:00"}
---



# 전처리기 지시자

> 전처리*Preprocessing*은 C와 C++의 컴파일 파이프라인의 첫번째 단계로, 컴파일로 보내기 전에 소스 코드를 만들고 수정할 수 있도록 하는 과정입니다. 전처리 과정에서 처리되어야 할 코드는 일련의 지시자*Directive*가 맨 앞에 붙어 있으며 이 지시자를 통해 C 전처리기의 수행을 통제하고 영향을 줄 수 있습니다. 이번 글에서는 전처리기 지시자에 어떤 종류가 있고 이를 활용해 무엇을 할 수 있는지 알아봅니다.

---

## 전처리기 지시자

C의 지시자는 모두 `#`으로 시작한다. 

+ `#`
+ `##`
+ `#define`
+ `#error`
+ `#include`
+ `#if`
+ `#ifdef`
+ `#ifndef`
+ `#pragma`
+ `#undef`
+ ...

그 중 전처리기 지시자는 크게 매크로*macro*를 정의할 때 사용하는 전처리기 지시자와 조건부 컴파일*conditional compile*에 사용하는 전처리기 지시자로 나눌 수 있고, 이 외에도 다른 파일의 내용을 가져오는 `#include`나 컴파일 과정에서 사용할 정보를 전달하기 위한 `#error`나 `#pragma` 등이 있다.

### `#include`

표준 입출력과 같이 수많은 소스 코드에서 공통적으로 사용하는 코드를 `.h`확장자를 가진 헤더 파일*header file* 안에 모은 다음, 사용하고자 하는 파일에 포함시킬 때 `#include` 지시자를 사용해 헤더 파일의 내용을 불러오는 것이 C/C++로 작성된 모든 프로젝트의 기본 틀이다. 전처리 과정을 거치고 나면 포함하려는 파일의 코드가 그대로 복사된다.

### 매크로

C에서 매크로는 이름을 가진 코드다. 모든 매크로는 이름과 사용 가능한 매개변수*parameter* 리스트, 그리고 값을 가지고, 매크로 확장 단계를 통해 매크로의 이름은 전처리 단계에서 매크로의 실제 코드로 치환된다. 

#### 매크로 정의하기

매크로는 `#define` 지시자로 정의한다. 정의된 매크로는 `#undef` 지시자 이후로 이어지는 코드에서는 사용할 수 없기 때문에, `#define`과 `#undef`는 매크로 정의가 유효한 범위를 결정한다.

매크로의 가장 기본적인 사용은 프로그램 내에서 자주 이용하는 값이나 코드를 매크로로 교체하는 것이다.

```C
#define PI 3.141592
```

이 매크로가 유효한 모든 곳에서 `PI`는 3.141592와 똑같다. 정확히는, 전처리 단계에서 `PI`가 3.141592로 대체된다. 실제 값을 사용하는 대신 이렇게 매크로를 사용하면 몇가지 장점이 따라온다.

+ 다양한 곳에서 공통으로 사용하는 값이나 코드를 수정하기 쉽다.
+ 코드가 이전에 비해 읽기 쉬워진다.

#### 유사 함수 매크로

전처리 단계에서 매크로가 그 값으로 대체된다는 점을 이용하면, 특정 값을 대체하는 용도를 넘어 C 함수를 새로 작성하지 않고도 실질적으로 함수를 사용하는 것과 거의 같은 효과를 낼 수 있다.

```C
#define ADD(a, b) a + b
```

이 코드는 함수가 아니고 인자*argument*를 받는 유사 함수 매크로*function-like macro*다. 작성하는 입장에서는 함수처럼 사용할 수 있지만 전처리 단계에서 실제 코드로 치환되고 나면 컴파일러 입장에서는 함수가 아니기 때문에 유사 함수 매크로는 실제 함수처럼 메모리 주소를 따로 가지지 않는다. 이는 실제 컴파일 이후 프로그램의 실행 과정에서 함수의 메모리 주소로 이동했다가 함수가 종료되면 다시 복귀하는 과정이 없다는 것을 뜻하기 때문에, 간단한 함수를 쓸 때 실제로 함수를 사용하는 대신 유사 함수 매크로를 사용하면 프로그램의 실행 속도를 조금이나마 높일 수 있다.

#### `#`와 `##`

```C
// File name: ExtremeC_exampels_chapter1_4.c
// Description: Example 1.4

#include <stdio.h>
#include <string.h>

#define CMD(NAME) \
  char NAME ## _cmd[256]  = ""; \
  strcpy(NAME ## _cmd, #NAME);

int main(int argc, char** argv) {

  CMD(copy)
  CMD(paste)
  CMD(cut)

  char cmd[256];
  scanf("%s", cmd);

  if (strcmp(cmd, copy_cmd) == 0) {
    // ...
  }
  if (strcmp(cmd, paste_cmd) == 0) {
    // ...
  }
  if (strcmp(cmd, cut_cmd) == 0) {
    // ...
  }

  return 0;
}
```

+ `#`은 전처리기를 조작할 수 있게 해주는 기호인 동시에 매개변수에 적용되는 연산자이기도 하다. `#` 연산자는 매개변수를 한 쌍의 따옴표로 둘러싼 문자 형태로 변환한다.
+ `##`은 매개변수를 다른 문자열과 결합시키는 연산자다. 예시 코드에서는 `NAME ## _cmd`를 주목해야 하는데, `##`이 앞의 매개변수 `NAME`과 뒤쪽의 `_cmd`를 결합하기 때문이다. 그래서 이 코드가 전처리 과정을 거치면 아래처럼 변한다.

```C
// (중략)

int main(int argc, char** argv) {

  char copy_cmd[256] = ""; strcpy(copy_cmd, "copy");
  char paste_cmd[256] = ""; strcpy(paste_cmd, "paste");
  char cut_cmd[256] = ""; strcpy(cut_cmd, "cut");

  char cmd[256];
  scanf("%s", cmd);

  if (strcmp(cmd, copy_cmd) == 0) {
    // ...
  }
  if (strcmp(cmd, paste_cmd) == 0) {
    // ...
  }
  if (strcmp(cmd, cut_cmd) == 0) {
    // ...
  }

  return 0;
}
```

#### 가변 인자 매크로

가변 인자 매크로*variadic macro*를 통해 길이가 정해지지 않은 가변 인자*variable argument*에도 대응할 수 있다. 새로 등장하는 식별자인 `__VA_ARGS__`는 매크로에 들어가는 인수의 개수가 고정되어 있지 않을 때 유용하다. 아직 다른 매개변수에 할당되지 않은 나머지 입력 인수는 매크로 화확장 단계에서 `__VA_ARGS__`가 있던 자리에 들어간다.

```C
// File name: ExtremeC_exampels_chapter1_5.c
// Description: Example 1.5

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define VERSION "2.3.4"

#define LOG_ERROR(format, ...) fprintf(stderr, format, __VA_ARGS__)

int main(int argc, char** argv) {

  if (argc < 3) {
    LOG_ERROR("Invalid number of arguments for version %s\n.", VERSION);
    exit(1);
  }

  if (strcmp(argv[1], "-n") != 0) {
    LOG_ERROR("%s is a wrong param at index %d for version %s.", argv[1], 1, VERSION);
    exit(1);
  }

  // ...

  return 0;
}
```

그래서 `LOG_ERROR` 매크로에서 `...`에 해당하는 나머지 인자는 매크로 확장 단계에서 `__VA_ARGS__`를 대신한다.

#### 루프 풀기

매크로를 통해 반복문을 만들 수도 있다.

```C
// File name: ExtremeC_exampels_chapter1_3.c
// Description: Example 1.3

#include <stdio.h>

#define PRINT(a) printf("%d\n", a);
#define LOOP(v, s, e) for (int v = s; v <= e; v++) {
#define ENDLOOP }

int main(int argc, char** argv) {
	LOOP(counter, 1, 10)
		PRINT(counter)
	ENDLOOP
	return 0;
}
```

전처리 과정을 거치고 나면 정상적인 반복문이 작성된 코드를 얻게 되며 문제없이 컴파일된다.

```C
... (대충 stdio.h의 내용) ...

int main(int argc, char** argv) {
	for(int counter = 1; counter <= 10; counter++) {
		printf("%d\n", counter);
	}
	return 0;
}
```

앞서 살펴본 가변 인자 매크로와 매크로를 통한 반복문 구현을 결합하면 반복문을 전처리 과정에서 풀 수도 있다. 반복문이 실제 명령어의 반복으로 대체되는 것이다. 루프 풀기*loop unrolling*로도 알려져 있는 이 방법은 이진 파일의 크기를 키우는 대신 실행 속도를 개선시킨다.

```C
// File name: ExtremeC_exampels_chapter1_6.c
// Description: Example 1.6

#include <stdio.h>

#define LOOP_3(X, ...) \
  printf("%s\n", #X);

#define LOOP_2(X, ...) \
  printf("%s\n", #X); \
  LOOP_3(__VA_ARGS__)

#define LOOP_1(X, ...) \
  printf("%s\n", #X); \
  LOOP_2(__VA_ARGS__)

#define LOOP(...) \
  LOOP_1(__VA_ARGS__)

int main(int argc, char** argv) {

  LOOP(copy paste cut) // printf("%s\n", "copy paste cut"); printf("%s\n", ""); printf("%s\n", "");
  LOOP(copy, paste, cut) // printf("%s\n", "copy"); printf("%s\n", "paste"); printf("%s\n", "cut");
  LOOP(copy, paste, cut, select)

  return 0;
}
```

이 코드는 전처리 단계를 거치고 나면 `printf`만 남기 때문에 반복문이 없다. 만약 소프트웨어 설계 시 구조가 유지되어야 한다면 이런 방식의 매크로 활용은 좋은 선택이 아니다. 설령 소스 코드가 소프트웨어 설계를 반영한다고 한들, 전처리 단계를 거치고 나면 매크로가 확장되면서 컴파일러가 읽는 코드에서는 기존의 구조가 유실될 수 있어서 실제 프로그램이 설계를 온전히 구현하지 못하는 문제가 발생할 수 있기 때문이다.

### 조건부 컴파일

조건부 컴파일*conditional compilation*은 C의 또다른 고유한 특성으로, 조건부 컴파일을 거치고 나면 조건에 따라 코드가 달라질 수 있다. 다만 이름과 달리 컴파일러 자체에서 조건에 따른 어떤 작업이 이루어지는 것은 아니고, 컴파일러가 읽게 되는 전처리된 코드가 조건에 따라 달라지는 것이다.

#### 전처리 단계의 조건문

물론 전처리 단계에서도 일반적인 조건문을 작성할 수 있다. `#if` - `#elif` - `#else` - `#endif`를 통해 `if` - `else if`- `else`문과 동일한 구조를 가지면서 컴파일 이전에 코드가 결정되는 제어문을 작성할 수 있는데, 이때 사용하는 조건문의 값[^1]은 0을 기준으로 참과 거짓으로 나뉜다.

+ `#if`의 조건이 참일 경우 `#if`부터 첫 `#elif` 앞까지 살아남고 그 이후부터 `#endif`까지는 전처리 과정에서 사라진다.
+ `#elif`의 조건이 참일 경우 다음 `#elif`나 `#elif`가 더 없는 경우 `#else` 앞까지 살아남고 나머지는 전처리 과정에서 사라진다.
+ `#else`의 조건이 참일 경우 `#else`부터 `#endif`까지 살아남고 나머지는 전처리 과정에서 사라진다.

[^1]: 일반적으로 C에서는 구문이 값을 반환한다. 예를 들어, `a<b`는 a가 b보다 작을 때 1의 값을 가지고 그렇지 않을 때 0의 값을 가지며, 입력 함수 `scanf`는 입력된 데이터의 길이를 반환한다.

또한 전처리 단계에서는 매크로의 정의가 조건이 되어 어떤 매크로의 정의 여부에 따라 특정 구간의 코드가 최종 결과에 나올 수도 있고 나오지 않을 수도 있다. 특정 매크로가 정의되어 있다면 `#ifdef`부터 `#endif` 사이의 모든 코드는 전처리 이후에도 남고, `#ifndef`부터 `#endif`는 전처리 과정에서 사라진다.

#### 헤더 가드

`#ifdef`, `#ifndef`, `#endif`, `#define`을 사용해 특정 코드를 컴파일러가 무시하도록 하는 이 패턴은 일반적으로 헤더 가드*header guard*라고 불리는, 헤더 파일*header file*의 이중 포함*double inclusion*을 막는 구문에서 사용한다. 이 구문은 전처리 단계에서 같은 헤더 파일이 두 번 포함되는 것을 방지하는데, C나 C++로 이루어진 프로젝트는 거의 다 `#ifndef`로 시작한다고 생각해도 될 만큼 헤더 가드는 매우 일반적인 디자인이며 모든 컴파일러에서 작동한다.

```C
// File name: ExtremeC_exampels_chapter1_8.c
// Description: Example 1.8

#ifndef EXAMPLE_1_8_H
#define EXAMPLE_1_8_H

void say_hello();
int read_age();

#endif
```


### 오류 메시지 설정하기

컴파일 과정에서 오류가 발생하면 컴파일러는 자동으로 메시지를 만들어 출력한다. 이 외에 자동으로 잡아주지 않지만 오류로 간주해야 하는 상황이 있을 수 있다. 이때  `#error`를 사용하면 컴파일러가 새로운 오류를 감지하고 컴파일을 멈추는 것처럼 만들 수 있다.

```C
// C program to demonstrate the use of error directive to display custom error message.

#include <stdio.h>

// #define GeeksforGeeks  // not Defining the symbol
// GeeksforGeeks

#ifndef GeeksforGeeks
#error GeeksforGeeks not found!
#endif

int main() {
    printf("Hello, GeeksforGeeks!\n");
    return 0;
}
```

`#error`를 만나면 컴파일러는 자동으로 컴파일러를 중단하고 지시자 뒤에 이어지는 메시지를 출력한다. 그렇기 때문에 `#error`를 이용해 새로운 오류를 만들어내는 과정은 `#error`를 오류에 해당하는 상황에서만 노출되도록 만드는 과정이다. 예시 코드처럼 `#ifndef`-`#endif`와 같은 조건부 컴파일에 관한 지시자들을 사용하는 것은 이런 이유에서다.

### 사전 정보 제공

컴파일 과정에서 컴파일러가 어떤 경고를 무시하게 하거나 어떤 메시지를 띄우게 하고 싶다면 `#pragma` 지시자를 사용하면 된다. 이 지시자를 사용하는 상황은 대표적으로 세 가지다.

#### 메시지 출력

`#pragma`*message*는 컴파일 과정에서 어떤 메시지를 컴파일러가 출력하도록 하는 방법이다. *message*의 자리에 원하는 문자열을 넣으면 컴파일러가 컴파일 과정에서 메시지를 출력해준다.

#### 특정 경고 활성화/무시

`#pragma`*warning*은 컴파일러의 특정 경고를 활성화하거나 무시할 때 사용한다. 주어진 어떤 상황을 컴파일러가 무시하고 지나갈 수도 있고, 아니면 오류가 발생했다고 알리며 컴파일을 멈출 수도 있다. 이런 경고는 보통 수로 표시되어 있어 이 수를 통해 연결된 경고를 키거나 끌 수 있다. 가장 대표적인 예시는 마이크로소프트의 통합 개발 환경(IDE) 비주얼 스튜디오(Visual Studio)에서 `scanf`처럼 보안상 안전하지 않은[^2] 함수를 사용할 때 발생하는 C4996 오류를 무시하기 위한 `#pragma warning(disable:4996)`이다.

[^2]: 보안상 안전하지 않다는 이유로 `printf`나 `scanf`같은 보편적인 입출력 함수를 제대로 사용하지 못하는 상황은 특히 초심자들에게 좋은 상황은 아니다.

#### 헤더 가드

간편함과 속도 향상을 이유로 헤더 가드를 위한 코드를 작성할 때 `#ifndef` - `#define` - `#endif` 패턴 대신 `#pragma once`를 사용하는 경우도 적지 않다. `#pragma once`를 사용한 파일은 컴파일 당 오직 한 번만 포함될 수 있기 때문에, 만약 `#include` 지시자를 통해 `#pragma once`가 있는 헤더 파일을 컴파일 과정에서 두 번 부르게 된다면 컴파일 에러가 발생한다. 다만 `#pragma once`를 사용할 때는 이 지시자를 지원하지 않는 컴파일러가 있다는 점[^3]에 유의해야 한다.

```C
#pragma once

void say_hello();
int read_age();
```

[^3]: GCC나 MSVC++처럼 잘 알려진 컴파일러들도 구버전에서는 사용하지 못할 수도 있으며, SDCC처럼 지원하지 않는 컴파일러도 있다.

---

## 참고 자료 & 더보기

### 참고 자료
+ Amini, K. (2022). *전문가를 위한 C* (박지윤, Trans.; 1st ed.). 한빛미디어.
+ [레퍼런스](https://www.cprogramming.com/reference/preprocessor/)
+ [\#pragma once](https://en.wikipedia.org/wiki/Pragma_once)
+ [*전문가를 위한 C* 공식 레포지토리](https://github.com/PacktPublishing/Extreme-C/tree/master)
+ [C Preprocessor Directives](https://www.geeksforgeeks.org/cpp-preprocessor-directives-set-2/?ref=lbp)

### 더보기
+ [\#pragma 사용법 정리](https://hizstory.tistory.com/45)
+ [[ProgrammingLanguage/C/Preprocessing\|전처리]]
+ [[ProgrammingLanguage/C/Phases_of_Translation\|컴파일 과정에 대한 개요]]