---
{"dg-publish":true,"tags":["Rust","프로그래밍언어"],"permalink":"/ProgrammingLanguage/Rust/Control_Statement/","dgPassFrontmatter":true,"created":"2024-08-01T01:32:10.000+09:00","updated":"2024-08-01T01:32:10.000+09:00"}
---



# 프로그램의 흐름을 통제하기

> 러스트에는 조건에 따라 다른 코드를 실행하거나 특정 구간을 반복하는 등 프로그램의 실행을 통제하는 제어 흐름문이 있습니다. 이번 글에서는 러스트의 제어 흐름문 및 프로그램의 실행을 제어하기 위한 대표적인 문법과 자료형을 살펴봅니다.

---

## `if`

조건에 따라 다른 코드를 실행해야 할 때 `if`문을 통해 조건에 따른 코드의 분기*branch*를 간단하게 구현할 수 있다. 모든 `if` 표현식은 `if`라는 키워드에서 시작하고 그 뒤에 `bool` 타입을 반환하는 조건식이 온다.

```rust
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

`number < 5`처럼 `if` 뒤에 따라오는 조건식은 반드시 `bool` 타입을 반환해야 하는데, 러스트는 C처럼 다른 타입에서 자동으로 참/거짓을 판단하지 않기 때문에 조건식이 반환하는 값에 유의해야 한다. 만약 조건식이 `bool` 타입을 반환하지 않는 경우 에러가 발생한다.

```rust
fn main() {
    let number = 3;

    if number {
        println!("number was three");
    }
}
```
```bash
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
error[E0308]: mismatched types
 --> src/main.rs:4:8
  |
4 |     if number {
  |        ^^^^^^ expected `bool`, found integer

For more information about this error, try `rustc --explain E0308`.
error: could not compile `branches` due to previous error
```

### `else if` - `else`

추가로 `else if`를 통해 또 다른 분기를 추가할 수 있고, `else`를 통해 조건에 맞지 않는 나머지 케이스를 처리할 수 있다.

```rust
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```

### `if`는 표현식이다

한편 `if`는 [[ProgrammingLanguage/Rust/Function#^a14250\|표현식]]에 해당하기 때문에 값을 반환한다. 이를 활용해 변수를 생성하는 `let` 구문의 우변에 사용할 수 있다.

```rust
fn main() {
    let condition = true;
    let number = if condition { 5 } else { 6 };

    println!("The value of number is: {number}");
}
```

이렇게 조건에 따라 다른 값을 넣을 수도 있다. 다만 조건에 따라 타입이 달라지면 변수의 타입이 실행 이후, 즉 런타임*runtime*에 정의되어야 하기 때문에 실행 이전에 모든 검증이 끝나는 것을 지향하는 러스트에서는 이런 형태의 코드를 허용하지 않는다.

## `loop`

러스트에서 가장 단순한 반복문은 `loop`다. 이 키워드를 사용하면 따로 멈추라는 말이 없으면 여기에 연결된 코드 블록을 계속 반복해서 실행한다.

```rust
fn main() {
    loop {
        println!("again!");
    }
}
```

위와 같은 코드를 실행하면 직접 반복을 중지시키기 전까지 프로그램은 계속 터미널에 `again!`을 출력한다. 프로그램을 멈추게 하려면 조건이 포함된 반복문으로 고치거나 터미널에서 강제로 중지해야 한다.

```rust
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished dev [unoptimized + debuginfo] target(s) in 0.29s
     Running `target/debug/loops`
again!
again!
again!
again!
^Cagain!
```

터미널에서 실행 중인 프로그램을 강제로 중지시키는 방법은 보통 `Ctrl`키와 `C`키를 동시에 누르는 것이다.

### `break`

또 다른 방법은 조건과 함께 `break`라는 키워드를 추가하는 것이다. 반복문을 탈출하기 위한 조건을 `if`로 작성하고 `break`를 그 안에 넣으면 조건이 맞는 상황에서 반복문을 탈출할 수 있다. 

```rust
fn main() {
    let mut counter = 0;
	
    loop {
        counter += 1;
		
        if counter == 10 {
            break;
        }
        
        println!("The result is {counter}");
    };
	
}
```

위 코드를 실행하면 아래와 같이 `counter`가 10이 되었을 때 `loop` 구문을 탈출한다.

```bash
revenantonthemission@MacBook-Pro-3 loops % cargo run         
   Compiling loops v0.1.0 (/Users/revenantonthemission/rustprojects/loops)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.46s
     Running `target/debug/loops`
The result is 1
The result is 2
The result is 3
The result is 4
The result is 5
The result is 6
The result is 7
The result is 8
The result is 9
```

그런데 러스트에서는 `break`를 통해 반복문을 탈출하는 동시에 값을 반환할 수 있다. 그리고 이 값을 변수에 할당할 수 있다.

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {result}");
}
```

또한 반복문에 라벨*loop label*을 추가해 어떤 반복문을 탈출할지 명시할 수도 있다. `break` 뒤에 라벨을 붙이면 특정 반복문을 정하여 해당 반복문을 탈출할 수 있다.

```rust
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
}
```

## `while`

특정 조건을 만나고 나서 반복을 중지하는 이 패턴을 줄일 수 있는데, 러스트에서는 `while`을 통해 그 방법을 제공한다.

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{number}!");

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```

`while`문에서는 반복할 때마다 자동으로 뒤에 붙는 조건식을 확인하는데, 그 값이 `true`일 경우 코드 블록을 실행하고 다시 조건식으로 돌아오며 `false`인 경우 종료한다. 

## `for`

`for`는 다른 반복문인 `loop`이나 `while`에 비교했을 때 반복할 구간이 정해져 있을 때 사용하기 편하다. 러스트의 `for`구문은 반복할 어떤 구간과 함께 사용해야 하며, 배열이나 튜플 같은 다양한 타입이 이러한 구간을 제공한다.

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}
```

실제로 사용할 때는 위와 같이 반복할 구간 안*in*에 대하여*for* 코드를 수행한다는 의미로 `for`와 함께 `in`이라는 키워드를 사용한다.

## `match`

러스트에서 다양한 케이스를 제어하는 방법에는 `if`,  `else if`, `else`도 있지만, `match`를 사용하면 보다 더 많은 경우의 수를 포괄할 수 있다. `if`와 달리 `b ool` 이외의 타입도 조건으로 사용할 수 있고, 코드 자체도 더욱 잘 보인다. 이런 특성을 가지기 때문에 `match` 구문은 [[ProgrammingLanguage/Rust/Enumerations\|열거형]]*enumeration*과 함께 사용하는 것이 일반적이다.

### `match`와 열거형

열거형과 `match` 표현식을 같이 사용하는 대표적인 케이스는 아래와 같다.

```rust
enum UsState {
    Alabama,
    Alaska,
    // --생략--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}
```

`match`라는 키워드 뒤에는 평가의 대상이 되는 변수가 따라온다. 이 변수가 가지는 경우의 수에 따라 다른 작업을 하게 되는데, 중괄호`{}` 안에 변수가 가질 수 있는 커이스를 작성한 다음 화살표`=>` 뒤에 해당 케이스에서 작업할 내용을 적으면 된다. 위 코드의 경우 함수 내부에서 경우에 따라 다른 값을 반환하도록 했기 때문에 화살표 뒤에 반환할 값이 들어간 경우다.

만약 열거형의 어떤 배리언트*variant*가 값을 담는 경우, 해당 케이스를 담는  `match`에서는 해당 값을 매개변수로 하는 함수와 같은 코드를 작성할 수 있다.

```rust
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
}
```

### 예외 처리

`match` 구문을 작성할 때는 예외 처리에 유의해야 하는데, 모든 케이스를 다루어야 한다. 만약 다루지 않는 케이스가 있다면, 러스트에서는 컴파일 에러가 발생한다. 이를 위해 `else`처럼 특별히 다루지 않은 모든 케이스를 다룰 때 사용하는 것이 바로 `_`라는 키워드다. 어차피 마지막 패턴이 나머지 모든 케이스를 다루긴 하지만, 일반적으로 그 값을 사용할 필요가 없을 때 `_`를 사용한다.

```rust
let dice_roll = 9;
match dice_roll {
    3 => add_fancy_hat(),
    7 => remove_fancy_hat(),
    _ => reroll(),
}

fn add_fancy_hat() {}
fn remove_fancy_hat() {}
fn reroll() {}
```

또한 나머지 케이스를 다룰 때 아무것도 하고 싶지 않다면 소괄호`()`만 사용하면 된다. 이때 그냥 비워버리면 컴파일 에러가 발생하니 작성할 때 주의해야 한다.

```rust
let config_max = Some(3u8);
match config_max {
    Some(max) => println!("The maximum is configured to be {}", max),
    _ => (),
}
```

### `if let` : 더욱 간결하게!

`if let`은 `match`를 사용하는 특정 케이스를 요약해주는 일종의 부가적인 문법*syntax sugar*인데, 위와 같이 하나의 케이스만 특별히 다루고 나머지 케이스를 한꺼번에 다루는 상황에서 코드의 길이를 줄여주는 역할을 한다.

```rust
let config_max = Some(3u8);
if let Some(max) = config_max {
    println!("The maximum is configured to be {}", max);
}
```

만약 나머지 케이스에서도 해야 할 일이 있다면 `else`를 같이 사용할 수도 있다.

```rust
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

---

## 참고 자료 & 더보기

+ [The Rust Programming Language(한국어판), 3.5. 제어 흐름문](https://doc.rust-kr.org/ch03-05-control-flow.html)
+ [The Rust Programming Language(한국어판), 6. 열거형과 패턴 매칭](https://doc.rust-kr.org/ch06-00-enums.html)