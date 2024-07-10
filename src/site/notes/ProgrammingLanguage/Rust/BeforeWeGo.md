---
{"dg-publish":true,"permalink":"/programming-language/rust/before-we-go/","tags":["Rust","프로그래밍언어"],"created":"2024-04-26T11:33:10.588+09:00","updated":"2024-07-10T16:21:37.014+09:00"}
---


# 사전 준비

> 본격적으로 러스트를 시작하기 전에 우선 개발 환경을 설정해 봅시다.

---

## rustup

`rustup`은 러스트 버전 및 러스트 관련 도구를 관리하는 프로그램이다. 러스트 컴파일러는 이 안에 있다.

```bash
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

macOS 기준으로 이 명령을 그대로 터미널에 입력하면 `rustup`이 설치된다. 다음과 같은 화면이 나오면 설치에 성공한 것이다.

![rustupInstall.png](/img/user/ProgrammingLanguage/Rust/rustupInstall.png)

러스트를 최신 버전으로 업데이트하려면 터미널에 `rustup update`를 입력하면 된다.

![rustUpdate.png](/img/user/ProgrammingLanguage/Rust/rustUpdate.png)

러스트를 삭제하고 싶다면 터미널에 `rustup self uninstall`을 입력하면 된다. 

![rustupUninstall.png](/img/user/ProgrammingLanguage/Rust/rustupUninstall.png)

## cargo

카고*Cargo*는 코드 빌드, 외부 라이브러리, 혹은 라이브러리를 제작할 때 편의를 봐주는 러스트 빌드 시스템 및 패키지 관리자다. 카고 또한 `rustup`에 포함되어 있으며, 러스트로 작성된 프로젝트는 대부분 카고를 사용하고 있기 때문에 러스트와 함께 카고를 배우는 편이 여러모로 유익해 보인다.

## rust-analyzer

터미널에서 커맨드 라인만으로 러스트 개발을 할 수 있지만, 효율적인 개발을 위해 언어를 불문하고 통합 개발 환경(IDE)을 사용하는 것이 일반적이다. 러스트에는 코드 자동 완성, 간편한 레퍼런스, 코드 분석 등을 지원하는 공식적인 유틸리티 플러그인 `rust-analyzer`가 있고, 여기서는 내가 사용하고 있는 Visual Studio Code(이하 VSC)를 기준으로 `rust-analyzer`를 설치하고 사용할 것이다.

![rustAnalyzerMarket.png](/img/user/ProgrammingLanguage/Rust/rustAnalyzerMarket.png)

설치하고 나서 카고로 만든 프로젝트를 열면 기본적인 개발 환경이 완성된다.

![rust-analyzer.png](/img/user/ProgrammingLanguage/Rust/rust-analyzer.png)


## 추가 도구들

러스트와 함께 설치되는 여러가지 보조 프로그램들이 있는데, 이들은 더 효율적이고 안전한 코드를 작성하는 데 도움을 줄 것이다. 이후의 모든 글은 명시하지 않는 한 카고로 만든 프로젝트를 대상으로 할 것이며, 다른 환경에서의 사용과 같은 본격적인 사용 방법은 공식 가이드라인을 참고하기를 바란다.

### rustfmt

`rustfmt`는 지정한 양식에 맞게 알아서 코드를 정리해주는 프로그램이다. 컴파일을 하는 것은 아니지만, 띄어쓰기나 줄바꿈, 혹은 괄호 위치 등을 조정해 코드를 더욱 보기 좋게 정리해준다.

```bash
rustup component add rustfmt
```

이 코드를 통해 `rustfmt`와 `cargo-fmt`라는 두 개의 프로그램이 추가되는데, 기능은 동일하지만 사용 방법이 다르다. 특정 파일을 고치려면 `rustfmt [파일 이름]`[^1], 전체 프로젝트를 고치려면 프로젝트의 최상단 디렉토리에서 `cargo fmt`를 실행하면 된다.

[^1] : `[파일 이름]`에 `rustfmt`의 대상이 될 파일의 이름을 넣으면 된다.

### rustfix

`rustfix`는 러스트 컴파일러에서 발생하는 경고를 자동으로 고쳐주는 프로그램으로, 러스트와 함께 설치된다. 컴파일러에서는 경고와 함께 권장사항을 같이 표시하는데, `cargo fix`를 통해 컴파일러의 권장사항을 자동으로 적용시킬 수 있다.

### clippy

`clippy`는 러스트 코드를 분석하면서 발생하는 일반적이고 사소한 오류들을 알려주는 프로그램이다. 설치를 위해서는 아래와 같이 `rustup`을 이용하면 된다.

```bash
rustup component add clippy
```

프로젝트에서 사용하기 위해서는 프로젝트의 최상단 디렉토리에서 `cargo clippy`를 실행하면 된다. 이 정도면 러스트를 배울 준비가 됐다고 생각하고 본격적으로 러스트에 대해 알아볼 것이다.

---

## 참고 자료 & 더보기

### 참고 자료
+ [The Rust Programming Language(한국어판), 1장](https://doc.rust-kr.org/ch01-00-getting-started.html)
+ [The Rust Programming Language(한국어판), 부록 D - 유용한 개발 도구](https://doc.rust-kr.org/appendix-04-useful-development-tools.html)

### 더보기
+ [rustfmt 공식 레포지토리](https://github.com/rust-lang/rustfmt): `rustfmt`를 본격적으로 사용하는 방법이 소개되어 있다.
+ [clippy 공식 레포지토리](https://github.com/rust-lang/rust-clippy): `clippy`를 본격적으로 사용하는 방법이 소개되어 있다.