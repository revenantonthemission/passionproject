---
{"dg-publish":true,"tags":["Rust","프로그래밍언어"],"permalink":"/ProgrammingLanguage/Rust/Enumerations/","dgPassFrontmatter":true,"created":"2024-08-01T01:32:10.000+09:00","updated":"2024-08-01T01:32:10.000+09:00"}
---



# 열거형

열거형*enumeration*은 여러 타입 중 하나를 선택할 수 있는 방법입니다. 이번 글에서는 러스트의 열거형에 대해 알아봅니다.

---

## 열거형 정의하기

열거형은 여러 타입을 하나로 묶는다는 점에서는 구조체*struct*와 비슷하지만, 열거형이 여러 타입을 묶는 방법은 구조체의 그것과 다릅니다. 열거형을 구성하는 각 타입을 배리언트*variant*라고 부르며, 이 배리언트 중 단 하나만 열거형의 값이 될 수 있다.

열거형을 정의할 때 사용하는 키워드는 `enum`이며, 배리언트는 중괄호`{}` 안에 쉼표`,`로 구분한다.

```rust
enum IpAddrKind {
    V4,
    V6,
}
```

배리언트 안에 데이터를 직접 넣을 수도 있는데, 배리언트 옆에 소괄호`()`를 붙이고 그 안에 타입을 넣어 선언하게 되면 각 배리언트의 이름은 해당 열거형 인스턴스의 생성자 함수와 같은 기능을 한다. 배리언트에는 어떤 종류의 데이터라도 넣을 수 있다.

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```

마지막으로, 구조체처럼 `impl` 블록에서 열거형에 대한 메서드를 정의할 수 있다.

```rust
impl Message {
    fn call(&self) {
        // 메서드 본문이 여기 정의될 것입니다
    }
}
```

## 열거형 사용하기

열거형을 사용할 때는 열거형에 대한 인스턴스를 만들어야 하는데, 이 인스턴스는 열거형 전체가 아닌 배리언트에 대한 인스턴스다. 

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```

두 변수의 타입은 `IpAddrKind`이지만, 두 변수는 배리언트에 대한 인스턴스이기 때문에 열거형의 이름은 배리언트가 속하는 네임스페이스*namespace*가 되어 이중 콜론`::`으로 전체 이름을 완성해야 한다. 만약 배리언트 안에 데이터가 있는 경우, 인스턴스를 생성할 때 위와 같이 정의 안에 포함된 타입의 인스턴스를 넣어야 한다.

```rust
let m = Message::Write(String::from("hello"));
m.call();
```

열거형에 대한 메서드를 사용하는 방법은 구조체에 대한 메서드를 사용하는 방법과 같다. `match`나 `if let` 등 열거형과 궁합이 좋은 제어 흐름 연산자는 이 글에서 다루는 범위를 넘어서며 자세한 내용은 [[ProgrammingLanguage/Rust/Control_Statement\|이 글]]에서 확인할 수 있다.

---

## 참고 자료 & 더보기

### 참고 자료
+ [The Rust Programming Language(한국어판), 6. 열거형과 패턴 매칭](https://doc.rust-kr.org/ch06-00-enums.html)

### 더보기
+ [[ProgrammingLanguage/Rust/Control_Statement#^37e07f\|match]]
+ [[ProgrammingLanguage/Rust/Control_Statement#^9f459b\|if let]]
+ [[ProgrammingLanguage/Rust/Struct\|구조체]]