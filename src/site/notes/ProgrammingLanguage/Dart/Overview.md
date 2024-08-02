---
{"dg-publish":true,"tags":["Dart","Flutter","프로그래밍언어"],"permalink":"/ProgrammingLanguage/Dart/Overview/","dgPassFrontmatter":true,"created":"2024-05-29T10:37:18.456+09:00","updated":"2024-08-02T16:25:03.435+09:00"}
---


# 기본 구조

> 이번 글에서는 Dart 프로그래밍 환경을 설정하는 법과 함께 Dart로 작성된 가장 기본적인 프로그램에 대해 알아봅니다.

---

## 미지와의 조우

Dart로 프로그램을 작성하기 위해서는 우선 개발 환경을 만들어야 한다. 웹에서 진행하는 간편한 방법이 있고, SDK(Software Development Kit)를 설치하는 덜 간편한 방법이 있다.

### DartPad

[DartPad](https://dartpad.dev)는 Dart 코딩을 시작하기 위한 가장 간편한 방법이다. 링크를 누르면 이런 화면이 나타난다. 왼쪽에는 프로그램의 코드가 있고, 오른쪽에는 왼쪽 코드의 실행 결과가 나타난다.
![DartPad1](/img/user/ProgrammingLanguage/Dart/DartPad1.png)
왼쪽 코드의 `Run`버튼을 누르면 코드가 실행되고, 오른쪽에 실행 결과가 출력된다. 그 외에 `New`버튼은 새로운 프로젝트를 만들어주고, `Samples`버튼을 통해 여러가지 예시 코드를 확인할 수 있다.
![DartPad2](/img/user/ProgrammingLanguage/Dart/DartPad2.png)

### SDK 설치

코딩의 차원을 넘어서 Dart를 조금 더 깊게 다루고 싶다면 [SDK를 설치](https://dart.dev/get-dart)해야 한다. 여러가지 옵션 중 개인적으로는 Dart를 배우는 이유라고 할 수 있는 [Flutter의 설치](https://docs.flutter.dev/get-started/install)를 추천한다. Dart SDK가 Flutter에 포함되어 있다.
![SDK1](/img/user/ProgrammingLanguage/Dart/SDK1.png)

이렇게 터미널을 통해 버전을 확인할 수 있다면 설치가 완료된 것이다.
![SDK2](/img/user/ProgrammingLanguage/Dart/SDK2.png)

#### Visual Studio Code 확장 설치

하지만 SDK를 설치하는 경우 보통 여기서 끝나지 않는다. 개발 환경을 설정하기 위해 기존의 개발환경과 연계하는 것이 일반적이다. 여기서는 내가 사용하고 있는 Visual Studio Code(이하 VSC)의 확장을 통해 개발 환경을 설정할 것이다.
![Extension](/img/user/ProgrammingLanguage/Dart/Extension.png)
마켓플레이스에서 Dart나 Flutter를 검색하면 바로 만날 수 있고, SDK와 마찬가지로 Flutter 확장이 Dart 확장을 포함하고 있기 때문에 Flutter 확장의 설치를 추천한다.

확장을 설치하고 나면 `> dart`를 통해 간단하게 명령을 실행할 수 있는데, 아래 스크린샷처럼 Dart: New Project를 실행하면 새로운 프로젝트를 만들 수 있다.
![Start1](/img/user/ProgrammingLanguage/Dart/Start1.png)

여러가지 템플릿이 존재하는데, 여기서는 콘솔 애플리케이션을 선택한다.
![Start2](/img/user/ProgrammingLanguage/Dart/Start2.png)

로컬에 생성하기 때문에 어떤 폴더에 프로젝트를 만들 것인지 정해야 한다.
![Start3](/img/user/ProgrammingLanguage/Dart/Start3.png)
폴더를 정하고 프로젝트의 이름을 설정하고 나면 새롭게 생성된 Dart 프로젝트를 확인할 수 있다.

![Start4](/img/user/ProgrammingLanguage/Dart/Start4.png)
![Welcome1](/img/user/ProgrammingLanguage/Dart/Welcome1.png)
조금 더 확대해보면 이렇게 되어 있다.
![Welcome2](/img/user/ProgrammingLanguage/Dart/Welcome2.png)
이제 가장 기본적인 Dart 프로그램을 분석하기 위한 모든 사전 작업이 끝났다.

## 프로젝트 분석하기

디렉토리는 이렇게 구성되어 있다. 핵심이 되는 파일들은 하이라이트 처리되어 있다.
+ dart_application_1
	+ .dart_tool
		+ package_config.json
	+ bin
		+ ==dart_application_1.dart==
	+ lib
		+ ==dart_application_1.dart==
	+ test
		+ ==dart_application_1_test.dart==
	+ .gitignore
	+ ==analysis_options.yaml==
	+ CHANGELOG.md
	+ ==pubspec.lock==
	+ ==pubspec.yaml==
	+ README.md

### 주요 코드 분석하기

본격적으로 분석에 들어가기 전에 README.md의 내용을 통해 대략적인 구조를 파악할 수 있다.

	A sample command-line application with an entrypoint in `bin/`, library code in `lib/`, and example unit test in `test/`.

모든 Dart 프로그램의 진입점은 C와 마찬가지로 `main()` 함수다. 이 함수는 명시적으로 값을 반환하지 않기 때문에 `void` 반환 타입을 가지며, 시작 전에 입력되는 인자들을 위한 `List<String>`형 매개변수 `arguments`를 가진다.

```Dart
//dart_application_1/bin/dart_application_1.dart
import 'package:dart_application_1/dart_application_1.dart' as dart_application_1;

void main(List<String> arguments) {
	print('Hello world: ${dart_application_1.calculate()}!');
}
```

`import`는 파이썬의 `import`와 비슷하다.[^1] 라이브러리를 가져오는 역할을 하는데, 여기서 가져오는 라이브러리는 `lib` 폴더의 `dart_application_1.dart`에 정의되어 있다. `as`를 추가하면 뒤에 오는 이름으로 라이브러리를 마치 클래스*class*처럼 사용할 수 있다.

```dart
//dart_application_1/lib/dart_application_1.dart
int calculate() {
	return 6 * 7;
}
```

C의 문법을 알고 있다면 익숙할 형태의 함수다. 이 함수는 정수형 자료 `int`를 반환하는 `calculate()` 함수며, `6 * 7` (=42)을 반환한다.

`main()`함수에 진입해서 실행하는 코드는 `print()`문으로, Dart의 출력문이다. Dart에는 ==String Interpolation==이라는 기능이 있는데, C에서 `printf()`를 사용하는 것과 비슷하게 문자열 내에서 특정 변수가 필요한 곳에 다음과 같이 변수를 투입하는 기능이다.

```dart
var name = 'nico';
var age = 10;
var greeting = "Hello everyone, my name is $name and I'm ${age + 2} years old."
```

위쪽 코드와 아래쪽 코드 모두 `$`과 `{}`를 통해 정수를 문자열 안에 집어넣었다는 것을 확인할 수 있고, 앞서 라이브러리에 정의된 `calculate()`함수의 호출을 통해 Dart에서 라이브러리의 함수를 어떻게 활용하는지 알 수 있다.

```dart
//dart_application_1/bin/dart_application_1.dart
print('Hello world: ${dart_application_1.calculate()}!');
```

### `test` 폴더의 존재의의

이렇게 프로그램의 실행 흐름에 따라 분석하는 과정은 끝이 났다. 그럼에도 불구하고, 프로젝트에 남아있는 Dart 소스 파일이 있는데 바로 `test` 폴더에 있는 파일이다. 이 폴더 아래에 있는 파일은 정식 배포 이전에 테스트를 하기 위한 파일이며, Dart에는 다양한 스케일의 테스트를 지원하는 다양한 라이브러리가 있다. 자세한 내용은 [Dart Testing 페이지](https://dart.dev/guides/testing)를 참고하면 될 것이다.

### 코드 분석

코드를 실행하기 전에 분석*static analysis*하는 과정을 통해 오작동을 예방하고 엄격한기준에 맞는 코드를 작성할 수 있다. `analysis_options.yaml`은 바로 이 코드 분석의 설정에 해당하는 파일로, 이 파일을 통해 작성자가 원하는 수준으로 코드를 분석할 수 있다.

기본 프로젝트에서 `analysis_options.yaml`은 기본 옵션 만을 포함하고 있다. 자세한 내용은 이 파일의 주석이나 [링크](https://dart.dev/tools/analysis)를 참고하면 될 것이다.
```yaml
# analysis_options.yaml
include: package:lints/recommended.yaml
```

### pubspec.yaml

`pubspec.yaml`은 프로젝트의 메타데이터*metadata*를 기록하는 파일이다. 여기서 프로젝트의 메타데이터라는 것은, 프로젝트의 이름과 개요, 버전, 그리고 SDK와 해당 프로젝트가 사용하고 있는 각종 라이브러리와 패키지를 포괄하는 개발 환경이다.

### pubspec.lock

프로젝트의 의존성*dependency*은 해당 프로젝트가 어떤 라이브러리를 사용하고 있는지를 나타낸다. 프로젝트의 실행이 라이브러리에 의존한다는 뜻에서 의존성이며, 소스 코드에 직접적으로 명시된 라이브러리 및 패키지의 사용에 대해서는 `pubspec.yaml`에서 확인할 수 있고, `pubspec.lock`은 라이브러리와 패키지의 모든 의존성을 기록한다. 이 파일은 `pub get`, `pub upgrade`, `pub downgrade`등의 터미널 명령어를 통해 Dart의 패키지 관리자인 [`pub`](https://pub.dev)가 자동으로 만들어준다.

## Pub, Dart의 패키지 관리자

### 라이브러리와 패키지

Dart에서 소스 코드 외부에 존재하는 코드를 끌어다 쓰기 위해서는 `import`를 통해 라이브러리*library*[^2]나 패키지*package*[^3]를 끌어다 써야 한다.[^4]

그 중에서 [SDK](https://api.dart.dev/stable/3.4.1/index.html)에서 기본으로 제공하는 핵심 라이브러리는 구문에서 다음과 같이 구분된다:[^5]
```dart
//핵심 라이브러리/패키지
import 'dart:math';

//그 외
import 'package:bloc'
```

핵심 라이브러리가 아닌 나머지 모든 라이브러리와 패키지는 Dart의 패키지 관리자인 [pub](https://pub.dev)에서 관리한다.

---

## 참고자료 & 더보기

+ [Dart 공식 API 문서](https://api.dart.dev/stable/3.4.1/index.html)
+ [Dart 공식 가이드](https://dart.dev/language)
+ [Dart 개요](https://dart.dev/overview)
+ [Customizing static analysis](https://dart.dev/tools/analysis)
+ [pubspec 파일](https://dart.dev/tools/pub/pubspec)
+ [main 함수](https://dart.dev/language/functions#the-main-function)
+ [패키지 가이드](https://dart.dev/guides/packages)
+ [pub.dev 패키지 홈페이지](https://pub.dev)

[^1]: 몇가지 프로그래밍 언어에 익숙해지고 나면 어지간한 프로그래밍 언어는 쉽게 이해할 수 있다.
[^2]: 재사용 가능한 코드의 모음으로, 변수, 함수, 클래스 등을 포함할 수 있다. 단일 Dart 파일 또는 여러 파일로 구성될 수 있다. 주로 단일 프로젝트 내에서 코드를 재사용하기 위해 사용된다.
[^3]: 하나 이상의 라이브러리를 포함하는 디렉토리로, 다른 개발자들과 공유하거나 프로젝트에서 재사용할 수 있도록 구성된 코드 묶음이다. 여러 프로젝트에서 재사용할 수 있도록 설계되었으며, Dart 생태계 내에서 공유되고 배포된다.
[^4]: 예외적으로 핵심적인 기능들이 모여 있는 `dart:core` 라이브러리는 자동으로 `import`된 것으로 간주하기 때문에 `import`를 사용하지 않는다.
[^5]: 예외적으로 `package:web`은 `dart:`로 시작하는 모든 웹 라이브러리를 대체하게 되면서 핵심 라이브러리 중 하나가 되었다.