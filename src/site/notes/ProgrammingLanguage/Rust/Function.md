---
{"dg-publish":true,"tags":["Rust","프로그래밍언어"],"permalink":"/ProgrammingLanguage/Rust/Function/","dgPassFrontmatter":true,"created":"2024-07-15T22:58:45.159+09:00","updated":"2024-07-22T23:27:43.845+09:00"}
---


# 함수

러스트에서 함수는 상당한 지분을 차지합니다. 세부적인 구분을 제외한다면 러스트 프로그램은 대부분 함수로 이루어져 있다고 봐도 과언이 아닙니다. 이번 글에서는 러스트에서 함수를 만들고 사용하는 다양한 방법에 대하여 알아봅니다.

---

## 함수 선언

러스트에서는 `fn` 뒤에 함수 이름과 괄호를 붙여서 함수를 정의하고, 중괄호`{}`를 통해 함수 본문의 시작과 끝을 정한다. 그리고 함수를 호출할 때는 함수 이름 뒤에 괄호를 사용한다. 이때 호출하는 쪽에서 함수를 찾을 수 있다면 함수가 정의된 위치는 상관없다.

```rust
fn main() { 
	println!("Hello, world!"); 
	another_function(); 
}

fn another_function() { 
	println!("Another function."); 
}
```

매개변수*parameter*가 있다면 괄호 안에 반드시 각 매개변수의 이름과 타입을 선언해야 한다. 함수의 이름과 매개변수에 해당하는 부분을 시그니처*signature* 혹은 함수 원형*function prototype*이라고 부르는데, 매개변수가 있는 함수를 호출할 때는 시그니처에 맞게 인자*argument*를 괄호 안에 넣어 주어야 한다.

```rust
fn main() {
    print_labeled_measurement(5, 'h');
}

fn print_labeled_measurement(value: i32, unit_label: char) {
    println!("The measurement is: {value}{unit_label}");
}
```

## `main` 함수 : 프로그램의 시작점

러스트로 만든 프로그램에서 다른 함수들과 조금 다른 지위를 가지는 함수가 있는데, 바로 `main` 함수다. 러스트의 프로그램은 주로 `main` 함수에서 시작해서 `main` 함수로 끝나는 구조를 가지고 있어서 `main` 함수는 프로그램의 실행 과정을 요약한 듯한 구조를 가질 때가 많다.

```rust
fn main() {
	println!("Hello, world!");
}
```

## 구문 ≠ 표현식
{ #a14250}


앞서 살펴본 함수들은 모두 반환값이 없는 함수다. 하지만 그렇다고 해서 러스트에서 함수가 값을 반환하지 않는다는 것은 아니며, 러스트도 다른 언어처럼 함수가 값을 반환할 수 있다. 다만 다른 언어와 차이를 보이는 지점은 바로 값을 반환하는 방식이다. `five`라는 함수를 보면 값을 반환하는 함수가 어떻게 작성되는지 한 눈에 파악할 수 있다. 

```rust
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {x}");
}
```

만약 반환해야 하는 값이 있다면, 함수를 선언할 때 중괄호 이전에 `->`와 함께 반환할 값의 타입을 명시해야 한다. 그리고 반환할 값은 대부분의 코드와 달리 세미콜론`;`을 붙이지 말아야 하는데, 러스트가 다른 언어와 달리 구문*statement*과 표현식*expression*을 엄격하게 구분하기 때문이다. 러스트에서 세미콜론`;`이 붙는 모든 코드는 구문이고, 그렇지 않은 코드는 표현식이다.

러스트에서 함수의 본문은 구문과 표현식의 나열로 구성되는데, 구문은 어떤 동작을 수행하고 값을 반환하지 않는 반면 표현식은 식의 결과값을 평가한다. 그렇기 때문에 `x = y = 6`과 같은 코드는 러스트에서 작동하지 않는다.

```rust
fn main() {
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {y}");
}
```

여기서 `let x = 3;`은 구문이고 `x + 1`은 표현식이다. 구문을 통해 `x`라는 변수에 3이라는 값을 평가하는 표현식 `3`이 있기 때문에 `x`에 3이 들어가는 것이고, `x + 1`을 평가하면 4가 되고 표현식은 값을 반환하기 때문에  `y`라는 변수에 4라는 값이 대입되는 것이다. 

## 메서드

러스트에서 함수를 사용하는 대표적인 상황 중 하나는 [[ProgrammingLanguage/Rust/Struct#^eed5bb\|메서드]]*method*라 불리는 구조체의 기능을 구현하는 상황이다. 엄밀히 따지자면 메서드는 함수와 유사하되 다른 개념이지만, 함수와 여러 공통점을 가지고 있기 때문에 실제로 코드를 작성하고 보는 입장에서는 구조체에 딸린 함수라고 봐도 무방하다.

## 클로저

메서드는 일반 함수와 형태 상 많은 공통점을 가지고 있지만, 러스트는 클로저*closure*라는 전혀 다른 형태의 함수도 사용하고 있다. 클로저란, 변수에 저장하거나 다른 함수에 인수로 전달할 수 있는 익명 함수다.

```rust
//unwrap_or_else를 사용하면 Result가 Ok일 때 Ok 안의 값을 반환하고, Err일 때 클로저 안의 코드를 호출한다.

let config = Config::build(&args).unwrap_or_else(|err| {

	//표준 에러 출력을 위한 eprintln! 매크로
	eprintln!("Problem parsing arguments: {err}");
	
	process::exit(1);
	
});
```

위에 주어진 예시 코드에서 `unwrap_or_else` 메서드 안에 클로저 표현식이 사용되었는데, 이 메서드를 호출한 인스턴스가 의도된 타입이 아닐 때 인자로 주어진 클로저를 실행하게 된다.

사실 클로저 표현식은 함수와 유사한데, `fn`과 소괄호`()`를 쓰지 않는 대신. `||` 사이에 매개변수가 들어가고 중괄호를 통해 함수 본문을 정의하면 된다. 그럼에도 불구하고 클로저가 함수와 동떨어져 보이는 것은 일반적으로 클로저를 작성할 때 매개변수와 반환 값의 타입을 명시하지 않고 때에 따라서는 중괄호`{}`도 생략하기 때문인데, 생략된 코드를 추가하면 다음과 같다.

```rust
let config = Config::build(&args).unwrap_or_else(|err: &str| {

	//표준 에러 출력을 위한 eprintln! 매크로
	eprintln!("Problem parsing arguments: {err}");
	process::exit(1);
	
});
```

이렇듯 클로저에서 사용되는 매개변수와 반환 값의 타입은 컴파일러가 알아서 추론하기 때문에, 같은 클로저를 여러 번 사용할 때 처음과 다른 타입의 인자를 넣으면 에러가 발생한다.

```rust
// closure-example/main.rs
let example_closure = |x| x;
let s = example_closure(String::from("hello"));
let n = example_closure(5);
```
```bash
$ cargo run
   Compiling closure-example v0.1.0 (file:///projects/closure-example)
error[E0308]: mismatched types
 --> src/main.rs:5:29
  |
5 |     let n = example_closure(5);
  |             --------------- ^- help: try using a conversion method: `.to_string()`
  |             |               |
  |             |               expected struct `String`, found integer
  |             arguments to this function are incorrect
  |
note: closure parameter defined here
 --> src/main.rs:2:28
  |
2 |     let example_closure = |x| x;
  |                            ^

For more information about this error, try `rustc --explain E0308`.
error: could not compile `closure-example` due to previous error

```


## 함수 포인터

비록 C처럼 포인터를 직접 다룰 수는 없지만, 러스트에서도 함수 포인터*function pointer*를 통해 일반 함수를 함수에 전달할 수 있다. 러스트에서는 `fn`을 자료형으로 사용할 경우 `fn`타입을 함수 포인터라고 부르며, 이 타입의 매개변수에 인자로서 함수를 전달할 수 있다. 함수 포인터를 매개변수로 활용할 때는 아래와 같이 시그니처만 적어준다.

```rust
fn add_one(x: i32) -> i32 {
    x + 1
}

fn do_twice(f: fn(i32) -> i32, arg: i32) -> i32 {
    f(arg) + f(arg)
}

fn main() {
    let answer = do_twice(add_one, 5);

    println!("The answer is: {}", answer);
}
```

이때 다른 타입처럼 `fn`을 반환 타입으로 사용할 수는 없다는 점을 조심해야 한다.

---

## 참고 자료 & 더보기

+ [The Rust Programming Language(한국어판), 1.2. Hello World!](https://doc.rust-kr.org/ch01-02-hello-world.html)
+ [The Rust Programming Language(한국어판), 3.3. 함수](https://doc.rust-kr.org/ch03-03-how-functions-work.html)
+ [The Rust Programming Language(한국어판), 13.1 클로저: 자신의 환경을 캡처하는 익명 함수](https://doc.rust-kr.org/ch13-01-closures.html)
+ [The Rust Programming Language(한국어판), 19.4. 고급 함수와 클로저](https://doc.rust-kr.org/ch19-05-advanced-functions-and-closures.html)