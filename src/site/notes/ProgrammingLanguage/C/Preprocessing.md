---
{"dg-publish":true,"permalink":"/programming-language/c/preprocessing/","tags":["C","프로그래밍언어","기초","전처리"],"created":"2024-03-15T12:32:38.847+09:00","updated":"2024-06-27T22:37:49.628+09:00"}
---


# 전처리

> 전처리*Preprocessing* [^1][^2]는 C의 강력한 구성요소이며, C를 다른 프로그래밍 언어와 구분시키는 수많은 특징 중 하나입니다. C로 작성된 소스 코드에서 전처리기 지시자*Preprocessor Directive* 가 포함된 코드가 전처리 과정과 관련되어 있습니다. 전처리는 전처리기*Preprocessor* 가 담당하고, 전처리 과정을 거치면 소스 코드에서 `#`으로 시작하는 코드들이 전부 처리되어 컴파일러가 직접 컴파일할 수 있는 컴파일 단위*Compilation Unit* / 변환 단위*Translation Unit* 가 만들어집니다. 이 섹션에서는 전처리를 위한 여러 수단과 그 과정 및 결과 등 전처리에 대한 본격적인 내용을 다룹니다.

[^1]: 전처리기*preprocessor* 라는 개념은 비단 C가 아니더라도 컴퓨터공학 전반에서 사용되는 개념이다. 입력된 데이터를 다른 프로그램의 입력으로 사용할 데이터로 바꿔 출력하는 프로그램이라면 무엇이든 전처리기라 부를 수 있고, 이때 전처리기가 출력하는 데이터는 전처리기가 입력된 데이터의 전처리된 형태*preprocessed form* 다. 이번 글에서 다루는 C의 전처리 과정은 전처리의 특수한 상황에 해당하며, 일반적인 전처리기와 구분하기 위해 C의 전처리기를 C 전처리기*C preprocessor* 로 칭한다.
[^2]: C의 전처리 과정으로 한정해서 다룰 것이다.

---

## 전처리란 무엇인가?

C로 작성된 소스 코드*source code* 에서는  `#`이 빠지지 않는다. 대표적으로 `#include <stdio.h>` 가 있다. 그런데 이 코드는 엄밀히 말하자면 C 코드가 아니다. C언어로 작성된 코드를 해석하는 일은 컴파일러*compiler*의 역할인데, 컴파일러는  `#`로 시작하는 코드를 해석하지 못한다.[^3]  `#`로 시작하는 C 전처리기 지시자*C preprocessor directive* 를 해석하는 것은 다른 프로그램의 몫이고, 컴파일러는 그 프로그램에 의해 해석된 결과가 반영되어 내용이 바뀐 코드를 해석한다. 소스 코드가 컴파일러로 넘어가기 전에 C 전처리기 지시자를 읽고 처리해 주는 이 프로그램이 바로 C 전처리기*C preprocessor* 이며, 소스 코드를 다듬는 과정이 바로 C의 전처리 과정이다.

[^3]: 이론상 컴파일러는 전처리기와 거의 독립적인 문법을 사용하고 있어서  `#`로 시작하는 C 전처리기 지시자를 해석하지 못하지만, 현대의 C 컴파일러는 대부분 C 전처리기 지시자를 해석할 수 있고 이를 통해 전처리 단계에서 발생하는 오류를 검출할 수 있다.

### 전처리 과정의 구체적인 단계

구체적으로 전처리 과정은 C의 컴파일 파이프라인이라고 할 수 있는 [Phases of Translation](https://en.cppreference.com/w/c/language/translation_phases) 중 1~4단계에 해당한다.

각 단계는 다음과 같다:

#### 1단계

우리가 작성한 소스 코드는 기본적으로 UTF-8이나 EUC-KR처럼 각 문자가 특정한 방법에 따라 인코딩*encoding* 되어 있다. 우선 전처리기에서는 컴파일러가 소화할 수 있도록 이 소스 코드의 각 바이트를 소스 문자 집합*source character set* 에 속하는 문자로 변환한다. 

##### Source Character Set

소스 문자 집합은 다음 96개의 문자로 구성된다: 
+ 스페이스, 수평 탭, 수직 탭[^4], 페이지 나누기*form feed*, 줄 바꿈*new-line*[^5]
+ `'0'`부터 `'9'`까지의 숫자 문자들 10개
+ 총 52개의 알파벳 대문자와 소문자 : `'a'`~`'z'`, `'A'`~`'Z'`
+ 29개의 구두점 문자*punctuation characters* : `_ { } [ ] # ( ) < > % : ; . ? * + - / ^ & | ~ ! = , \ " '`

이때 추가로 삼중자*trigraph sequence*[^6]를 변환하게 되는데, 이 과정은 C++17부터 C++에서 없어졌으며, C23을 마지막으로 C에서도 없어질 예정이다.

[^4]: [이 글](https://stackoverflow.com/questions/3380538/what-is-a-vertical-tab) 참고. 과거 수직으로 공백을 표시해야 할 때 유용하게 사용했다고 한다.
[^5]: 이들은 모두 공백 문자*whitespace characters* 에 속한다.
[^6]: 삼중자와 그 변환 결과에 대한 내용은 [이 글](https://en.cppreference.com/w/c/language/operator_alternative)의 Trigraphs 섹션을 참조.

#### 2단계

이 단계에서는 `\`(역슬래시, backslash)[^7]가 등장할 때마다 이 뒤에 이어지는 줄바꿈과 같이 없애는 과정을 통해 소스 코드 상에서 두 줄로 나뉘어 있던 코드를 한 줄로 합친다.

[^7]: `\`는 소스 코드를 작성할 때 가독성과 같은 이유로 논리상 한 줄로 쓰여야 하는 코드를 부득이하게 여러 줄로 쪼개야 할 때 사용한다.

#### 3단계

이제 소스 파일은 주석*comments*, 공백 문자들*sequences of whitespace characters*, 그리고 전처리 토큰*preprocessing tokens* 으로 분해된다.

##### Preprocessing Tokens

전처리 토큰은 전처리기가 코드를 해석하는 단위며, 그 종류는 다음과 같다:

+ `<stdio.h>` 나 `"myheader.h"`와 같은 헤더 파일의 이름이나 식별자*identifier*[^8]
+ 스트링 리터럴*string literals*, 상수*constant* 와 같이 전처리 대상에 해당하는 데이터
+ 연산자와 구두점*punctuator*
+ 그 외 공백이 아닌*non-whitespace* 다른 문자들

[^8]: C의 식별자에는 함수, 구조체, 매크로 등이 포함된다. 식별자에 대한 자세한 내용은 [이 글](https://en.cppreference.com/w/c/language/identifier)을 참조.

모든 주석은 공백 한 칸으로 대체되며, 줄 바꿈은 유지되지만, 나머지 공백 문자들이 어떻게 될지는 환경에 따라 다르다.

#### 4단계

마지막 단계에서 `#include` 지시자와 함께 사용된 파일들 또한 1~4단계를 거친다. 이 과정을 통해 소스 코드 전체에서 전처리기 지시자는 사라지고 컴파일러에 입력되는 하나의 논리 단위가 완성되며 이것을 컴파일 단위*compile unit* 또는 변환 단위*translation unit* 이라 부른다.

### 실제 전처리 과정 살펴보기

```C
// File name: ExtremeC_examples_chapter2_1.h
// Description: Contains the declaration needed for the 'avg' function

#ifndef EXTREMEC_EXAMPLES_CHAPTER_2_1_H
#define EXTREMEC_EXAMPLES_CHAPTER_2_1_H

typedef enum {
    NONE,
    NORMAL,
    SQUARED
} average_type_t;

// Function declaration
double avg(int*, int, average_type_t);

#endif
```

```C
// File name: ExtremeC_examples_chapter2_1_main.c
// Description: Contains the 'main' function

#include <stdio.h>
#include "ExtremeC_examples_chapter2_1.h"

int main(int argc, char** argv) {
    // Array declaration
    int array[5];
    
    // Filling the array
    array[0] = 10;
    array[1] = 3;
    array[2] = 5;
    array[3] = -8;
    array[4] = 9;
    
    // Calculating the averages using the 'avg' function
    double average = avg(array, 5, NORMAL);
    printf("The average: %f\n", average);
    
    average = avg(array, 5, SQUARED);
    printf("The squared average: %f\n", average);
    
    return 0;
}
```

```C
// File name: ExtremeC_examples_chapter2_1.c
// Description: Contains the definition of the function 'avg'

#include "ExtremeC_examples_chapter2_1.h"

double avg(int* array, int length, average_type_t type) {
    if (length <= 0 || type == NONE) {
        return 0;
    }
    double sum = 0.0;
    for (int i = 0; i < length; i++) {
        if (type == NORMAL) {
            sum += array[i];
        } else if (type == SQUARED) {
            sum += array[i] * array[i];
        }
    }
    return sum / length;
}
```

다음과 같이 함수와 자료형을 선언한 파일`ExtremeC_examples_chapter2_1.h`, 함수를 사용하는 main 함수가 있는 파일`ExtremeC_examples_chapter2_1_main.c`, 함수를 정의한 파일`ExtremeC_examples_chapter2_1.c`이 따로 분리되어 있는 프로젝트가 오늘의 실험 대상이다. 전처리기를 직접 사용하는 방법이 있고 컴파일러를 통해 우회적으로 사용하는 방법이 있는데, 두 방법 모두 프로젝트를 빌드하기 전에 우선 전처리의 결과만을 볼 수 있다. 

#### gcc -E

대표적인 컴파일러인 GCC*GNU Compiler Collection* (이하 gcc)는 전처리를 위해 GNU C 전처리기를 사용한다. 여기서는 `ExtremeC_examples_chapter2_1.c`만을 전처리할 예정이다. gcc는 원래 컴파일을 위해 사용하지만, 전처리가 컴파일의 일부분이기 때문에 이를 이용해서 전처리 결과만을 확인할 수 있는데, 이것이 바로 gcc 컴파일러의 `-E` 옵션이다. 이 옵션을 통해 터미널에 gcc 명령을 입력하면 gcc는 전처리 이후 그 결과를 변환 단위로 터미널에 출력한다.

전처리 결과는 다음과 같다.

```text
revenantonthemission@MacBook-Pro-2 PS % gcc -E ExtremeC_examples_chapter2_1.c
# 1 "ExtremeC_examples_chapter2_1.c"
# 1 "<built-in>" 1
# 1 "<built-in>" 3
# 418 "<built-in>" 3
# 1 "<command line>" 1
# 1 "<built-in>" 2
# 1 "ExtremeC_examples_chapter2_1.c" 2



# 1 "./ExtremeC_examples_chapter2_1.h" 1







typedef enum {
  NONE,
  NORMAL,
  SQUARED
} average_type_t;


double avg(int*, int, average_type_t);
# 5 "ExtremeC_examples_chapter2_1.c" 2

double avg(int* array, int length, average_type_t type) {
  if (length <= 0 || type == NONE) {
    return 0;
  }
  double sum = 0.0;
  for (int i = 0; i < length; i++) {
    if (type == NORMAL) {
      sum += array[i];
    } else if (type == SQUARED) {
      sum += array[i] * array[i];
    }
  }
  return sum / length;
}
revenantonthemission@MacBook-Pro-2 PS % 
```

#### clang -E

gcc 컴파일러의 `-E` 옵션은 clang 컴파일러에서도 동일한 기능을 수행한다. 이 옵션을 통해 터미널에 clang 명령을 입력하면 clang은 전처리 이후 그 결과를 변환 단위로 터미널에 출력한다.

clang 컴파일러의 실행 환경은 다음과 같다.

```text
revenantonthemission@MacBook-Pro-2 PS % clang -v
Homebrew clang version 17.0.6
Target: arm64-apple-darwin23.4.0
Thread model: posix
InstalledDir: /opt/homebrew/opt/llvm/bin
revenantonthemission@MacBook-Pro-2 PS % 
```

그리고 전처리 결과는 다음과 같다.

```text
revenantonthemission@MacBook-Pro-2 PS % clang -E ExtremeC_examples_chapter2_1.c
# 1 "ExtremeC_examples_chapter2_1.c"
# 1 "<built-in>" 1
# 1 "<built-in>" 3
# 420 "<built-in>" 3
# 1 "<command line>" 1
# 1 "<built-in>" 2
# 1 "ExtremeC_examples_chapter2_1.c" 2



# 1 "./ExtremeC_examples_chapter2_1.h" 1







typedef enum {
  NONE,
  NORMAL,
  SQUARED
} average_type_t;


double avg(int*, int, average_type_t);
# 5 "ExtremeC_examples_chapter2_1.c" 2

double avg(int* array, int length, average_type_t type) {
  if (length <= 0 || type == NONE) {
    return 0;
  }
  double sum = 0.0;
  for (int i = 0; i < length; i++) {
    if (type == NORMAL) {
      sum += array[i];
    } else if (type == SQUARED) {
      sum += array[i] * array[i];
    }
  }
  return sum / length;
}
revenantonthemission@MacBook-Pro-2 PS % 
```

#### cpp

대부분의 유닉스 계열 운영체제에는 cpp*C Pre-Processor* 라는 도구가 있는데, 유닉스 계열 운영체제에 포함된 C 개발 번들의 일부이며 C 파일을 전처리할 때 사용할 수 있다. 앞서 살펴본 gcc와 같은 C 컴파일러가 전처리 과정에서 사용하는 도구다. 보통은 컴파일러가 백그라운드에서 사용하지만, 아래와 같이 직접 사용할 수도 있다.

```text
revenantonthemission@MacBook-Pro-2 PS % cpp ExtremeC_examples_chapter2_1.c
# 1 "ExtremeC_examples_chapter2_1.c"
# 1 "<built-in>" 1
# 1 "<built-in>" 3
# 417 "<built-in>" 3
# 1 "<command line>" 1
# 1 "<built-in>" 2
# 1 "ExtremeC_examples_chapter2_1.c" 2
// File name: ExtremeC_examples_chapter2_1.c
// Description: Contains the definition of the function 'avg'


# 1 "./ExtremeC_examples_chapter2_1.h" 1
// File name: ExtremeC_examples_chapter2_1.h
// Description: Contains the declaration needed
//              for the 'avg' function




typedef enum {
  NONE,
  NORMAL,
  SQUARED
} average_type_t;

// Function declaration
double avg(int*, int, average_type_t);


# 5 "ExtremeC_examples_chapter2_1.c" 2

double avg(int* array, int length, average_type_t type) {
  if (length <= 0 || type == NONE) {
    return 0;
  }
  double sum = 0.0;
  for (int i = 0; i < length; i++) {
    if (type == NORMAL) {
      sum += array[i];
    } else if (type == SQUARED) {
      sum += array[i] * array[i];
    }
  }
  return sum / length;
}
revenantonthemission@MacBook-Pro-2 PS % 
```

## 전처리의 결과물

전처리의 결과로 만들어진 컴파일 단위(변환 단위)는 `.c` 확장자를 가지는 소스 파일과 달리 `.i` 확장자를 가진다. 따라서 `.i` 확장자를 가진다는 것은 전처리 과정을 이미 마쳤다는 뜻이며, 따라서 곧바로 컴파일 단계로 보내야 한다. 이런 파일을 전처리기로 보낼 경우, 컴파일러가 이미 파일이 전처리되었다는 경고 메시지를 보낸다.

```text
revenantonthemission@MacBook-Pro-2 PS % clang -E ExtremeC_examples_chapter2_1.c > ex2_1.i
revenantonthemission@MacBook-Pro-2 PS % clang -E ex2_1.i
clang: warning: ex2_1.i: previously preprocessed input [-Wunused-command-line-argument]
revenantonthemission@MacBook-Pro-2 PS % 
```

---

## 참고 자료 & 더보기

### 참고 자료

+ Amini, K. (2022). *전문가를 위한 C* (박지윤, Trans.; 1st ed.). 한빛미디어.
+ [GNU C Preprocessor의 내부 구조를 설명하는 공식 문서](http://www.chiark.greenend.org.uk/doc/cpp-4.3-doc/cppinternals.html)
+ [GNU C Preprocessor에 대한 개요](https://gcc.gnu.org/onlinedocs/gcc-2.95.3/cpp_1.html)
+ [C 전처리기에 대한 위키피디아 페이지](https://en.wikipedia.org/wiki/C_preprocessor)
+ [The Preprocessor: Everything You Need to Know and More! - Brian Ruth - CppCon 2021](https://www.youtube.com/watch?v=6KNdGnUiRBM)
+ [C++ Reference - Phases of Translation](https://en.cppreference.com/w/c/language/translation_phases)
+ [C++ Reference - Preprocessor](https://en.cppreference.com/w/c/preprocessor)
+ [C++ Reference - Identifier](https://en.cppreference.com/w/c/language/identifier)
+ [What is a vertical tab?](https://stackoverflow.com/questions/3380538/what-is-a-vertical-tab)
+ [What are carriage return, line feed, and form feed?](https://stackoverflow.com/questions/3091524/what-are-carriage-return-linefeed-and-form-feed)

### 더보기(추후 변경될 수 있습니다.)

+ [[ProgrammingLanguage/C/PreprocessingDirective\|전처리기 지시자]]