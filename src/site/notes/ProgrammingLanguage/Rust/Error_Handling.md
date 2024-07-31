---
{"dg-publish":true,"tags":["Rust","프로그래밍언어"],"permalink":"/programming-language/rust/error-handling/","dgPassFrontmatter":true,"created":"2024-07-10T17:05:18.198+09:00","updated":"2024-07-31T16:46:23.147+09:00"}
---


# 오류를 처리하는 방법

러스트에서는 프로그램에서 발생하는 오류를 구분합니다. 다른 언어도 예외*exception* 처리를 지원하기 때문에 대부분 오류를 구분한다고 생각할 수도 있지만, 자바나 C++가 러스트처럼 오류의 종류에 따라 처리 방법까지 달라지지는 않습니다. 이번 글에서는 러스트에서 발생 가능한 오류를 구분하는 기준과 유형 별 오류 처리 방법에 대해 살펴봅니다.

---

## 복구할 수 있는가?

러스트로 만든 프로그램에서 발생할 수 있는 모든 오류를 분류하는 첫번째 기준은 복구다. 가장 먼저 코드 상에서 관리가 가능한 오류와 그렇지 않은 오류로 분류하고 또 그 안에서 세분화한다. 그래서 러스트에서 발생하는 모든 오류는 크게 복구 가능한*recoverable* 오류와 복구 불가능한*unrecoverable* 오류로 나눈다.

## 복구 가능한 오류 다루기

복구 가능한 오류는 오류지만 프로그램의 실행에 문제를 일으키지는 않는 오류다. 복구 가능한 오류의 대표적인 예시는 잘못된 입력을 하거나 필요한 파일을 찾지 못한 경우인데, 이런 에러에 대해서는 프로그램을 중단하는 일 없이 그저 문제를 보고하고 정확한 입력을 하거나 다른 파일을 찾게 하도록 사용자에게 재시도를 요구하면 된다. 이런 오류는 프로그램이 실행하고 나서 생기는 오류이기 때문에 일반적으로 컴파일 과정에서는 잡아내기 어렵다.

### 러스트에는 널(Null)이 없다?

그러나 러스트는 컴파일 단계에서 안전을 보장하기 위해 널리 사용되는 개념 하나를 없앴다. 자료형과 관련된 러스트의 독특한 특징 중 하나는 널*null*이 없다는 것이다. 널은 여러 컴퓨터 언어에서 사용하고 있는 개념 중 하나로 "값이 없음"을 나타내기 위한 값인데, 이런 널 개념이 존재하는 언어에서 변수의 상태는 널이거나 널이 아니다.

```CPP
//C++
int *ptr = nullptr;
```

널 개념이 있으면 위와 같이 값이 없는 상태를 구현하기 쉽지만, 널 값을 널이 아닌 값처럼 사용하려고 할 때나 그 반대 상황에 생기는 여러 종류의 에러에 대처해야 한다. 러스트에도 값의 유무를 표현할 수 있는 방법은 있지만, C++의 `NULL` 혹은 `nullptr`이나 Java의 `null`처럼 널 개념을 직접적으로 나타내는 키워드는 없다.

### <code>Option\<T\></code> : 자료형을 통한 에러 방지

대신 러스트에서 사용하는 대표적인 타입 중 하나는 `Option<T>`다[^1].  이 타입은 널 개념을 대신하여 값이 있거나 없을 수 있는 상황 전체를 포괄하는 타입이다. 이 타입이 다음과 같이 [[ProgrammingLanguage/Rust/Enumerations\|열거형]]으로 정의되어 있다.

```rust
enum Option<T> {

    None,    //값이 없는 상태
    Some(T), //값이 있는 상태
    
}
```


[^1]: 여기서 `<T>`는 이 타입이 제네릭*generic* 타입이라는 것을 의미한다. `T`에는 어떤 타입이든 들어갈 수 있다.

어떤 값이 있거나 없을 수 있는 상태를 하나의 열거형 타입으로 정해놓은 덕분에 `Option<T>`타입은 **어떤 방법을 쓰더라도 유효한 값으로 사용할 수 없다.** 대신 `Option<T>`를 유효한 타입으로 바꾸는 과정이 반드시 코드에 존재해야 하는데, 이 과정은 명시적으로 `Option<T>`가 가질 수 있는 모든 케이스, 즉 값이 있거나 없는 상황을 모두 다루어야 하기 때문에 어떤 값이 없을 수 있는 상황에 대한 완벽한 대처가 보장되며, `Option<T>`가 아닌 모든 값은 널이 아니라고 확신할 수 있다.

열거형을 다루기에 좋은 표현식은 단연 `match` [[ProgrammingLanguage/Rust/Control_Statement#^37e07f\|표현식]]이다. `match`표현식을 통해서 `Option<T>`를 다루는 코드는 다음과 같은 형식을 지니고 있다.

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```

`match`표현식을 통해 가능한 모든 상황을 다루어야 하기 때문에 러스트로 프로그램을 작성할 때는 널 개념이 있는 다른 프로그래밍 언어로 작성된 프로그램에서 발생할 수 있는 오류를 안전하게 예방할 수 있다.

### `Result<T, E>` : 복구 가능한 에러 처리하기

앞서 살펴봤던 `Option<T>`는 **값이 있어야 하는데 없는 상황**에서 발생하는 오류를 예방한다. 값이 없다고 해서 그것이 곧 오류는 아니기 때문에, 러스트에서는 오류가 발생할 수 있는 상황 자체를 나타내는 또다른 열거형 타입인 `Result<T, E>`를 사용한다. 어떤 함수의 성공과 실패를 나타내는 두 개의 배리언트 `Ok`와 `Err`는 각자 맡은 상황에서 반환할 값[^2]의 타입인  `T`와 `E`를 내부에 지니고 있다.

[^2]: 함수가 의도대로 실행되었음을 의미하는 `Ok` 배리언트는 성공시 반환될 값의 타입인 `T`를 내부에 지니고 있고, 함수가 의도대로 실행되지 않았음을 의미하는 `Err` 배리언트는 실패시 반환될 에러의 타입인 `E`를 지니고 있다.

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

이 타입은 주로 함수가 반환하는 타입으로 사용되는데, 이 타입을 반환하는 함수는 성공 여부에 따라 반환되는 타입이 둘로 나뉘는 효과를 가지기 때문에 함수가 의도대로 작동하지 않는 상황을 편하게 대처할 수 있도록 만들어준다. 그렇기 때문에 `match` 표현식과 함께 사용하는 것이 일반적인 패턴이다. 아래와 같이 어떤 함수나 메서드의 실행 결과가 가질 수 있는 모든 경우를 `match` 표현식을 통해 통제하는 것이 에러를 다루는 러스트의 기초적인 방법이다.

```rust
use std::fs::File;

fn main() {
    let greeting_file_result = File::open("hello.txt");
	
    let greeting_file = match greeting_file_result {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}
```

`match`를 사용하는 것이 에러를 다루는 가장 기초적인 방법이긴 하지만, 코드가 길어지면서 알아보기 어려워지는 측면이 있어 러스트에서는 `Result<T, E>`에 존재하는 여러 메서드를 통해 코드의 길이를 줄여주고 가독성을 높여준다. 그 중 대표적인 것이 바로 `unwrap` 메서드와 `expect` 메서드다.

#### `unwrap`

`unwrap` 메서드는 `Result<T, E>` 인스턴스의 값이 `Ok`일 때 `Ok` 안의 값을 반환하고 `Err`일 때 복구 불가능한 에러를 발생시키는 `match` 구문과 같은 기능을 수행한다. 아래의 예시 코드는 바로 이전에 `Result<T, E>`를 설명하면서 사용한 예시 코드와 비슷한 기능을 수행한다.

```rust
use std::fs::File;

fn main() {
    let greeting_file = File::open("hello.txt").unwrap();
}
```

#### `expect`

`expect` 메서드는 `unwrap` 메서드에서 한 발 더 나아가 복구 불가능한 에러가 발생할 때 원하는 대로 에러 메시지를 설정할 수 있게 해준다. 이러한 특성 덕분에 `unwrap`보다 코드의 문제를 검출하기에 용이하다.

```rust
use std::fs::File;

fn main() {
    let greeting_file = File::open("hello.txt")
        .expect("hello.txt should be included in this project");
}
```

## 복구 불가능한 오류 다루기

한편 프로그램에서 복구할 수 없는 오류들이 있다. 복구 불가능한 오류의 대표적인 예시는 배열의 끝을 넘어가는 메모리 주소에 접근하려 하거나, 어떤 값이 유효하지 않을 때와 같은 상황인데, 이럴 때는 프로그램을 중단하도록 해야 한다.

### `panic!` 매크로

#### 패닉

러스트 프로그램에서 복구할 수 없는 오류가 발생하면, 패닉*panic*이 발생한다. 패닉이 발생하면 우선 프로그램은 패닉이 발생한 각 함수에서 시작해 호출 과정을 거꾸로 거슬러 올라가면서 데이터를 청소하는 되감기*unwinding* 작업을 시작한다[^3]. 이후 실패 메시지가 출력되고, 프로그램이 사용하고 있던 메모리를 정리하는 작업은 운영체제가 담당한다. 이러한 패닉을 명시적으로 호출하는 매크로가 바로 `panic!` 매크로인데, 실제로 사용할 때는 출력문과 패닉을 연이어 수행한다는 느낌으로 사용한다.

```rust
fn main() {
    panic!("crash and burn");
}
```

프로그램을 실행하면, 매크로 안에 지정해 둔 메시지가 출력되면서 패닉이 발생한다. 이때 패닉이 발생한 지점을 확인할 수 있다.

```bash
$ cargo run
   Compiling panic v0.1.0 (file:///projects/panic)
    Finished dev [unoptimized + debuginfo] target(s) in 0.25s
     Running `target/debug/panic`
thread 'main' panicked at 'crash and burn', src/main.rs:2:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

만약 패닉이 발생한 지점이 작성 중인 코드가 아닌 다른 코드[^4]라면, 패닉이 발생한 원인을 알기 위해서 더 자세한 분석이 필요하다.

[^3]: 상황에 따라 데이터 청소가 어려울 경우 프로그램이 데이터를 정리하지 않고 즉시 종료하는 그만두기*aborting* 작업이 대신 이루어진다.
[^4]: 이를테면 다른 사람이 작성한 코드를 가져왔는데 거기서 매크로를 호출하는 경우도 많다.

#### 백트레이스

이때 사용하는 것이 바로 백트레이스*backtrace*다. 패닉으로 발생한 실패 메시지의 마지막을 보면 `RUST_BACKTRACE=1`이라는 환경변수 설정을 통해 패닉에 이르기까지 함수가 호출된 과정을 역으로 보여주는 백트레이스를 실패 메시지와 함께 출력할 수 있다. 백트레이스를 출력해서 함수의 호출 과정을 확인하면, 어떤 코드가 어떻게 잘못되어 패닉에 이르게 되었는지 더욱 상세하게 파악할 수 있다.

```rust
   Compiling playground v0.0.1 (/playground)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.62s
     Running `target/debug/playground`
thread 'main' panicked at src/main.rs:2:5:
crash and burn
stack backtrace:
   0: rust_begin_unwind
             at /rustc/129f3b9964af4d4a709d1383930ade12dfe7c081/library/std/src/panicking.rs:652:5
   1: core::panicking::panic_fmt
             at /rustc/129f3b9964af4d4a709d1383930ade12dfe7c081/library/core/src/panicking.rs:72:14
   2: playground::main
             at ./src/main.rs:2:5
   3: core::ops::function::FnOnce::call_once
             at /rustc/129f3b9964af4d4a709d1383930ade12dfe7c081/library/core/src/ops/function.rs:250:5
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```

직전 섹션과 동일한 코드를 이번에는 백트레이스 환경변수를 1로 설정하고 실행한 결과다. 패닉하면서 발생하는 메시지 아래에 `stack backtrace:` 아래로 보이는 것이 바로 백트레이스다.

## 오류 전파하기

이번에는 **오류를 처리하는 지점**이라는 또다른 관점에서 오류를 살펴보자. 어떤 함수에서 발생하는 오류를 지금까지 봐왔던 것처럼 직접 처리할 수도 있지만, 이 오류를 직접 처리하지 않고 다른 함수에 넘길 수도 있다. 이를 에러 전파하기*propagating*라고 하며, 주로 호출되는 함수에서 사용한다.

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let username_file_result = File::open("hello.txt");

    let mut username_file = match username_file_result {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut username = String::new();

    match username_file.read_to_string(&mut username) {
        Ok(_) => Ok(username),
        Err(e) => Err(e),
    }
}
```

`Err(e)`라는 오류 자체를 반환하기 때문에 위 함수를 호출하는 쪽에서는 오류 자체를 값처럼 넘겨받고 그 오류가 무엇인지 해석한 다음 그에 따라 오류를 처리한다.

### `?` : 에러를 전파하기 위한 지름길

에러를 더 쉽게 전파하기 위해 러스트에서는 `?`라는 연산자를 제공한다. `Result` 값 뒤에 붙어 `Result`값이 `Ok`라면 `Ok` 안의 값을 돌려주고 `Err`라면 `Err`의 값을 반환한다. 이때 `?`연산자는 에러를 원하는 타입의 에러로 바꿔 준다.

`?` 연산자는 `if let`처럼 [[ProgrammingLanguage/Rust/Control_Statement#^9f459b\|특정 패턴]]의 `match` 표현식을 줄인 것이기 때문에 `Result`나 `Option`처럼 연산자와 호환되는 타입을 반환하는 함수에서만 사용할 수 있다.

---

## 참고 자료 & 더보기

+ [The Rust Programming Language(한국어판), 9. 에러 처리](https://doc.rust-kr.org/ch09-00-error-handling.html)
+ [[ProgrammingLanguage/Rust/Control_Statement\|프로그램의 흐름을 통제하기]]
+ [[ProgrammingLanguage/Rust/Enumerations\|열거형]]